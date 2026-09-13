#### Lab for advanced AAP

##### Question:

Create a `Job Template Worklow` that meets the following requirements:
- The primary job to be used creates a user with a password.
- Configure a variables prompt, where the user needs to input username as well as password.
- It is allowed but not required to use a properly encrypted password
- If the job executes successfully, another job should run that creates the directory `/${USER}files` and sets the user you created as owner to that directory
- If the job fails to execute, a dedicated job should run that prints the message `"failed to create a user"`.

##### Solution:
We need 3 playbooks, the simplest to create is the [error message playbook](../playbooks/lab_fail.yaml).



