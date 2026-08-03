#### Using the `fail` module.

A non zero exit code indicates that a task has failed on a host, if a task fails on a host, ansible stops the rest of the play execution on that host and moves to the next host. 

Errors can be handled using various methods;
- The `block`, `rescue`, and `always` as discussed above.
- Using `ignore_erros: yes` in the task or play to ignore errors.
- Using `force_handlers: yes` to make sure a handler runs even if an error occurs in the notifying task. Note `ignore_erros: yes` supercedes `force_handlers: no` if both are used together, meaning the `handler` will still run even if the `notify` task fails because we are ignoring errors. 