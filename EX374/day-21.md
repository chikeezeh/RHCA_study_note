#### Using system roles.

Sytem roles are roles that are used for common tasks, in RHEL they are provided by the `rhel-system-roles.rpm` package or by using the `redhat.rhel_system_roles` collection.

The documentation for these roles include useful examples and can be found at;
- `/usr/share/doc/rhel-system-roles` if installed via the RPM package.
- `$COLLECTION_PATH/ansible_collections/fedora/linux_system_roles/docs` if installed using collections.

Installed the rhel-system-roles using the command below;
`sudo dnf install rhel-system-roles`

##### Content of the RHEL system roles.

```shell
ansible@control:/usr/share/ansible/collections/ansible_collections/redhat/rhel_system_roles/roles$ ls -l
total 132
drwxr-xr-x.  9 root root 4096 Aug 10 08:20 ad_integration
drwxr-xr-x.  8 root root 4096 Aug 10 08:20 aide
drwxr-xr-x.  9 root root 4096 Aug 10 08:20 bootloader
drwxr-xr-x.  8 root root 4096 Aug 10 08:20 certificate
drwxr-xr-x.  9 root root 4096 Aug 10 08:20 cockpit
drwxr-xr-x.  8 root root 4096 Aug 10 08:20 crypto_policies
drwxr-xr-x.  8 root root 4096 Aug 10 08:20 fapolicyd
drwxr-xr-x.  8 root root 4096 Aug 10 08:20 firewall
drwxr-xr-x.  7 root root 4096 Aug 10 08:20 gfs2
drwxr-xr-x. 10 root root 4096 Aug 10 08:20 ha_cluster
drwxr-xr-x.  9 root root 4096 Aug 10 08:20 journald
drwxr-xr-x.  9 root root 4096 Aug 10 08:20 kdump
drwxr-xr-x.  9 root root 4096 Aug 10 08:20 kernel_settings
drwxr-xr-x.  9 root root 4096 Aug 10 08:20 keylime_server
drwxr-xr-x.  6 root root 4096 Aug 10 08:20 logging
drwxr-xr-x.  7 root root 4096 Aug 10 08:20 metrics
drwxr-xr-x.  9 root root 4096 Aug 10 08:20 nbde_client
drwxr-xr-x.  8 root root 4096 Aug 10 08:20 nbde_server
drwxr-xr-x.  7 root root 4096 Aug 10 08:20 network
drwxr-xr-x.  8 root root 4096 Aug 10 08:20 podman
drwxr-xr-x.  9 root root 4096 Aug 10 08:20 postfix
drwxr-xr-x.  9 root root 4096 Aug 10 08:20 postgresql
drwxr-xr-x.  8 root root  109 Aug 10 08:20 private_logging_subrole_rsyslog
drwxr-xr-x.  8 root root  109 Aug 10 08:20 private_metrics_subrole_bpftrace
drwxr-xr-x.  7 root root   93 Aug 10 08:20 private_metrics_subrole_elasticsearch
drwxr-xr-x.  9 root root  122 Aug 10 08:20 private_metrics_subrole_grafana
drwxr-xr-x.  8 root root  109 Aug 10 08:20 private_metrics_subrole_keyserver
drwxr-xr-x.  9 root root  122 Aug 10 08:20 private_metrics_subrole_mssql
drwxr-xr-x.  9 root root  122 Aug 10 08:20 private_metrics_subrole_pcp
drwxr-xr-x.  6 root root   76 Aug 10 08:20 private_metrics_subrole_postfix
drwxr-xr-x.  8 root root  109 Aug 10 08:20 private_metrics_subrole_repository
drwxr-xr-x.  7 root root   93 Aug 10 08:20 private_metrics_subrole_spark
drwxr-xr-x.  8 root root 4096 Aug 10 08:20 rhc
drwxr-xr-x.  8 root root 4096 Aug 10 08:20 selinux
drwxr-xr-x.  7 root root 4096 Aug 10 08:20 snapshot
drwxr-xr-x.  8 root root 4096 Aug 10 08:20 ssh
drwxr-xr-x.  9 root root 4096 Aug 10 08:20 sshd
drwxr-xr-x.  7 root root 4096 Aug 10 08:20 storage
drwxr-xr-x.  8 root root 4096 Aug 10 08:20 sudo
drwxr-xr-x.  7 root root 4096 Aug 10 08:20 systemd
drwxr-xr-x.  9 root root 4096 Aug 10 08:20 timesync
drwxr-xr-x.  9 root root 4096 Aug 10 08:20 tlog
drwxr-xr-x.  9 root root 4096 Aug 10 08:20 vpn
```
