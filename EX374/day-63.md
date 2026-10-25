#### Manage content collections - drills

The following exam content will be practiced in the drills that follow;

- Create a content collection
- Install a content collection
- Publish a content collection

These drills will be performed on AAP. 

###### Task 1 — requirements.yml + first FQCN module. 

Add collections/requirements.yml to the repo pinning community.general to an exact version (if the newest release demands a newer ansible-core than 2.16, pin the newest that accepts 2.16 — discovering that is part of the task). Write a playbook that uses community.general.ini_file by its full FQCN to set a value in an INI file on the web hosts, and run it via a job template. Prove: job output, plus the INI file's contents on a web host.

<details>
<summary> Click to see solution </summary>

First step is to see what version of ansible is running in my execution environment. I grabbed the registry image from AAP, which was: `registry.redhat.io/ansible-automation-platform-25/ee-supported-rhel8:latest`, then ran the following command: `podman run --rm registry.redhat.io/ansible-automation-platform-25/ee-supported-rhel8:latest ansible --version`

Current ansible-core version was `ansible [core 2.16.19]`, so the required version of `community.general` is `9.5.13`. 

Created the `requirements.yml` file and added the content below:

```yaml
---
collections:
  - name: community.general
    version: "9.5.13"
```
Created a simple playbook the utilized an example from the `community.general.ini_file` module.

```yaml
---
- name: test the community.general collection by using one of its modules
  hosts: web
  tasks:
  - name: Add "beverage=lemon juice" is in section "[drinks]" in specified file
    community.general.ini_file:
      path: /tmp/beverages
      section: drinks
      option: beverage
      value: lemon juice
      mode: '0600'
      state: present
      exclusive: false

```



</details>

###### Task 2 — find the FQCN offline. 

Goal: generate an OpenSSH keypair on vm3. Using only ansible-doc on the control node (no web), find which collection and module does this. Add that collection to requirements.yml, write the playbook using the FQCN, run via controller. Prove: the ansible-doc output you used, job output, and the key files on vm3.

<details>
<summary> Click to see solution </summary>

Used the `community.crypto:2.22.3` collection to solve this. See the sample playbook below:

```yaml
---
- name: configure dummy ssh on vm3
  hosts: vm3
  tasks:
  - name: Generate an OpenSSH keypair with the default values (4096 bits, rsa)
    community.crypto.openssh_keypair:
      path: /tmp/id_ssh_rsa

```

</details>

###### Task 3 — two collections, version constraints. 
Extend requirements.yml so both collections carry version constraints (an exact pin for one, a range for the other). One playbook, one job: use a module from each collection in the same play. 

<details>
<summary> Click to see solution </summary>

See edit made to the requirements file below:

```shell
[ansible@tower ansible-lab (main)]$ cat collections/requirements.yml
---
collections:
  - name: community.general
    version: "9.5.13"
  - name: community.crypto
    version: ">=2.16.0,<3.0.0"

```

See the test playbook below:

```yaml
---
- name: test the community.general collection by using one of its modules
  hosts: web
  tasks:
  - name: Add "typeofmellons=pumpkin" is in section "[mellons]" in specified file
    community.general.ini_file:
      path: /tmp/fruits
      section: mellons
      option: typeofmellons
      value: pumpkin
      mode: '0600'
      state: present
      exclusive: false
  - name: Generate an OpenSSH keypair with the default values (4096 bits, rsa)
    community.crypto.openssh_keypair:
      path: /tmp/take2_id_ssh_rsa
```

</details>

###### Task 4 — FQCN refactor. 

Take a working playbook from an earlier drill (e.g., deploy_web.yml) and convert every module reference to FQCN — builtins included. Run the original and the refactor, prove identical behavior: same recap shape, same resulting files.

This one is pretty straightforward, see [deploy_web_mod](../playbooks/deploy_web_mod.yml) for solution playbook.

###### Task 5 — prove what the job actually sees. 

Add a diagnostic task (or small playbook) run through the controller that lists the collections visible inside the job's environment (ansible-galaxy collection list output captured and debugged). Prove: the job output shows both collections from your requirements.yml, at the versions you pinned — not just the ones baked into the default EE.

<details>
<summary> Click to see solution </summary>

See [verifycollections](../playbooks/verifycollections.yml) playbook for my implementation of the solution.

