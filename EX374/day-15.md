#### Lab on conditionals
Question: Using the command module, make adding a user idempotent.

<details>
<summary> Click to expand Solution 1 </summary>
The first approach is to use block, rescue, always. This way if we try adding the user in the block and it fails because the user already exists, the rescue prints a message telling us that the user already exists, and the play continues. The alwyas will run regardless since in each situation the user should be in the host. See implementation below:

```yaml
---
- name: This play uses the command module to add a user idempotently
  hosts: vm1
  vars:
    username: joan
  tasks:
  - name: use the command module to add a user name
    block:
    - name: add the user
      command: useradd {{ username }}
    rescue:
    - name: runs if user is already added
      debug:
        msg: The user {{ username }} is already in the system
    always:
    - name: final result
      debug:
        msg: The {{ username }} has been added.

```

</details>

<details>
<summary> Click to expand Solution 2 </summary>
The second approach is to check if the user already exists, and use a when clause to run the command module if the user doesn't exist. See implementation below:


```yaml
---
- name: This play uses the command module to add a user idempotently
  hosts: vm1
  vars:
    username: joan
  tasks:
  - name: use the shell command to capture the content of /etc/passwd
    shell: cat /etc/passwd
    register: passwd_content
 
  - name: use the command module to add a user name
    command: useradd {{ username }}
    when: passwd_content.stdout.find(username) == -1

```

</details>

#### File management.

There are a couple of key modules that can be used to manage file creation, content manipulation etc.

- `ansible.builtin.copy` Used to copy files from control host to a managed host.
- `ansible.posix.synchronize` syncs file contents and is more efficient than copy. This requires `rsync` installed on all the hosts involved. 
- `ansible.builtin.fetch` will fetch a file from a managed host to the control host.
- `ansible.builtin.stat` Gets the status of files.
- `ansible.builtin.blockinfile` Can be used to write a block of text to a file, and much more.