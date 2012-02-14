More Hash Commands
------------------

Commands covered here:
----------------------

HEXISTS key field - Does 'field' exist in the hash 'key'?
HKEYS key - Get all the 'fields' from the hash 'key'
HVALS key - Get all the values from the hash 'key'
HLEN key - Get the number of fields in the hash 'key' (not the actual size of the values it contains.)

HMGET key field1 field2 etc - Batch retrieve the field values from the hash 'key'
HMSET key field1 value1 field2 value2 etc - Batch set the field values for the hash 'key'

HINCRBY key field - INCR the field value in hash 'key'

Hash Utility Functions
----------------------

The utility functions here are a smaller subset of the usual hash or dict methods you might have grown accustomed to relying upon, but they are still very useful in their own right.

HEXISTS is a hash version of the EXISTS command - testing for the presence of a particular field in a given hash. (In my experience, I haven't found much use for this - a nil or null return from a HGET or HMGET is equivalent to a non-existent value and it's easy (and transactionally faster) to code for this exception, rather than add in a lookup call to check for existence.)

HKEYS and HVALUES work as suggested - the former responds with a list of all the keys in a given hash and the latter with all the values in it. One important thing to bear in mind however is that you should not rely on the ordering between the two calls to be the same. For example, you shouldn't assume that the 4th field in the HKEYS list corresponds to the 4th value in the HVALUES list.

HLEN does what it says on the tin, assuming you know that LEN or length of dictionaries is customarily the number of fields the hash contains.

Batch Hash functions
--------------------

HMSET and HMGET are two commands that can really speed up transactions with the server, especially when you are doing large numbers of lookups and settings to one or more hashes.

One way I have used them in the past is to speed up geo-lookups from a cache of geolocated plain-text addresses. It takes the result of basic query (in JSON), within which there are a large number of addresses in plain text and augments it with Lat/Long fields, before sending it to a JS display. Null geocoding responses are cropped from the JSON. You can see the result here: <http://benosteen.com/globe>


