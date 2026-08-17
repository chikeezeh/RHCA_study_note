#### Using filters to change variables.

Filters allow variable's data type to be changed before they are used, this doesn't change the original variable, hence if you need to do a filter of the variable in multiple parts of the play, the filter has to be applied again.

Syntax; `{{ variable | filter }}`

Example:

The prompt asks for an input, however this is treated as a `string` object, to be able to do an `int` comparison, we use a filter to convert from `string` to `int`.

```yaml

---
- name: test int filter
  hosts: vm1
  vars_prompt:
  - name: filesize
    prompt: "Specify a file size in megabytes"
    private: no
  tasks:
  - name: check if file size is valid
    assert:
      that:
      - (filesize | int) <= 100
      - (filesize | int) >= 1
      fail_msg: "file size must be between 0 and 100"
      success_msg: "file size is good, lets continue"
  - name: create a file
    command: dd if=/dev/zero of=/bigfile bs=1 count={{ filesize }}

```

#### Some common filters

- `mandatory` fails a play if a variable doesn't have a value
- `default` will set a variable with a default value, `{{ my_var | default(myvalue, True) }}` the default value is `True` if `myvalue` is empty. 
- `capitalize` will capitalize a string
- `int` convert a variable to an integer
- `float` convert a variable to a float
- `+ - / * ` will perform basic calculations. 
- `union` creates a single list from multiple values.
- `random` extracts a randome element from a list.
- `sort` will sort a list. 
- `password_hash` creates a password hash   
