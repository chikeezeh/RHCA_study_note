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

Note; the above command will try to put the variables defined for groups and hosts in the inventory file, so make sure to verify that the variables are accurate. 

> [!CAUTION]
>$\color{red}{\text{Note; the above command will try to put the variables defined for groups and hosts in the inventory file, so make sure to verify that the variables are accurate.}}$

