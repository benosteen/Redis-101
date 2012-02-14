Commands acting on strings
--------------------------

Commands covered here:

APPEND key value - Append the value to the end of whatever value was associated with key
GETRANGE key start end - GET a portion of the string in key. Semantically equivalent to a "substr()" command.
SETRANGE key offset value- Overwrite a portion of the string
STRLEN key - Length of the value in key

These commands are generally straightforward tools for manipulating strings. Please see the official documentation for further details. Some interactions will be detailed here to show how the commands function. These interactions are derived from the official documentation.

APPEND http://redis.io/commands/append
GETRANGE http://redis.io/commands/getrange
SETRANGE http://redis.io/commands/setrange
STRLEN http://redis.io/commands/strlen

APPEND:
-------

    redis> EXISTS mykey
    (integer) 0
    
    redis> APPEND mykey "Hello"
    (integer) 5
    
    redis> APPEND mykey " Dev8D"
    (integer) 11
    
    redis> GET mykey
    "Hello Dev8D"

GETRANGE:
---------

    redis> SET mykey "This is a string"
    OK
    
    redis> GETRANGE mykey 0 3
    "This"
    
    redis> GETRANGE mykey -3 -1
    "ing"
    
    redis> GETRANGE mykey 0 -1
    "This is a string"
    
    redis> GETRANGE mykey 10 100
    "string"

SETRANGE:
---------

    redis> SET key1 "Hello World"
    OK
    
    redis> SETRANGE key1 6 "Dev8D"
    (integer) 11
    
    redis> GET key1
    "Hello Dev8D"

