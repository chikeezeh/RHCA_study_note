#### Manage content collections - drills

The following exam content will be practiced in the drills that follow;

- Create a content collection
- Install a content collection
- Publish a content collection

These drills will be performed on AAP. 

###### Task 1 — requirements.yml + first FQCN module. 

Add collections/requirements.yml to the repo pinning community.general to an exact version (if the newest release demands a newer ansible-core than 2.16, pin the newest that accepts 2.16 — discovering that is part of the task). Write a playbook that uses community.general.ini_file by its full FQCN to set a value in an INI file on the web hosts, and run it via a job template. Prove: job output, plus the INI file's contents on a web host.
