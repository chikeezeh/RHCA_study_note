#### Delegate tasks - Drills

The drills that will follow will test the following exam objectives.

- Run a task for a managed host on a different host
- Control whether facts gathered by a task are delegated to the managed host or the controlling host.


###### Task 1 — delegate_to. 

Write a play targeting web. For each host, delegate a task to localhost that appends one line — the managed host's inventory hostname — to /tmp/delegation-report.txt on the control node. Prove: the file exists on tower (not on the web hosts) with one line per web host.