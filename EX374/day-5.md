#### Working with variables
The goal of working with variables is to bring good programming practices to Ansible code development. 
Program code needs to be portable, and specific information shouldn't be in the code for ease of reusability. 

##### Using variables in a playbook.
Variables are key, value pairs which make it easy to refer to the value in different places in the playbook. We define the pair once and use the key where needed in the playbook. When the value changes, we only need to update it once where it was defined.

Ansible also provides variables that are called facts, these are properties of the managed hosts that are discovered when the playbook is running. Facts discovery is on by default, and can be disabled if needed.