See the output from AAP below. This shows that the collections in the `requirements.yml` file are stored under `/runner/requirements_collections/ansible_collections`, while the collections in the execution environment are under `/usr/share/ansible/collections/ansible_collections`.

```shell

13
ok: [localhost] => {
14
    "commandoutput.stdout_lines": [
15
        "",
16
        "# /runner/requirements_collections/ansible_collections",
17
        "Collection                      Version     ",
18
        "------------------------------- ------------",
19
        "community.crypto                2.26.9      ",
20
        "community.general               9.5.13      ",
21
        "",
22
        "# /usr/share/ansible/collections/ansible_collections",
23
        "Collection                      Version     ",
24
        "------------------------------- ------------",
25
        "amazon.aws                      9.2.0       ",
26
        "ansible.controller              4.6.30      ",
27
        "ansible.eda                     2.10.0      ",
28
        "ansible.hub                     1.0.0       ",
29
        "ansible.netcommon               7.1.0       …

```

</details>


###### Task 6 — capstone. 

A new job template running a playbook from the repo that uses: requirements.yml with ≥2 collections, ≥3 collection modules by FQCN (builtins don't count), FQCN for every module call, and targets two different host groups.

<details>
<summary> Click to see solution </summary>

See the solution to the capstone in the [attached playbook](../playbooks/collectioncapstone.yml). I re-used previous plays and added a 3rd task to create a null file. 

</details>


#### Content collection create and publish - drills

The above drills only covered the install part of content collections. Will practice creating and publishing a content collection below. Will re-use some of the ansible contents created on [day-64](./day-64.md).

###### Task 1 — Scaffold the collection

Run ansible-galaxy collection init to create a collection named webtools under a namespace of your choice (use your GitHub username if you intend to publish to Galaxy, since Galaxy namespaces tie to provider accounts).Set the load-bearing galaxy.yml fields: namespace, name, version 1.0.0, description, license, repository.Package your existing work into it: the webapp role goes under the collection's roles/ directory, and the quoted_csv filter goes under plugins/filter/ in collection plugin layout.
Prove: the collection tree, and galaxy.yml showing namespace, name, and version.

<details>
<summary> Click to see solution </summary>

Created the initial scaffold with `ansible-galaxy collection init chikeezeh.webtools`

Edited the `galaxy.yml` file to contain the correct information. 

Then moved the `webapp` role created on day-64 and the filter plugin `quoted_csv` into the respective folders. See below the structure of the source folder for the collection. 

```shell
[ansible@tower ansible-lab (main %)]$ tree chikeezeh
chikeezeh
└── webtools
    ├── docs
    ├── galaxy.yml
    ├── meta
    │   └── runtime.yml
    ├── plugins
    │   ├── filter
    │   │   └── lab_filters.py
    │   └── README.md
    ├── README.md
    └── roles
        └── webapp
            ├── defaults
            │   └── main.yml
            ├── files
            ├── handlers
            │   └── main.yml
            ├── meta
            │   └── main.yml
            ├── README.md
            ├── tasks
            │   └── main.yml
            ├── templates
            │   └── quotecsv.j2
            ├── tests
            │   ├── inventory
            │   └── test.yml
            └── vars
                └── main.yml


```
Edited the `runtime.yml` file to add the minimum supported ansible version.

```shell
[ansible@tower ansible-lab (main %)]$ cat chikeezeh/webtools/meta/runtime.yml
---
# Collections must specify a minimum required ansible version to upload
# to galaxy
requires_ansible: '>=2.14.0'

```


</details>

###### Task 2 — Build it, install it locally, use it.

Build the collection with ansible-galaxy collection build and install the resulting tarball from local disk (no Galaxy/hub involved yet).Write a playbook that uses the collection's contents by FQCN: the role as <namespace>.webtools.webapp and the filter as <namespace>.webtools.quoted_csv in a template.

<details>
<summary> Click to see solution </summary>

Used the following command to build the collection.

`ansible-galaxy collection build chikeezeh/webtools/`

Post build, a tar file: `chikeezeh-webtools-1.0.0.tar.gz` was created.

Used `ansible-galaxy collection install chikeezeh-webtools-1.0.0.tar.gz -p collections` to install the collection. 

Used the playbook below to test the collection, note edited the task in the `roles/webapp/tasks/main.yml` to use FQCN. 

```yaml
---
- name: Use a custom created collection and its underlying role.
  hosts: frontend-1
  roles:
    - role: chikeezeh.webtools.webapp

```

</details>