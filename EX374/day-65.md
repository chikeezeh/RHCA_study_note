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