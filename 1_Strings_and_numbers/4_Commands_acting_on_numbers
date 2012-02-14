Commands acting on numbers
--------------------------

Strictly, these commands act on integers only so bear this in mind.

Commands covered here:
----------------------

Simple maths:

INCR key  -  "key = key + 1"
INCRBY key integer - "key = key + integer"
DECR key - "key = key - 1"
DECRBY key integer - "key = key - integer"

Bitwise functions:
SETBIT key bit_offset [1|0]- Set the bit at offset to either 1 or 0
GETBIT key bit_offset - GET the bit value at the given offset

Simple Maths:
-------------

These INCR and DECR commands are very useful indeed in avoiding problematic conditions when redis is being used by more than one client. Succintly, it sidesteps the race-condition that would be presented by the following workflow:

1 - GET counter value
2 - SET counter to value + 1

It shouldn't need to be explained why this pattern is very problematic when more than one client becomes involved. Let's see the commands in action to see how they help here.

Using the INCR/DECR commands:
-----------------------------

redis> INCR counterkey
(integer) 1

redis> INCR counterkey
(integer) 2

redis> GET counterkey
(integer) 2

redis> DECR counterkey
(integer) 1

redis> DECRBY counterkey 5
(integer) -4

redis> INCRBY counterkey 10
(integer) 6

Note that Redis initialised the counterkey value to 0 before applying the INCR command at the first step and the response for each issuing of the command is the value that the key is now set to, not the older value.

One situation where I have used this funcitonality extensively is in tallying and analysing datasets using multiple processes, a sort of poor-mans-Hadoop.

Exercises
---------

1. Go through all the 'sherlock:*' keys and create a tally count for each occurence of a given word and additional tallys for number of words in a given paragraph and number of words processed in total. Full marks only if you use threads or multiprocessing to do so.

1b. Grab a few more texts from Project Gutenberg and create tallys as above. Compare and contrast the usage of certain groups of words between older and newer texts.

2. Imagine it's 1993 and a client wants to have a click counter on their website, with a counter per page. How would you build it using Redis? (if it had existed back then and Piwik/GA is unavailable for some contrived reason.)

3. The client is pleased with the click counter, but now wants page analytics - a daily total of views per page. They are very keen that not a single pageview is missed. How might you build this in? (GETSET might be a useful command here.)

Using SETBIT and GETBIT
-----------------------

Intuitively useful for application flags, but note that these commands can act on *any* string, and so can be used to make arbitrary changes as required.

redis> SET foo 8 1
(integer) 0

(NB foo didn't exist until the SETBIT command)

redis> GET foo
"\x00\x80"

redis> GETBIT foo 8
(integer) 1

