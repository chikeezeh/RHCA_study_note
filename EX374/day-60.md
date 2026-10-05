#### Putting the previous drills on Ansible Automation Platform.

Capstone: a tagged multi-role playbook launched through automation controller (project → inventory → credentials → job template). This will be broken down into separate tasks. 

###### Task 1 — push the code. 

Push your tagged playbook (site.yml) to the private GitHub repo, top level of the repo. Tell me the playbook's path in the repo once it's up.

<details>
<summary> Click to open solution </summary>

First step is to move all the files created in the previous drills to the repo directory.

```shell
[ansible@tower drill2]$ cp -r ./* /home/ansible/dev/ansible-lab/
[ansible@tower drill2]$ tree /home/ansible/dev/ansible-lab/
/home/ansible/dev/ansible-lab/
├── anotherfeature
├── config.txt
├── deploy.yml
├── featurefile
├── firstfile
├── group_vars
│   ├── all
│   │   ├── common.yml
│   │   └── security.yml
│   ├── db
│   │   └── config.yml
│   └── web
│       ├── config.yml
│       └── packages.yml
├── host_vars
│   ├── frontend-1
│   │   ├── app.yml
│   │   └── tuning.yml
│   ├── frontend-2
│   │   └── tuning.yml
│   ├── vm3
│   │   └── tuning.yml
│   └── vm4
│       └── app.yml
├── inventory
├── README.md
├── remotefile
├── secondfile
├── site.yml
├── task2.yml
└── testgroupvars.yml

9 directories, 22 files

```
Remove some of the files not needed for this project. 

```shell
[ansible@tower ansible-lab (main %)]$ rm -f task2.yml
[ansible@tower ansible-lab (main %)]$ rm -f testgroupvars.yml
```

Commit the changes and push to github private repo.

```shell
git add .
git commit -m "Added files that will be needed for ansible aap drills"
git push origin main
```

</details>