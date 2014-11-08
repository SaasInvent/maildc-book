#Local storage

All the client data will be stored locally using "local shared objects" LSO.

In order to achieve this, we will rely on Flex swf objects for persistent storage.

We will also use [LokiJS](http://lokijs.org/?utm_source=javascriptweekly#/) a fast, in-memory document-oriented data store for node.js and browser.

MailDC will load data from the LSO into the LokiJS data-store.

    loadJSON( serializedDB )
    
This method takes a json string and loads the database from it. Useful if you want to persist your database somewhere on the cloud and re-initialize it with an ajax call returning JSON.

>arguments	serializedDB: the JSON string to load into the db


Document class
--------------

A document is just a json object. Once saved the document gets an id and objType property equal to that of the collection the document belongs to.

Collection class
----------------

>insert( obj )

Each record in the database is a document. A document is created through the document() method of the collection class. You can take any object and store it in the collection. While like all nosql databases there's no rule on how documents should be structured it just is good practice to put documents with identical structures in the same collection. 
Note!: if you are trying to insert an object that has already a property 'id', then that property will be renamed to 'originalId', and id will become the lokijs id. 
For example:
childrenOfLoki.document({ name:'Sleipnir', legs: 8 }); 
or
childrenOfLoki.document([{ name:'Sleipnir', legs: 8 }, { name: 'Fenrir', legs: 4 }]);

arguments	obj: the document to be saved in the collection, or an array of objects
returns	document: the document with an automatically generated id.
update( obj )

Updating the document is done by simply updating the properties of a document as the collection only holds a reference to the original object. When you call update on an object though you force a resync of the indexes of the collection.

>arguments	obj: the document to be updated in the collection, or an array of objects
returns	document: the document itself.
remove( obj )
Removes a document from the collection.
arguments	obj: the document to be removed from the collection.







## The Flex SDK ##
First of all you'll need to download the [Flex SDK](http://www.adobe.com/devnet/flex/flex-sdk-download.html).


## The mxmlc compiler ##

The flash compiler can be found in the 
>/client/flashplugin 

directory.

The compiler script is in the same directory:



## Flex and JSON ##

The [following](http://blog.infrared5.com/2011/07/working-with-native-json-in-flash-player-11/) article by Tod Anderson is very helpful. The following is a slightly adapted version:

You can head over to the online docs and check out the JSON API for a deeper explanation, but the JSON class basically has two static methods:

    parse( text:String, reviver:Function = null ):Object
    stringify( value:Object, replacer:* = null, space:* = null ):String



JSON.stringify()

The JSON.stringify() method does essentially what you might expect: put object in, get string out. 
The replacer parameter can be supplied as a list of String or Number that correspond to the key-value pairs which you want to serialize to a JSON string. It can also be supplied as a Function delegate that will be invoked upon each key-value pair that allows you to handle the data directly in either modifying the value being generated or – in the case of returning undefined – removing it from serialization.

*Put in other terms, it is a map function!*

Here is a quick example of how you can assign a replacer delegate and remove a value from being serialized to the generated JSON string:

    var objStr:String = JSON.stringify( {
		  name:"Todd Anderson",
	      company:"Infrared5",
	      phone:15558576309
    }, deflate );

    trace( "JSON obj>String: " + objStr ); // >
    {"name":"Todd Anderson","company":"Infrared5"}


    protected function deflate( key:String, value:* ):*
    {
	   return ( key == "phone" ) ? undefined : value;
	}
	
That gives you a little peek at the potential that a replacer has when generating JSON, but you can imagine that it could be valuable for, say, hashing passwords and such on the client side before being sent over the wire.



JSON.parse()

Let’s take a look at the parse method. 
var obj:Object = JSON.parse( value );

However, there is a reviver parameter on the parse() method which can be of use if you wish to handle working with the key-value pairs during the parse operation of the value object. 

Again, we could call this our map function!

The signature of the Function supplied as the reviver should have two parameters defined – key and value – and should return the modified value in order to be stored in the object.

Here is a quick example of using the parse() method with a reviver that turns each string value to upper case:

	var objStr:String = JSON.stringify({name:"Todd Anderson", company:"Infrared5"});
	var obj:Object = JSON.parse( objStr, inflate );

	protected function inflate( key:String, value:* ):*
	{
		 return ( (typeof value) == "string" ) ? String(value).toUpperCase() : value;
	}


One thing to note here is that if you return undefined from the reviver delegate, you can actually remove the property and its value from the generated object, which is useful but also is something to look out for if things seem to be going wrong. Another thing to note here is that the last key-value pair passed through this reviver delegate is the actual object itself – keep that in mind when using a reviver and make sure to check that you are not modifying the whole object you are parsing.



Storing typed data in a shared object
-------------------------------------

[The Action Script Cookbook](http://www.oreilly.de/catalog/9780596529857/chapter/ch17.pdf) is a valuable source.

