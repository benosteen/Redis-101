Batch GETting and SETting values
--------------------------------

One of the bigger 'costs' with Redis can be the cost of handshaking and/or reconnecting to a server instance. Wouldn't it be handy if we could send a large number of updates or GET requests in a single action?

Yes, and the following commands seek to help with this. (There is also a way to pipeline commands into transactions which can further increase the speed. This is particularly noticeable when using interpreted languages such as python and ruby. This will be dealt with in the part on Transactions later on.)

MGET - GET the values for a number of keys simultaneously.
MSET - SET the values for a number of keys simultaneously.
MSETNX - As MSET above, but will only succeed when the keys referenced do not already exist.

Using multiple keys:
----------------------

The MSET syntax is straightforward, appending the key value pairs after the MSET command:

    redis> MSET key1 value1 key2 value2 .... keyN valueN

For example:

    redis> MSET foo1 "Hello" foo2 "World" foo3 "Dev8D"
    OK

MGET is even more straightforward, simply being MGET followed by a list of keys you wish to retrieve. For example:

    redis> MGET foo1 foo3 thiskeydoesntexist foo3
    1) "Hello"
    2) "Dev8D"
    3) (nil)
    4) "Dev8D"

It should be clear that some interesting things happen here. Firstly, the keys are returned in the same order that they are queried in. Note that I included a key that does not exist and this responds in exactly the same manner as it would do if I had asked for it individually. Finally, you can ask for the same key multiple times, and it will appear in the responses multiple times too, so something to be aware of.

Exercise
--------

1. Adapt the Sherlock paragraph setting code you wrote previously (in section 1 of this) to set the paragraphs in configurable batches, using MSET.
