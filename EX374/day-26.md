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


