#### Capstone on previous exam objectives.

`roles` are not listed as exam objectives, however they are ground knowledge assummed by the exam. 
This capstone will use `roles` as a vehicle to practice the following objectives.

- Understand and use Git

- Manage inventory variables

- Transform data with filters and plugins

- Delegate tasks

- Manage content collections / Manage automation controller


###### Task 1 — Scaffold the `common` role.

From the project root, use ansible-galaxy role init to create a role named `common` under roles/. 
The role must: define a default variable common_env_name: development in the role's defaults, and deploy `/tmp/common-marker.txt` from a role template that renders that variable (template lives in the role's templates/).Prove: a throwaway playbook applying only this role to frontend-1, run output, and cat `/tmp/common-marker.txt` on frontend-1 showing development. Rerun must be changed=0.


<details>
<summary> Click to see solution </summary>

Created the `common` role using `ansible-galaxy role init common --init-path roles`. Below is the structure of the role, including the task 1 template `task1.j2`. 

```shell
[ansible@tower ansible-lab (main *%)]$ tree roles/common/
roles/common/
├── defaults
│   └── main.yml
├── files
├── handlers
│   └── main.yml
├── meta
│   └── main.yml
├── README.md
├── tasks
│   └── main.yml
├── templates
│   └── task1.j2
├── tests
│   ├── inventory
│   └── test.yml
└── vars
    └── main.yml
```
Then I edited `./roles/common/defaults/main.yml` to contain the desired variable.

```shell
[ansible@tower ansible-lab (main *%)]$ cat roles/common/defaults/main.yml
---
# defaults file for common
common_env_name: development
```

Created a simple task in `.roles/common/tasks/main.yml` to have the desired task of using the template and creating a specified file. 

```yaml
---
# tasks file for common
- name: Create a file using templates
  template:
    src: task1.j2
    dest: /tmp/common-marker.txt
```

Finally, called the role in a playbook.

```yaml
---
- name: Test common role we created
  hosts: frontend-1
  gather_facts: false
  roles:
    - role: common

```


</details>

Task 2 — Role parameters beat defaults.

One playbook, two plays: play 1 applies common to frontend-1 with `common_env_name `passed as a role parameter set to `alpha`; play 2 applies it to frontend-2 with the parameter set to `beta`. Do not edit the role's defaults, and do not use host_vars/group_vars for this.Prove: playbook file plus cat of the marker file on both hosts — alpha on frontend-1, beta on frontend-2.

<details>
<summary> Click to see solution </summary>

Created a simple playbook shown below to answer the question.

```yaml
---
- name: use common role on frontend-1
  hosts: frontend-1
  gather_facts: false
  roles:
    - role: common
      vars:
        common_env_name: alpha

- name: use common role on frontend-2
  hosts: frontend-2
  gather_facts: false
  roles:
    - role: common
      vars:
        common_env_name: beta

```

</details>