#### Using Delegation
Delegation lets you run tasks on another hosts. The `delegate_to` task property is used to achieve this functionality. This is useful when you want to write a play that affects multiple hosts, but you want a specific task to run on a specific host without starting a new play. 

#### Requirement for delegation
1. delegation host needs to meet the requirements of the control host.
   1. Python is installed.
   2. SSH access to managed hosts is enabled.
   3. SSH keys are copied over.
   4. Hostname to IP address resolution works.
2. `delegate_to` host must exist in the inventory on the control host.