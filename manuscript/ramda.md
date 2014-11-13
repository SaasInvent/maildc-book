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

    a = [
       nameA1: valueA1
       nameA2: valueA2
       nameA3: valueA3
      ,
       nameB1: valueB1
       nameB2: valueB2
       nameB3: valueB3
    ]

Will become:

    var a;
    
    a = [
      {
        nameA1: valueA1,
        nameA2: valueA2,
        nameA3: valueA3
      }, {
        nameB1: valueB1,
        nameB2: valueB2,
        nameB3: valueB3
      }
    ];

    // Q.all() and spread()
    var stepA = function () {
        console.log("This is step A, args=", arguments);
        return "ret A";
    };
    var stepB = function () {
        console.log("This is step B, args=", arguments);
        return "ret B";
    };
    var finalStep = function (retA, retB) {
        console.log("This is the final step, args=", arguments);
    };
    var promise = Q.all([Q.fcall(stepA), Q.fcall(stepB)]);
    promise.spread(finalStep);