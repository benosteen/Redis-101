Pushing and Popping values
--------------------------

The linked list structure in redis is particularly useful and fast, for a given type of operation. Pushing objects onto either end of a list is uniformly fast - it doesn't matter if there is 2 or 2 million items in a given list, the time to append or prefix a value to it is uniform.

What it isn't, is an array. Retrieval by index or by range is not nearly as fast as you might expect if this were an indexed array of some sort. 

Each list has a 'left' and a 'right', corresponding to either end of the list, and most of the basic commands that work on lists are prefixed by either an 'L' or an 'R' respectively, to let you know which end they work on.

Commands covered here:

LPUSH list_key value - Push the value onto the left of the list
LPUSHX list_key value - Push the value onto the left of the list, ONLY if the list_key exists and is a list
RPUSH list_key value - As LPUSH, but pushes the item onto the right of the list
RPUSHX list_key value - As LPUSHX, but acts on the right side

LPOP list_key - Removes or 'pop's the leftmost item of the list_key list
RPOP list_key - As LPOP, but acts on the right.

RPOPLPUSH - A cunning command which pops a value from the right of one list and pushes it onto the left of another as a single, atomic action.

Basic actions:
--------------

Creating a list is often done by using either LPUSH or RPUSH and having Redis auto-create one. 

    redis> LPUSH mylist "Welcome"
    (integer) 1           <---- Note that the response of a PUSH is the length of the list.

    redis> LPUSH mylist "to"
    (integer) 2
    
    redis> LPUSH mylist "the"
    (integer) 3
    
    redis> LPUSH mylist "jungle"
    (integer) 4

You may dimly remember data-structure ideas like FIFO/FILO, queues, stacks and deques and these are all easily implementable with redis lists:

For example:

Using the above list - we pushed onto the lefthand side, so to use it as a queue - FIFO remember - we have to pop from the right:

    redis> RPOP mylist
    "Welcome"
    
    redis> RPOP mylist
    "to"
    
    redis> RPOP mylist
    "the"
    
    redis> RPOP mylist
    "jungle"
    
    redis> RPOP mylist
    (nil)

Note that this would work equally if we used RPUSH and LPOP, pushing onto the right and popping from the left. Also, the (nil) response reappears here, indicating that the list is empty. This command returns immediately - non-blocking - and so can be used for periodic polling and so on. There are blocking versions, which will be covered in section 3.

A stack is another structure that is readily implementable - FILO, first-in, last-out. The only thing to remember is to POP from the same side that you are PUSHing onto:

    redis> LPUSH stack "Cool"
    (integer) 1
    
    redis> LPUSH stack "are"
    (integer) 2
    
    redis> LPUSH stack "Stacks"
    (integer) 3
    
    redis> LPOP stack
    "Stacks"
    
    redis> LPOP stack
    "are"
    
    redis> LPOP stack
    "Cool"
    redis> 

The linked list in Redis is a double-ended list, which is the 'deque' as mentioned above.

Cunning command: RPOPLPUSH
--------------------------

This has to be one of my favorite commands. In a single atomic action, it will RPOP a value from one list, and LPUSH it onto another. It cannot (or rather, really shouldn't) fail inbetween. It is very effective when used to distribute tasks. A worker process can use its own private work list, claiming items to work on from the main task list using RPOPLPUSH. Once the task is complete, the worker should pass on the task with a report to a logger, and can also send a complete receipt to the process asking for the work to be done.

For example:

Main task queue:

    (task descriptions in the list)
    
    redis> LPUSH jobqueue "Task encoded with JSON or XML or something else."
    (integer) 2003004    <-- lots of jobs, such as lookup X or upload 'Foo.png'
    
    or
    
    (task descriptions as a reference)
    
    redis> SET task_reference:1000 "Task description"
    OK
    
    redis> LPUSH jobqueue task_reference:1000
    (integer) 2001...
    
    
Each worker (of which you can run many of) is given an worker ID of some sort, eg worker_1, _2, etc.

To claim a job to work on (if you are 'worker_1'): 

    redis> RPOPLPUSH jobqueue worker_1:jobs
    "Task encoded wi...."
    
Once job is complete, the task - from 'worker_1:jobs' in this case - can be passed onto a logger for example, or simply RPOP'd off and ignored.

By minimising the points where the task is not held in a persistent store, you minimise the decisions you have to make about how robust you will handle the task descriptions in the worker code. Treating the Redis store as the canonical version, and the jobs held in the worker process's memory as copies of this is a useful way to think about it.

Common usage pattern:
---------------------

One common pattern with lists worth mentioning here is the use of lists for arbitrary ordering of references, where the references are commonly keys in Redis. For example:

    redis> SET comment:10000 "....."
    OK
    
    redis> SET comment:10003 "..... #dev8d"
    OK
    
    redis> SET comment:10005 "....."
    OK
    
    redis> SET comment:10004 ".....#dev8d"
    OK
    
    ... and so on ...
    
    redis> LPUSH dev8d_thread comment:10003
    (integer) 1
    
    redis> LPUSH dev8d_thread comment:10004
    (integer) 2

More usefully, the values of the keys being referenced will be more interesting types of things, like hashes, lists or sets. This allows the 'objects' (comments, users, things more generally) to be recorded once, but indexed and referred to many times.

Exercises:
----------

1.  Practice getting comfortable with the different ways to use the lists - either as queues or stacks. 

2.  The "Hello World" for lists and queues is an IM messaging clone. Do a basic implementation of a client, that only does direct messaging to named users. Use a list_key based on the username and have clients push simple messages of the form "(from ...) Message goes here" to user lists. Have the client periodically pull the messages from their own lists and display it.

3.  Implement the ability to 'mention' (a la twitter) other users and to then broadcast the messages to multiple users mentioned.

4.  Implement a crude broadcast service. 
--  INCR a counter for each posted message, using that number as the message's unique key (eg HMSET "msg:1022" "from" "ben" "text" "My message"' per message).
--  Add the message ID to a to_be_worked_on list.
--  Use a second process (or more) to take a msg reference from the task list, push it to the users mentioned in the message.

5. (Hardcore) Implement a dropbox (using a library such as pyinotify to watch a directory for changes)
-- Post a message onto a list for each event (eg "file x added") make it machine readable, using JSON for instance.
-- Have workers pull each event from the queue and do various actions to it:
--      If it ends in .png, .jpg, etc, upload it to flickr
--      If it ends in .doc, convert it to .txt (using cmdline antiword for example)
--      If it ends in .txt, add it to a search index, such as Solr (solrpy client)
