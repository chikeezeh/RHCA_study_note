#### Manage task execution - drills

The drills to follow will cover the following exam objectives. 

- Control privilege escalation
- Run selected tasks from a playbook

We will be practicing the following Ansible concepts.
- `become_user`
- `become`
- `become_method`
- `tags`, specifically `--tags` and `--skip-tags`
  
###### Task 1 — the tagged playbook. 
Write site.yml for your `web` group (frontend-1, frontend-2) with three tags: `package`(install `httpd` — needs become), `config` (write a file to /etc/httpd/conf.d/custom.conf — needs become), `service` (start and enable httpd — needs become). Before running anything, write down which tasks you expect from each of these: `--tags package` / `--tags config,service` / `--skip-tags service` / no flags at all. Then run all four and compare against your predictions. Helpers: --list-tags and --list-tasks.

