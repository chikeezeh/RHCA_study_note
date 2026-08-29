#### Ansible Tower / Ansible Automation Platform.
The training guide am following uses ansible Tower, but this has been replaced by ansible automation platform in RHEL10. However, I will still go through the presentations to get knowledge of how the web GUI is used, and try my best to translate it to ansible automation platform.

##### Understanding Users.
Users are the people that need to access the web interface. Role Based Access control (RBAC) is use to grant users access to specific roles. This can be assigned at the team or individual level. The level of access can grant users, view, use, change or deletion of ansible objects. 

##### Understanding Organizations.
This is a collection of teams, projects, and inventories. For a large company with different deployments, organizations allow for resources to be separated. Ansible tower comes with one organization called `default`. Note, `users` exist at a higher level so they can have roles in multiple `organizations`.

##### Understanding User Types.
- `System Administrator` has `read/write` access to the entire Tower installation.
- `system Auditor` has `read` only access to the entire installation
- `Normal users` has `minimal` access, and must be provided with access by adding roles to the user. 

##### Understanding Teams
A group of users make up a team, teams exist at an organizational level. System Administrator can assign `users` the `team` roles on resources in different `organizations`.

