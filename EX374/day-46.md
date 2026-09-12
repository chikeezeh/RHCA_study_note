#### How to backup AAP
Since I am using a containerized installation, to do the backup, navigate to the install directory (`/home/ansible/ansible-automation-platform-containerized-setup-2.5-25`). Then run the command below;

`ansible-playbook -i inventory ansible.containerized_installer.backup`


Once the playbook is done running, it will create a backup directory as shown below;

```shell
[ansible@tower ansible-automation-platform-containerized-setup-2.5-25]$ ll
total 420
-rw-r--r--. 1 ansible ansible 367133 Sep 11 17:59 aap_install.log
-rw-r--r--. 1 ansible ansible    146 Jun 18 08:50 ansible.cfg
drwxr-x---. 2 ansible ansible   4096 Sep 11 17:59 backups
drwxr-xr-x. 3 ansible ansible     33 Jun 18 08:50 collections
-rw-r--r--. 1 ansible ansible   4065 Jul 28 06:54 inventory
-rw-r--r--. 1 ansible ansible   3409 Jul 28 06:51 inventory.bk
-rw-r--r--. 1 ansible ansible  44253 Jun 18 08:50 README.md
```
The backups directory will contain compressed files as shown below;

```shell
[ansible@tower backups]$ ll
total 857512
-rw-r--r--. 1 ansible ansible  11617227 Sep 11 17:59 controller_tower.cezeh.lab.tar.gz
-rw-r--r--. 1 ansible ansible   1407341 Sep 11 17:59 eda_tower.cezeh.lab.tar.gz
-rw-r--r--. 1 ansible ansible   1177624 Sep 11 17:59 gateway_tower.cezeh.lab.tar.gz
-rw-r--r--. 1 ansible ansible 860387017 Sep 11 17:59 hub_tower.cezeh.lab.tar.gz
-rw-r--r--. 1 ansible ansible   1137954 Sep 11 17:59 postgresql_tower.cezeh.lab.tar.gz
-rw-r--r--. 1 ansible ansible   1136677 Sep 11 17:59 receptor_tower.cezeh.lab.tar.gz
-rw-r--r--. 1 ansible ansible   1133628 Sep 11 17:59 redis_tower.cezeh.lab.tar.gz
```
Note that while running the backup command, we can pass variables in the command line to change where the backups are stored. 

```shell
ansible-playbook -i inventory ansible.containerized_installer.backup -e "backup_dir=/var/backups/aap"
```

#### Restoring from backups

Stop the active containers to prevent write conflicts during the database restore:

`podman pod stop --all`

Verify no AAP service containers are actively writing to the databases:
`podman ps`, this should be empty.

Run the containerized installer's restore playbook against your inventory file, passing the backup location using backup_dir:

```shell
ansible-playbook -i inventory ansible.containerized_installer.restore \
-e "backup_dir=/path/to/installer/backups/<backup_folder_or_name>"
```

Example:

```shell
ansible-playbook -i inventory ansible.containerized_install.restore -e "backup_dir=/home/ansible/ansible-automation-platform-containerized-setup-2.5-25/backups"
```
The above command will be run from the install directory:
`/home/ansible/ansible-automation-platform-containerized-setup-2.5-25/`



