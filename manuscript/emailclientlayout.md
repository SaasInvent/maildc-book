#Email client layout

MailDC email client layout heavily relies on the following open source projects

 - [jQuery UI Layout](http://plugins.jquery.com/layout/)
 - [jQuery tablesorter](http://tablesorter.com/docs/)
 - [Fancytree](https://github.com/mar10/fancytree/)



## UI Layout ##

The general layout of the page will be done with the 

 - [jQuery UI Layout](http://plugins.jquery.com/layout/)

This layout plugin affords collapsible panes.

There will be three parts:

 - on the left hand side: a tree with the email folder
 - on the right hand side two panes horizontally stacked:
	 - upper pane : sorted table with the list of emails
	 - lower pane: email body in html



## Fancy Tree ##

The [Fancytree](https://github.com/mar10/fancytree/) jQuery plugin is in charge to manage the email folders

 - inbox
 - drafts
 - sent
 - spam
 - private folders


## Table sorter ##

The [jQuery tablesorter](http://tablesorter.com/docs/)  plugin is in charge to manage the email list of the selected email folder (inbox, draft, ...)







> Written with [StackEdit](https://stackedit.io/).