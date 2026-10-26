#### Manage Controller and Automation Hub

The next set of tasks will focus on the features below for AAP controller and automation hub.

Controller core: 
- Projects (Git-backed), 
- Inventories (including constructed and smart inventories), 
- Credentials, 
- Job templates.

###### Task 1 — A second project that proves update-on-launch

Create a new project in automation controller (name it `ansible-lab-dev`) pointing at your existing GitHub repo, with Update Revision on Launch enabled.
Push a small, observable change to the repo after creating the project (e.g. a marker value in a playbook or template that will show up in job output).
Launch a job template from this project without manually syncing it first.
Prove: the job's output reflects the new commit — the update-on-launch did the sync, not you.

<details>

<summary> Click to open solution </summary>

New project created, note that you have to use the ssh cloning URL if using ssh keys to access the repo. 

![New Project](images/ansible-lab-dev-project.jpg)

Edited a playbook with minor change and re-launched it without manual sync, and it picked the new changed. 

</details>

###### Task 2 — Constructed inventory

Create a constructed inventory in controller (name it constructed-lab) whose source is your existing lab inventory.
Its constructed-inventory configuration must do two things: (a) create a group containing exactly the hosts whose inventory names start with frontend, 

and (b) compose a new variable on those hosts (name and value of your choice, e.g. a tier label).

Prove: the controller's inventory host view shows the group and its members, and a job template run against that group executes only on frontend-1 and frontend-2.

Used the Source variable below to create the constructed Inventory.

```yaml
plugin: ansible.builtin.constructed
strict: false
compose:
  tier: '''frontend'''
groups:
  frontend_hosts: inventory_hostname.startswith('frontend')
```

A new group called `frontend_hosts` was created that only has the two hosts with an `inventory_hostname` starting with `frontend`.

