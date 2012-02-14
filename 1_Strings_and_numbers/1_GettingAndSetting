Getting and Setting Values in Redis
-----------------------------------

Commands covered here:
----------------------

GET - GET a key to a string
SET - SET a key to a string
EXISTS - Sees if a key EXISTS or not
DEL - DELete a key

GETSET - SET a key to a string, and return the key's original value

SETNX - SET a key to a string, only if that key does not exist

First steps: GET, SET, DEL and EXISTS
-------------------------------------

As is traditional, we will start with 'Hello World':

(Connect to your redis instance, either programmatically, using 'redis-cli' or by visiting 'Try Redis' - http://try.redis-db.com/)

redis> SET message "Hello World"
OK     (those on the Try Redis will see "OK" here)

redis> GET message
"Hello World"


Redis keys are 'binary-safe', which implicitly means that they are also case-sensitive:

redis> GET Message
(nil)      (Try Redis will respond will null here)

redis> GET MESSAGE
(nil)

This can be checked with the EXISTS command too:

redis> EXISTS message
(integer) 1

redis> EXISTS Message
(integer) 0

However, it should be noted that commands are not case-sensitive even though everything after them is:

redis> get message
"Hello World"

Finally, it is time to get rid of this message:

redis> DEL message
(integer) 1

A response of '1' shows that this action succeeded. You can verify this by trying to access the message key again:

redis> GET message
(nil)

Integers
--------

You can also set keys to be associated with integers too, and Redis provides commands that work with these, such as INCR and DECR as detailed later.

redis> SET counter 0
OK

redis GET counter
"0"

Note that even though this looks like a string, Redis will handle it differently when you use the integer-specific commands.

Exercises
---------

1.  Set the key "sherlock:para1" to the first paragraph of the "Adventures of Sherlock Holmes", the paragraph that begins 'To Sherlock Holmes she is always THE woman.' (The text can be found in the gzip'd archive pg1661.txt.gz in this directory, or available from http://www.gutenberg.org/cache/epub/1661/pg1661.txt)
    
    Confirm this, by GETting the paragraph back out again.

(requires using a programmatic client, or OCD and a lot of time.)

2. Continue setting keys of the form "sherlock:paraXXXX", where XXXX is the number of the paragraph, based on this text. Make the assumption that a blank line indicates the beginning of a new paragraph. Retain the code you use here, as you will be able to adapt it for later exercises.

3. (Hardcore mode)

Pull in the tweets for '#dev8d', and store the JSON response for each one in a key equal to the unique ID of the tweet.

Official documentation:
 GET http://redis.io/commands/get
 SET http://redis.io/commands/set
 DEL http://redis.io/commands/del
 EXISTS http://redis.io/commands/exists

Useful commands: GETSET
-----------------------

With multiple threads or clients working on the same set of keys, it can be problematic to make sure that one client does not trample over the work of another. One of the powerful aspects of Redis is that there are a number of commands that help to eliminate this problem from your service/app.

GETSET is one of these commands, providing the ability to SET a key to a new value and GET to old value as a single, atomic action. When working with a counters for example, this command is useful in avoiding a race-condition when reseting the counter to zero and getting the value it reached while other clients are incrementing or otherwise changing the counter's value.

Exercise
--------

1. Play around with this function to get used to how it functions. What does it respond if the key does not exist? 

 GETSET http://redis.io/commands/getset

Useful commands: SETNX
----------------------

Another useful command for dealing with possible race-conditions is the SETNX command. Consider the following (contrived) situation:

You need to create a system for assigning a small numeric ID to arbitrary tags, set by multiple clients. The basic plan is to have the tags as keys, with the values set to the smaller, numerical ID. The workflow could be simple (and broken!):

1 - get a tag and see if it EXISTS as a key.
2 - if it does, just return the value of that key.
3 - If not, GET the last ID and add one to it.
4 - SET the tag key to be equal to ID
5 - Use that ID for other things, associating with comments or posts, etc.

The problem occurs between step 3 and 4. It is possible for two clients to try to create IDs for the same tags simultaneously. The faster client would set the tag equal to one ID and this will be overwritten by the second.

Using SETNX at point 4, this can be improved so that the second, fractionally slower client can detect if it has been beaten to the punch, and just return the value of the key as set by the first client.

