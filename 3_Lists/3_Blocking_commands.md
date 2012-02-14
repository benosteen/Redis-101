Blocking Commands:
------------------

Blocking commands function much like their B-prefixless counterparts, but if the list they are POPping from is empty, it will block the process until something is added to the list. This tends to be a much more efficient way to have workers act, rather than constant optimistic polling.

BLPOP mylist - POP a value from the left of mylist, blocking and waiting for a value in the case that the list is empty.
BRPOP mylist - As above, but popping from the right side of the list

BRPOPLPUSH mylist worker_list - Same functionality as RPOPLPUSH, but will block as mentioned above until there is a value in mylist.

Exercises:
----------

1. Using your new found ways to improve latency and efficiency of worker processes, amend any code you've written for the IM or broadcast clones to include this functionality for the workers.

