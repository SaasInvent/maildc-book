#MailDc and Ramda

[Ramda](https://github.com/ramda/ramda) is a functional javascript library.

We are using the concepts documented in the following article

 - [Favoring curry](http://fr.umio.us/favoring-curry/)


The Beginning
-------------

Everything above assumes you get a promise from somewhere else. This is the common case. Every once in a while, you will need to create a promise from scratch.


## Q with CoffeeScript ##

Here is a very good example of the [usage of Q with coffeescript.](http://asaf.github.io/blog/2013/07/09/q-promises-with-coffeescript/)


Defining promises with CoffeeScript:

1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
q = require 'q'

exports.hello = () ->
  d = q.defer()
  d.resolve 'hello'
  d.promise

    exports.world = () ->
      d = q.defer()
      d.resolve 'world'
      d.promise

    exports.die = () ->
      d = q.defer()
      d.reject 'bye world'
      d.promise

And here are Mocha sample of Q propagations and error handling


    assert = require 'assert',
    promises = require './promises'
    
    describe('Promises', () ->
      it 'Simple', (done) ->
        promises.die().then(
          (val) =>
            #handle val
          (err) =>
            assert.equal err, 'bye world'
            done()
        )




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

Here is another example

    function square(x) {
        return x * x;
    }
    function plus1(x) {
        return x + 1;
    }
    
    Q.fcall(function () {return 4;})
    .then(plus1)
    .then(square)
    .then(function(z) {
        alert("square of (value+1) = " + z);
    });