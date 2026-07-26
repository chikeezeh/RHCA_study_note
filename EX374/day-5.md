#### Working with variables
The goal of working with variables is to bring good programming practices to Ansible code development. 
Program code needs to be portable, and specific information shouldn't be in the code for ease of reusability. 

##### Using variables in a playbook.
Variables are key, value pairs which make it easy to refer to the value in different places in the playbook. We define the pair once and use the key where needed in the playbook. When the value changes, we only need to update it once where it was defined.

Ansible also provides variables that are called facts, these are properties of the managed hosts that are discovered when the playbook is running. Facts discovery is on by default, and can be disabled if needed.

##### Defining Variables.
- Define the variables in a playbook.
- Use include files to store variables externally to the playbook.
- Useing `register` to capture the output of a command or taks and saved to a variable.
- Using `var_prompt` to ask for input.
- Using `ansible-vault` for sensitive values.
- Host variables, that have been set of a host or group of hosts.

##### Variable Precedence
NOTE, the most specific level always wins in ansible.
- Command line (Highest precedence)
- Inventory file
- Playbook (using `set_facts`)