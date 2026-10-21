#### Manage content collections - drills

The following exam content will be practiced in the drills that follow;

- Create a content collection
- Install a content collection
- Publish a content collection

These drills will be performed on AAP. 

###### Task 1 — requirements.yml + first FQCN module. 

Add collections/requirements.yml to the repo pinning community.general to an exact version (if the newest release demands a newer ansible-core than 2.16, pin the newest that accepts 2.16 — discovering that is part of the task). Write a playbook that uses community.general.ini_file by its full FQCN to set a value in an INI file on the web hosts, and run it via a job template. Prove: job output, plus the INI file's contents on a web host.

<details>
<summary> Click to see solution </summary>

First step is to see what version of ansible is running in my execution environment. I grabbed the registry image from AAP, which was: `registry.redhat.io/ansible-automation-platform-25/ee-supported-rhel8:latest`, then ran the following command: `podman run --rm registry.redhat.io/ansible-automation-platform-25/ee-supported-rhel8:latest ansible --version`

Current ansible-core version was `ansible [core 2.16.19]`, so the required version of `community.general` is `9.5.13`. 

Created the `requirements.yml` file and added the content below:

```yaml
---
collections:
  - name: community.general
    version: "9.5.13"
```
Created a simple playbook the utilized an example from the `community.general.ini_file` module.

```yaml
---
- name: test the community.general collection by using one of its modules
  hosts: web
  tasks:
  - name: Add "beverage=lemon juice" is in section "[drinks]" in specified file
    community.general.ini_file:
      path: /tmp/beverages
      section: drinks
      option: beverage
      value: lemon juice
      mode: '0600'
      state: present
      exclusive: false

```



</details>