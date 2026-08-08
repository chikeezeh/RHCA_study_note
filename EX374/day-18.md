#### Using Roles
Roles can be compared to functions in programming languages, they are pre-packaged tasks that can be re-used. 
Roles need to be installed to be used. 
They are typically installed in;
- `/usr/share/ansible/roles`
- `/etc/ansible/roles`
- `./roles` in the current working directory. 

Alternatively, you can edit where roles are installed by changing the `roles_path` options in `ansible.cfg` file.

By default, roles are executed before all the tasks in the playbook. To force tasks to be executed before the roles, use the `pre_tasks` option. To force tasks to be executed after the roles, use the `post_tasks` option.

##### Installing Roles

- from ansible galaxy official website: `ansible-galaxy role install geerlingguy.nginx`
- Install to a `roles` directory in the current working directory `ansible-galaxy role install geerlingguy.nginx -p roles`

##### Important roles commands
- `ansible-galaxy role install <name of role>` Install a role using the name.
- `ansible-galaxy role list` List the roles installed on the control node.
- `ansible-galaxy search <keyword>` Search for a role keyword from `galaxy.ansible.com`

