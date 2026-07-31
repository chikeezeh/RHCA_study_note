#### Using conditionals contd.

##### `when`
Is used to perform an action only if a specific condition is true, for example we can use `yum/dnf` module when our OS is `RHEL` or `apt` when our OS is `Ubuntu`, and have this as one task. 

To use `when` correctly, we need to refer to the right variable type. For example:

`ansible_machine == "x86_64" ` --> variable is a string
`ansible_memfree_mb == 1024 ` --> variable is an integer
`my_variable is defined` --> variable is a boolean
`my_variable` --> variable is Boolean True

When using comparisons, variable types are important, to prevent errors, use filters to cast the variable type. See example below;

`when vgsive | int > 5` This makes sure the `vgsize` variable is an integer before comparing it to the number 5. 
`when runme | bool` This makes sure that `runme` is treated as a boolean and it checked to see if it returns `True`.
