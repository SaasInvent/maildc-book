#MailDc and Ramda

[Ramda](https://github.com/ramda/ramda) is a functional javascript library.

We are using the concepts documented in the following article

 - [Favoring curry](http://fr.umio.us/favoring-curry/)


The Beginning
-------------

Everything above assumes you get a promise from somewhere else. This is the common case. Every once in a while, you will need to create a promise from scratch.

Using Q.fcall

You can create a promise from a value using Q.fcall. This returns a promise for 10.

    return Q.fcall(function () {
        return 10;
    });