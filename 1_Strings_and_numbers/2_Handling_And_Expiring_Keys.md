Handling and Expiring Keys
--------------------------

Commands covered here:
----------------------

Handling:

KEYS pattern - returns all the keys that match the given pattern
TYPE - returns the type of value associated with a key
RENAME key newkey - RENAMEs the given key to newkey
RENAMENX - RENAMEs the given key to newkey, *only* if newkey does not already exist
RANDOMKEY - GETs a random key

Expiration/persisting:

EXPIRE key timeout - Will cause the given key to expire and be removed once 'timeout' number of seconds have elapsed
EXPIREAT key when - As EXPIRE above, but the timeout is set as an absolute UNIX timestamp (seconds since January 1, 1970).
SETEX - SET a key to a string, with an expiration (a Time-To-Live)

PERSIST key - Will remove any expiration associated with this key, persisting it.
TTL key - How many more seconds will key exist before being removed.


Handling keys:
--------------

An expensive, but useful command is the KEYS command. It allows you to find any key in the redis instance that match a given pattern. The most basic pattern is just a wildcard - '*'. This will attempt to return all the keys in the current db.

(connect to a used redis instance:)

    redis> KEYS *
    ......... lots of keys .....

TYPE is also a very useful tool to help you inspect or perhaps even rediscover the type of value associated with a KEY. This becomes more useful later, when you are working with other sorts of values, such as lists and sets, as well as strings and integers.

    redis> SET message "Foo"
    OK

    redis> TYPE message
    string

(List example:)

    redis> LPUSH queue "Foo"
    (integer) 1

    redis> TYPE queue
    list

Renaming keys
-------------

RENAME key is a handy command which has its uses. It simply changes the association of a value from one key value to another. For example:

    redis> SET mykey "Hello Dev8D"
    OK

    redis> RENAME mykey mynewkey
    OK

    redis> GET mynewkey
    "Hello"

    redis> GET mykey
    (nil)

RENAMENX is useful in a multi-client system much like SETNX before, as this will allow you to avoid accidentally overwriting keys through renaming.

Expiration and Persistence
--------------------------

By default, keys you create in Redis will not expire. There are a number of situations where having keys exist temporarily is a useful tool and Redis provides a number of commands that are, frankly, wonderful for most tasks that need it.

There are three key commands used to set expirations: EXPIRE, EXPIREAT and SETEX:

(cause the 'message' key to expire in 10 seconds)

    redis> EXPIRE message 10
    (integer) 1
    
    redis> GET message
    "Foo"

... wait a bit ...

    redis> GET message
    (nil)

EXPIREAT works in the same manner, but takes a UNIX timestamp for the time you want things to expire:

For example:
The epoch timestamp of 1329236925 corresponds to the normal time of Tue, 14 Feb 2012 16:28:45 GMT

    redis> EXPIRESAT queue 1329236925
    (integer) 1

SETEX is a way to set both the value and the expiration timeout of a key in a single action (NB note the slightly counter-intuitive syntax. This is required as the string value can be binary.)

    redis> SETEX message 30 "This message will self-destruct"
    OK

The key 'message' will expire in 30 seconds from the point when the command is accepted.

Checking expiration times - TTL
-------------------------------

TTL is a handy command to check how long keys have to 'live'. For example:

    redis> SETEX message 30 "This message will self-destruct"
    OK
    redis> TTL message
    (integer) 28

...

    redis> TTL message
    (integer) 18

...

    redis> TTL message
    (integer) -1

(message key no longer exists, so the TTL command will respond with -1)

NB TTL is not a replacement for EXISTS however. It will respond with -1 when the key given either exists and is not set to expire OR when the key does not exist.

Removing expirations - PERSIST
------------------------------

Quite simply, this will remove any expiration associated with a key and will do nothing if the key is already permanent.

Exercises:
----------

1. What happens if you set a timeout for a non-existing key? Or attempt to persist one?

2. Consider (and if desired, build) a system that makes sure that a user of a web app can only vote on a poll once every X hours.

3. (Hardcore) consider (and perhaps build) a system that takes a feed (atom, twitter, and so on) and builds up a list of trending tags that reflect the most used tags in the past 24 hours. It is likely that the solution will be made much better by also using some of the integer and hash commands too, so you might want to attempt this later on.
