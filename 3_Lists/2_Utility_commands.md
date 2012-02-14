List utility commands
---------------------

A number of utlity commands for lists exist, some obvious in use and some that should be used sparingly.

LLEN list_key - Returns the length of the list.

LRANGE
------

LRANGE mylist start end

A useful command, when used with care. The server responds with the range of values from the list, as you may expect. However there are some important caveats to be aware of:


Consistency with range functions in various programming languages

Note that if you have a list of numbers from 0 to 100, LRANGE list 0 10 will return 11 elements, that is, the rightmost item is included. This may or may not be consistent with behavior of range-related functions in your programming language of choice (think Ruby's Range.new, Array#slice or Python's range() function).


Out-of-range indexes

Out of range indexes will not produce an error. If start is larger than the end of the list, an empty list is returned. If stop is larger than the actual end of the list, Redis will treat it like the last element of the list.

    redis> RPUSH mylist "one"
    (integer) 1
    
    redis> RPUSH mylist "two"
    (integer) 2
    
    redis> RPUSH mylist "three"
    (integer) 3
    
Start and end set to same value - returns just that value
    
    redis> LRANGE mylist 0 0
    1) "one"

Minus numbers work from the right end of the list, as opposed to the left end. Note that the indexes start at 0, so the end '2' refers to the third and last element of the list.

    redis> LRANGE mylist -3 2
    1) "one"
    2) "two"
    3) "three"

An example of the out-of-range edge case:

    redis> LRANGE mylist -100 100
    1) "one"
    2) "two"
    3) "three"
    redis> LRANGE mylist 5 10
    (empty list or set)

---------

LINDEX list_key value - Get a value from the list at the given index along from the left. As the structure is not an array but a list, this is a much more expensive operation than working on the elements at either end of the list.
    
Important to note that if you use LPUSH, then LINDEX might work counter-intuitively to how you might expect, as it also works from the lefthand side:

    redis> LPUSH m 1
    (integer) 1
    redis> LPUSH m 2
    (integer) 2
    redis> LPUSH m 3
    (integer) 3
    
List is [3, 2, 1] not the other way around. An index of -1 results in the last element of the list, or the 'first-in':
    
    redis> LINDEX m -1
    "1"
    
    redis> LINDEX m -2
    "2"
    
    redis> LINDEX m -3
    "3"
    
    redis> LINDEX m 1
    "2"
    
    redis> LINDEX m 2
    "1"
    
---------

LINSERT list_key [BEFORE|AFTER] pivot_value value - insert value into the list, either BEFORE or AFTER the pivot_value

For example:

    redis> LPUSH mylist 10
    (integer) 1
    
    redis> LPUSH mylist 11
    (integer) 2
    redis> LPUSH mylist 13
    (integer) 3
    
    redis> LPUSH mylist 14
    (integer) 4
    
    redis> LPUSH mylist 15
    (integer) 5
    
Note that this contrived sequence is missing a '12' value. Let's use LINSERT to put it in, in its right place:
    
    redis> LINSERT mylist BEFORE 13 12
    (integer) 6
    
    or
    
    redis> LINSERT mylist AFTER 11 12
    (integer) 6
    
An important note is that the LINSERT command starts at the lefthand end of a list and works along it, until it reaches the pivot_value. This results in the operation taking O(n) time, where n is the number of elements to the left of the pivot_value. If the list does not hold the pivot_value, this will still take up time proportional to the length of the list even though it fails.

LTRIM mylist start end - Trims the list from the left from the start index to the end index. From the documentation:

    A common use of LTRIM is together with LPUSH/RPUSH. For example:
    
        LPUSH mylist someelement
        LTRIM mylist 0 99
    
    This pair of commands will push a new element on the list, while making sure that the list will not grow larger than 100 elements. This is very useful when using Redis to store logs for example. It is important to note that when used in this way LTRIM is an O(1) operation because in the average case just one element is removed from the tail of the list.

LREM (from official documentation: <http://redis.io/commands/lrem> )

Removes the first count occurrences of elements equal to value from the list stored at key. The count argument influences the operation in the following ways:

    count > 0: Remove elements equal to value moving from head to tail.
    count < 0: Remove elements equal to value moving from tail to head.
    count = 0: Remove all elements equal to value.

For example, LREM list -2 "hello" will remove the last two occurrences of "hello" in the list stored at list.

Note that non-existing keys are treated like empty lists, so when key does not exist, the command will always return 0.

    redis> RPUSH mylist "hello"
    (integer) 1
    redis> RPUSH mylist "hello"
    (integer) 2
    redis> RPUSH mylist "foo"
    (integer) 3
    redis> RPUSH mylist "hello"
    (integer) 4
    redis> LREM mylist -2 "hello"
    (integer) 2
    redis> LRANGE mylist 0 -1
    1) "hello"
    2) "foo"

LSET list_key index value (also from official documentation <http://redis.io/commands/lrem>):

Sets the list element at index to value. For more information on the index argument, see LINDEX. For example, "LSET mylist 4 foo" is equivalent to "mylist[4] = 'foo'"

An error is returned for out of range indexes.

    redis> RPUSH mylist "one"
    (integer) 1
    redis> RPUSH mylist "two"
    (integer) 2
    redis> RPUSH mylist "three"
    (integer) 3
    redis> LSET mylist 0 "four"
    OK
    redis> LSET mylist -2 "five"
    OK
    redis> LRANGE mylist 0 -1
    1) "four"
    2) "five"
    3) "three"

