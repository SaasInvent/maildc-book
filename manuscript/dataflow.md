#Data flow in browser

MailDC uses two browser data stores

 - in memory data store (LokiJS)
 - persistent data store (LSO)

This chapter describes the data flow between these two data stores.

## In memory data store ##

Once the page page is ready ($.documentready), MailDC fetches its email data from the persistent storage into memory.


## Persistent data store ##

On every update or creation of data, the data in the LSO will be saved.

