#### Fact Caching Caveats

- If a playbook uses facts but has `gather_facts: false`, it will fail if the cached facts expire. 
- A scheduled job to refresh cache can prevent cache from expiring.
- To use `gather_facts` in place of `cache`, run the playbook with the `--flush-cache` flag. 

#### `password` and `password_hash` plugins.

The `password` plugin can be used to generate a random string that can then be passed into the `password_hash` pluging to create an encrypted password for a user. See example playbook below. 

The playbook creates users from the `users` variable, and assigns random passwords that are 6 character long. The passwords are stored in the `credentials` directory, under a file named after each user. 

```yaml
---
- name: Create users using random passwords.
  hosts: vm1
  vars:
    users:
      - linda
      - anna
      - lisa
      - isabelle
    userpasswd: "{{ lookup('password', 'credentials/' + item + ' length=6') }}"
  tasks:
  - name: create users
    user:
      name: "{{ item }}"
      password: "{{ userpasswd | password_hash('sha512') }}"
      update_password: on_create
    loop: "{{ users }}"
```

Output:

```shell
[ansible@control ansible_work]$ ansible-playbook userswithpass.yaml

PLAY [Create users using random passwords.] **********************************************************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************************************************************************
ok: [vm1]

TASK [create users] **********************************************************************************************************************************************************************************
[DEPRECATION WARNING]: Encryption using the Python crypt module is deprecated. The Python crypt module is deprecated and will be removed from Python 3.13. Install the passlib library for continued
encryption functionality. This feature will be removed in version 2.17. Deprecation warnings can be disabled by setting deprecation_warnings=False in ansible.cfg.
changed: [vm1] => (item=linda)
changed: [vm1] => (item=anna)
changed: [vm1] => (item=lisa)
changed: [vm1] => (item=isabelle)

PLAY RECAP *******************************************************************************************************************************************************************************************
vm1                        : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
Credential files created:

```shell
[ansible@control ansible_work]$ ls -l credentials/
total 16
-rw-------. 1 ansible ansible 7 Aug 25 19:03 anna
-rw-------. 1 ansible ansible 7 Aug 25 19:03 isabelle
-rw-------. 1 ansible ansible 7 Aug 25 19:03 linda
-rw-------. 1 ansible ansible 7 Aug 25 19:03 lisa
```
