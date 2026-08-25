#### Plugin based Inventory.

Plugins allow ansible to use different type of formats for the inventory file. For example, using the `ini` plugin, we can utilize the old style of inventory. Also, `script` plugin can be used for scripts that query a source that produces host names that can be used in the inventory.

The example below is an `ini` inventory based file.

```ini
[webservers]
vm1
vm2
vm3

[aap]
tower

[ubuntu]
vm4

[allservers:children]
webservers
aap
ubuntu
```

The same inventory file can be written in `yaml` format to make it more readable.

```yaml
allservers:
  children:
    webservers:
      vm1
      vm2
      vm3
    aap:
      tower
    ubuntu:
      vm4
```
You can use the following command to convert from `ini` to `yaml` based inventory file. 

`ansible-inventory --yaml -i inventory --list --output inventory.yaml`



> [!CAUTION]
>$\color{red}{\text{Note; the above command will try to put the variables defined for groups and hosts in the inventory file, so make sure to verify that the variables are accurate.}}$

#### Inventory sources.

Use the command `ansible-doc -t inventory -l` to get a list of inventory sources available on the control node.

Sample output:

```shell
ansible-doc -t inventory -l
ansible.builtin.advanced_host_list Parses a 'host list' with ranges
ansible.builtin.auto               Loads and executes an inventory plugin specified in a YAML config
ansible.builtin.constructed        Uses Jinja2 to construct vars and groups based on existing inventory
ansible.builtin.generator          Uses Jinja2 to construct hosts and groups from patterns
ansible.builtin.host_list          Parses a 'host list' string
ansible.builtin.ini                Uses an Ansible INI file as inventory source
ansible.builtin.script             Executes an inventory script that returns JSON
ansible.builtin.toml               Uses a specific TOML file as an inventory source
ansible.builtin.yaml               Uses a specific YAML file as an inventory source
ansible@control:~/ansible_work$

```

The enabled plugins are found in the `[inventory]` section of the `ansible.cfg` in your project directory. The line to edit is;
`enable_plugins = host_list, script, ini, auto, yaml, ini, toml`

#### Fact Caching

To optimize the execution time of playbooks, we can cache the facts so that we don't need to gather facts everytime a play executes, hence the playbook runs faster. However, this means that we might have outdated facts for rapidly changing values suchs `"ansible_memfree_mb`. 

Example steps for fact caching.

Note, `redis` is no longer available for rhel10, however the steps will work for rhel9.

- install `redis` `sudo dnf install redis -y`
- start the redis service `sudo systemctl enable --now redis`
- Install the python dependency `pip3 install redis`
- edit the ansible.cfg file with;
    ```yaml
    fact_caching = redis
    fact_caching_timeout = 3600
    fact_caching_connection = localhost:6379:0

    ```

