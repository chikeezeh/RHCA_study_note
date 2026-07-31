#### Using conditionals
##### `loop`
Used to process a list of items, so instead of writing multiple tasks, you write one tasks that loops over the items and executes the task per item.
See examples below:

###### `loop` without variable.
```yaml
- name: start services
  ansible.builtin.service:
    name: "{{ item }}"
    state: started
  loop:
    - vsftpd
    - httpd
```
###### `loop` using a variable.
```yaml
---
- name: Testing how loops work
  hosts: vm1
  vars:
    my_services:
      - httpd
      - sshd
      - vsftpd
  tasks:
  - name: use debug to print various tasks
    debug:
      msg: "starting service: {{ item }}"
    loop: "{{ my_services }}"

```
###### `loop` using a list of dictionaries.

```yaml
---
- name: Testing how loops with dictionaries work
  hosts: vm1
  vars:
    my_users:
      - name: anna
        groups: wheel
      - name: lisa
        groups: admin
  tasks:
  - name: use debug to print users and groups
    debug:
      msg: "Creating user {{ item.name }} and adding to group {{ item.groups }}"
    loop: "{{ my_users }}"

```
##### `handlers`
Can be used to run specific tasks if another task changes something. This prevents unnecessary execution of certain tasks which we only need to be executed if another task makes a change. Example, if we make a change to a service, we want to reload that service, but if no change is made, then the service doesn't need to be reloaded.

###### Example of handler that executes

```yaml
---
- name: Test how handlers work
  hosts: vm1
  tasks:
  - name: Run a generic command
    command: ls -l /root
    notify:
      - nameofhandler
  handlers:
  - name: nameofhandler
    debug:
      msg: main task ran, handler ran

```
Output:
```shell
ansible-playbook handlersdemo.yml

PLAY [Test how handlers work] ********************************************************

TASK [Gathering Facts] ***************************************************************
ok: [vm1]

TASK [Run a generic command] *********************************************************
changed: [vm1]

RUNNING HANDLER [nameofhandler] ******************************************************
ok: [vm1] => {
    "msg": "main task ran, handler ran"
}

PLAY RECAP ***************************************************************************
vm1                        : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
###### Example of handler that won't execute

```yaml
---
- name: Test how handlers work
  hosts: vm1
  tasks:
  - name: Run a generic command
    debug:
      msg: I don't make any change
    notify:
      - nameofhandler
  handlers:
  - name: nameofhandler
    debug:
      msg: main task ran, handler ran
```
Output, note the handler task doesn't execute since the debug module doesn't make a change to the host.

```shell
[ansible@control ansible_work]$ ansible-playbook handlersdemo.yml

PLAY [Test how handlers work] ********************************************************

TASK [Gathering Facts] ***************************************************************
ok: [vm1]

TASK [Run a generic command] *********************************************************
ok: [vm1] => {
    "msg": "I don't make any change"
}

PLAY RECAP ***************************************************************************
vm1                        : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
##### Special note on handlers.
- They are executed after running all the task in a play.
- To run handlers immediately, use `meta: flush_handlers` in the task that triggers the handler.
- Handlers will only run if the `notify` task changes something. 
- If any task in the play fails, the handler won't, this behavior can be overwritten using `force_handlers: True ` at the top of the play.
