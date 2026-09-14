#### Managing Windows with Ansible.

This isn't  required learning for the RHCA if skipping EX417, so this section will be light just to have this as a tool in my tool belt. I won't be doing any labs for this. 

##### Requirements for managing windows.
- All recent Windows are supported by Ansible.
- Remote management is required for Ansible, no ssh.
- The following variables are required.
```ini
[win:vars]
ansible_user=<user>
ansible_password=<passowrd>
ansible_connection=winrm
ansible_winrm_server_cert_validation=ignore
```
- For `ansible.cfg`, `[privilege_escalation]` isn't required, because the `ansible_user` is an administrative user. 

##### Useful Windows Modules

`ansible.windows.win_command`: runs a command (not idempotent)
`ansible.windows.win_shell`: runs powershell commands
`ansible.windows.win_package`: installs software from executables
`ansible.windows.win_feature`: Manages system roles or Windows features
`ansible.windows.win_update`: Updates Windows
`ansible.windows.win_reboot`: reboots Windows
`ansible.windows.win_format`: formats storage devices
