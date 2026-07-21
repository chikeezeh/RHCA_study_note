#### Requirement for managing remote hosts.
1. SSH setup already on VM2 and VM3.
2. Edited `/etc/sudoers` file on VM2 and VM3 to add a timestamp so that password is only asked once, and every other 60 minutes. This is a more secure way than doing the `NOPASSWD` option. 
```shell
sudo visudo
#then add the line below
Defaults timestamp_type=global,timestamp_timeout=60
```
3. 