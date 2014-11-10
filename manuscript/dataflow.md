#Data flow in browser

MailDC uses two browser data stores

 - in memory data store (LokiJS)
 - persistent data store (LSO)

This chapter describes the data flow between these two data stores.

## In memory data store ##

Once the web page page is ready ($.documentReady()), MailDC loads its email data from the persistent storage into memory.

We're probably not using LokiJS!!!!


Actionscript

EmailManager

has to manage emails and exposes the following functions:

 - getEmail(index:String);
 - storeEmail(email:String);



Three LSO's to manage emails

 - index
 - emails
 - folders



Here some example code

	var db = new loki('MailDCEmail');


    // create two example collections
    var users = db.addCollection('users', ['email'], true, false);
    var projects = db.addCollection('projects', ['name']);

    // show collections in db
    db.listCollections();

   
    
    // create six users
    var odin = users.insert( { name : 'odin', email: 'odin.soap@lokijs.org', age: 38 } );
    var thor = users.insert( { name : 'thor', email : 'thor.soap@lokijs.org', age: 25 } );
    var stan = users.insert( { name : 'stan', email : 'stan.soap@lokijs.org', age: 29 } );
    // we create a snapshot of the db here so that we can see the difference
    // between the current state of the db and after the json has been reloaded
    var json = db.serialize();


## Persistent data store ##

On every update or creation of data, the data in the LSO will be saved.

