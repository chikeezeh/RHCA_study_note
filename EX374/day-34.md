#### The `test` plugin.

This can be used in replacement of the `when` keyword. It can be used to test input properties. 

`ansible-doc -t test -l` gives a list of all the properties that can be tested, see example output below.

```shell
[ansible@control ansible_work]$ ansible-doc -t test -l
ansible.builtin.abs             is the path absolute
ansible.builtin.all             are all conditions in a list true
ansible.builtin.any             is any condition in a list true
ansible.builtin.change          did the task require changes
ansible.builtin.changed         did the task require changes
ansible.builtin.contains        does the list contain this element
ansible.builtin.directory       does the path resolve to an existing directory
ansible.builtin.exists          does the path exist, follow symlinks
ansible.builtin.failed          did the task fail
ansible.builtin.failure         did the task fail
ansible.builtin.falsy           Pythonic false
```
