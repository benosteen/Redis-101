Basic Hash commands
-------------------

Commands covered here:
----------------------

HSET key field value - SET 'field' to be equal to the value in the hash 'key' --> "key[field] = value"
HGET key field - GET the value of 'field' in the hash 'key'
HGETALL key - GET all the field:value pairs held within the hash 'key'
HDEL key field - DEL 'field' from the hash 'key'

HSETNX - as HSET above, but only succeeds if 'field' doesn't already exist.

Basic Hash Usage:

Hashes were introduced into Redis in one of the later 2.x versions. Before this, many applications used conventions to impose a hash-like hierarchy on the keys in the Redis db. You may still see documentation or code that uses commands of the sort:

redis> SET users:10001:passwordhash "md5:....."
OK

Redis doesn't ascribe any particular behaviour to the colon or structure in the key used here. It's just another key to Redis. However, it helps segregate keys into groups. One way of utilising hashes to improve this might be to do the following:

redis> HSET user:10001 passwordhash "...."
OK

In other words, use a hash per user to contain all the user-specific data. Note that the hash key doesn't need to exist before using it in this way. Redis will instantiate an empty hash and associate it with the key you use automatically.

To get back the passwordhash from this example, you can use the command HGET:

redis> HGET user:10001 passwordhash
"......."

Placing related information into a single hash has certain benefits, such as the following:

Getting all the data from a hash:
---------------------------------

redis> HSET myhash field1 "Hello"
(integer) 1
redis> HSET myhash field2 "World"
(integer) 1

redis> HGETALL myhash
1) "field1"
2) "Hello"
3) "field2"
4) "World"

Even though this appears to return the fields in the same order they were issued, note that this is a hash and that you cannot trust the field-value pairs to be returned in any particular order.

Deleting fields:
----------------

The ordinary DEL command will remove the entire hash:

redis> DEL myhash
OK

redis> EXISTS myhash
(integer) 0

Use the related command, HDEL, to remove a single field from a hash:

redis> HDEL myhash field1
OK

Exercises:
----------

1. Getting used to hashes: Take a CSV file, such as one from http://data.gov.uk/dataset/financial-transactions-data-cumbria-pct and store it as a hash per line.

2. (Hardcore) Create a dict/hash class in your chosen language that provides a subset of the normal dict/hash methods, but maps them onto a Redis-persisted hash instead of holding values locally.
