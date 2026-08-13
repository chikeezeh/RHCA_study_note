#### Ansible Best Practices and optimization.

We can modularize playbooks as they grow in complexity. Roles play a role in this modularity, were we define the role separately from the playbook, and call it when needed.
Using `include` for dynamic processing, for example executing a task when a conditional is met, or `import` to pre-process code when a playbook is read.

Example below;

```yaml
# main.yml
- name: Setup Web Server
  hosts: webservers
  tasks:
    - name: Import base package tasks
      import_tasks: tasks/install_packages.yml
```

The example above, the `install_packages.yml` is imported and the content is used statically. 

```yaml
# main.yml
- name: Setup Web Server
  hosts: webservers
  tasks:
    - name: Include tasks conditionally based on OS
      include_tasks: "tasks/{{ ansible_os_family | lower }}_setup.yml"
```
In the example above, the file is loaded dynamically based on the `os_family`. So in our `tasks` directory, we will have playbooks specifically for the `os_family`.