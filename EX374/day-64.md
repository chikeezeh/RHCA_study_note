#### Capstone on previous exam objectives.

`roles` are not listed as exam objectives, however they are ground knowledge assummed by the exam. 
This capstone will use `roles` as a vehicle to practice the following objectives.

- Understand and use Git

- Manage inventory variables

- Transform data with filters and plugins

- Delegate tasks

- Manage content collections / Manage automation controller


Task 1 — Scaffold the `common` role.

From the project root, use ansible-galaxy role init to create a role named `common` under roles/. 
The role must: define a default variable common_env_name: development in the role's defaults, and deploy `/tmp/common-marker.txt` from a role template that renders that variable (template lives in the role's templates/).Prove: a throwaway playbook applying only this role to frontend-1, run output, and cat /tmp/common-marker.txt on frontend-1 showing development. Rerun must be changed=0.


