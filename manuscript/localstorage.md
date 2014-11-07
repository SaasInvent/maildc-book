#Local storage

All the client data will be stored locally using "local shared objects" LSO.

In order to achieve this, we will rely on Flex swf objects.

## The Flex SDK ##

First of all you'll need to download the [Flex SDK](http://www.adobe.com/devnet/flex/flex-sdk-download.html).


## The mxmlc compiler ##

The flash compiler can be found in the 
>/client/flashplugin 

directory.

The compiler script is in the same directory:



## Flex and JSON ##

The [following](http://blog.infrared5.com/2011/07/working-with-native-json-in-flash-player-11/) article by Tod Anderson is very helpful:

You can head over to the online docs and check out the JSON API for a deeper explanation, but the JSON class basically has two static methods:

parse( text:String, reviver:Function = null ):Object
stringify( value:Object, replacer:* = null, space:* = null ):String
If coming from working with the JSON class from as3corelib, these will most likely look different than the methods you are used to. That’s alright, they do relatively the same thing and, depending on the level of control you want, it may just be a matter of the methods being named differently. I do want to go over what has been made available in these methods in moving toward the use of the native JSON class, however, as they can be quite interesting and possibly useful depending on the design of your application.

JSON.stringify()

The JSON.stringify() method does essentially what you may be familiar with as JSON.encode() from the as3corelib – put object in, get string out. However, the global JSON class of FP11 now has a couple more parameters that can be used to your advantage when turning that object into a JSON string.

The replacer parameter can be supplied as a list of String or Number that correspond to the key-value pairs which you want to serialize to a JSON string. It can also be supplied as a Function delegate that will be invoked upon each key-value pair that allows you to handle the data directly in either modifying the value being generated or – in the case of returning undefined – removing it from serialization.

Here is a quick example of how you can assign a replacer delegate and remove a value from being serialized to the generated JSON string:

>var objStr:String = JSON.stringify( {
		  >name:"Todd Anderson",
	      >company:"Infrared5",
	      >phone:15558576309
 >}, deflate );

trace( "JSON obj>String: " + objStr ); // >
// {"name":"Todd Anderson","company":"Infrared5"}
...
protected function deflate( key:String, value:* ):*
{
    return ( key == "phone" ) ? undefined : value;
}
That gives you a little peek at the potential that a replacer has when generating JSON, but you can imagine that it could be valuable for, say, hashing passwords and such on the client side before being sent over the wire.

The other parameter in JSON.stringify() – space – can be a String or Number and is used in prettifying the generated JSON string by providing whitespace and readability. Using the previous example, if we provided the value of 4 as the argument for spacer, the trace output would no longer be on one line and look more like this:

{
    "name": "Todd Anderson",
    "company": "Infrared5"
}
Pretty!

JSON.parse()

Let’s take a look at the parse method. Again, if coming from as3corelib JSON, it will probably just be a change of calling:

[FP 11 global: JSON]

var obj:Object = JSON.parse( value );
instead of:

[as3corelib: JSON]

var obj:Object = JSON.decode( value );
However, there is a reviver parameter on the parse() method which can be of use if you wish to handle working with the key-value pairs during the parse operation of the value object. The signature of the Function supplied as the reviver should have two parameters defined – key and value – and should return the modified value in order to be stored in the object.

Here is a quick example of using the parse() method with a reviver that turns each string value to upper case:

>var objStr:String = JSON.stringify({name:"Todd Anderson", company:"Infrared5"});
var obj:Object = JSON.parse( objStr, inflate );
...
protected function inflate( key:String, value:* ):*
{
    return ( (typeof value) == "string" ) ? String(value).toUpperCase() : value;
}


One thing to note here is that if you return undefined from the reviver delegate, you can actually remove the property and its value from the generated object, which is useful but also is something to look out for if things seem to be going wrong. Another thing to note here is that the last key-value pair passed through this reviver delegate is the actual object itself – keep that in mind when using a reviver and make sure to check that you are not modifying the whole object you are parsing.

GOING A STEP FURTHER

Now this new API is well and good and we could be on our way sending and receiving JSON data and punch our time card and grab a beer, but there may come a time where you actually want to have more of a workflow in streamlining the generation and parsing of this data using real model objects in your project rather than generic Objects.

Now it is true that as long as the property values held on a custom object are those that are supported in the JSON format – string, number, boolean, array and null – that you could supply the custom object to the JSON.stringify() method and it will properly generate the JSON object for you. 

Going the other way – parsing – however, is not going to inherently create or write to that custom object that you may have previously serialized; it will just generate a generic object. Of course there is nothing stopping you from traversing the properties on that object and setting them on a new custom instance, but this is an opportunity in which we might want to think about how we can go about creating a design in which we can easily map back to custom objects. Things I like to think about and bore people with. Don’t fall asleep on me.

To get an idea of what i am talking about, say we have a Person class which serves as a model for a person in the context of our application. It has your basic properties on it which are standard value types – like name, company, etc from previous examples. We can certainly generate a JSON string and pass that around like so:

>var person:Person = new Person();
person.name = "Todd Anderson";
person.company = "Infrared5";
var objStr:String = JSON.stringify( person );
trace( JSON.parse( objStr ) ); // [object Object]

Now, in our perfect world, that would trace [object Person], wherein the JSON parser was knowledgable to map the object to a Person. There is that reviver parameter on JSON.parse() but that won’t help us much in mapping back to a custom instance as it only handles the key-value pairs and not the object as a whole.

One solution is to serialize the classname down with the generated JSON object, so that when it is received on the client, we can inflate a custom instance based on the associated class. So, if you can imagine, the models you will use in your application all extend a base model that handles setting this classname that is accessible and writable to a JSON object, eg:

package
{
    import flash.utils.describeType;
    import flash.utils.getQualifiedClassName;

    public class Model
    {
        protected var _clazz:String;
        private static var _description:Object = {};

        public function Model()
        {
            _clazz = getQualifiedClassName( this );
        }

        public function get model():String
        {
            return _clazz;
        }

        [Transient]
        public function get description():Vector.
        {
            if( Model._description[_clazz] == null )
            {
                var desc:Vector.<String> = Model._description[_clazz] = new Vector.<String>();
                var xml:XML = describeType( this );
                var variables:XMLList = xml..variable;
                var variable:XML;
                // Run through public variable declarations.
                for each( variable in variables )
                {
                    desc[description.length] = variable.@name;
                }
                // Run through accessors.
                var accessors:XMLList = xml..accessor;
                for each( variable in accessors )
                {
                    // Only include if readwrite.
                    if( variable.@access == "readwrite" )
                        desc[description.length] = variable.@name;
                }

            }
            return Model._description[_clazz];
        }
    }
}
In this base model class, there is a model accessor which is the qualified classname of the subclass. It also exposes a description accessor which allows easy access to the list of read-write properties on the subclass. It is marked as transient to denote that it should not be considered enumerable when generating the associated JSON object.

Now that we have our base model, any subclasses will be serialized with a model property representing the qualified clas name which can be used to inflate the custom object upon parse. Modifying the Person class used in a previous example, the class now looks like this:

package
{
    public class Person extends Model
    {
        public var name:String;
        public var company:String;

        public function Person()
        {
            super();
        }
    }
}
We just have to remember to call super() in the constructor or ensure that the _clazz property value is that of the subclass. From here, all that is left is using that model property to instantiate a new instance and fill the available property values from the parsed JSON object received. Here is a quick working example:

var person:Person = new Person();
person.name = "Todd Anderson";
person.company = "Infrared5";

// To JSON.
var objStr:String = JSON.stringify( person );

// Create new Person from JSON.
var parsedPerson:Person = parseToModel( objStr ) as Person;
...

public function parseToModel( value:String ):Model
{
    var obj:Object = JSON.parse( value );
    var model:Model = new ( getDefinitionByName( obj.model ) as Class ) as Model;
    var description:Vector.<String> = model.description;

    // Fill new Model
    var property:String;
    for( property in obj )
    {
        if( description.indexOf( property ) != -1 )
        {
            model[property] = obj[property];
        }
    }
    return model;
}
In the parseToModel() method in this example, we see how the read-only model property is passed in the flash.utils.getDefinitionByName() function to inflate a new instance of the associated model – Person – that was previously serialized to a JSON object.

One thing to remember is that a reference to the class in which you want to use to inflate a new instance of must be compiled into the SWF, otherwise you will get Runtime Errors. There are a couple ways in which you can ensure that the reference is included, and I will leave that up to you to explore.















> Written with [StackEdit](https://stackedit.io/).