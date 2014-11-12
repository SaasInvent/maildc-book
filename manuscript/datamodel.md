#Data Model

The MailDC data model mimics the classic functionality of an email client

**MailDC should be able to handle emails that you store in folders.**

In an OOP approach we should create two classes:

 - Folder
 - Email

These two classes should be implemented in CoffeeScript and in ActionScript.

The Security aspects will only be handled in the CoffeeScript class based on the Forge library.

## Email Data ##

Our Email Object should have the following properties:

 - uuid : String : uuid.v1();
 - date : new Date()
 - folder : String : uuid.v1();
 - from: String
 - to: Array of objects
 - Ccc : Array of objects
 - Bcc : Array of objects
 - AttachFiles : array of objects (list of url links)
 - subject : String
 - 

## ArrayCollections ##

MailDC will be using the following ArrayCollections and LSOs : 

 - inbox
 - draft
 - sent
 - template
 - spam
 - trash
 - current
 - archive

There will be two tree structures:

 - currentsubfolders
 - archivesubfolders

The subfolders will be managed by the jQuery plugin "FancyTree", which uses a JSON object as its source.

    source: [
        {title: "Node 1", key: "1"},
        {title: "Folder 2", key: "2", folder: true, children: [
          {title: "Node 2.1", key: "3"},
          {title: "Node 2.2", key: "4"}
        ]}
      ],


The modifications will be done in the swf object in actionscript, since it provides a "modifyAt" method which is simple to use.

We update the currentsubfolder ArrayCollection and then relaod the FancyTree!

The archive will be updated after 90 days.


## Lazy loading ##

The LSOs will be loaded in the following order

    

    inbox, current, draft, sent, trash, archive