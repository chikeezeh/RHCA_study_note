#### Manage execution environments - Drills

The drills that follow are to practice the following exam objectives;

- Build an execution environment
- Run playbooks in a execution environment
- Upload execution environments into automation hub
- Using execution environments in automation controller

###### Task 1 — Build a custom EE
In the repo, create an EE definition (e.g. under ee/) based on the default base image ee-supported-rhel8, with a galaxy requirements file listing the collections you actually use (community.general, community.crypto — the ones from the earlier blocks).Build it with ansible-builder and tag it (e.g. lab-ee:1.0).

<details>
<summary>Click to open solution</summary>

Installed `ansible-builder`, see [Key information for execution environment below](#key-information-for-execution-environment). 

This is the content of the `execution-environment.yml` file.

```yaml
version: 3
images:
  base_image:
    name: registry.redhat.io/ansible-automation-platform-25/ee-minimal-rhel8:latest
dependencies:
  ansible_core:
    package_pip: ansible-core==2.16.19
  galaxy: requirements.yml

```

Also, the `requirements.yml`:

```yaml
---
collections:
 - name: community.general
   version: "9.5.13"
 - name: community.crypto
   version: "2.22.3"

```

See below the content of the created image.

```shell
[ansible@tower ansible-lab (main)]$ podman run --rm -it localhost/lab-ee:1.0 ansible-galaxy collection list
/usr/lib64/python3.12/site-packages/cryptography/hazmat/backends/openssl/backend.py:8: CryptographyDeprecationWarning: You are using OpenSSL < 3.0. Support for OpenSSL < 3.0 is deprecated and will be removed in the next release. Please upgrade to OpenSSL 3.0 or later.
  from cryptography.hazmat.bindings.openssl import binding

# /usr/share/ansible/collections/ansible_collections
Collection        Version
----------------- -------
community.crypto  2.22.3
community.general 9.5.13

```


</details>

###### Task 2 — Run a playbook inside the EE, standalone

Run a playbook from inside the new EE via podman run (mount the playbook and inventory in) that uses a baked collection by FQCN — e.g. community.general.ini_file writing a file on a managed host.The key constraint: no collections/requirements.yml involved — the playbook's project must not install anything at runtime. If the run succeeds, the collection came from the EE, and nothing else.Prove: the run output showing the task succeeding, plus the command you ran (so the mounts and image are visible).

<details>

<summary> Click to open solution </summary>

I decided to use `ansible-navigator` for this, so installed it first `sudo dnf install ansible-navigator`.

Then created a new directory to test my previous playbook.

Inside the directory, created to files,




```shell

-rw-r--r--. 1 ansible ansible    263 Nov  4 11:19 ansible-navigator.yml
-rw-r--r--. 1 ansible ansible    114 Nov  4 11:16 inventory

[ansible@tower nav]$ cat ansible-navigator.yml
---
ansible-navigator:
  ansible:
    inventory:
      entries:
      - ./inventory
    cmdline: "--user ansible"
  execution-environment:
    container-options:
      - "--net=host"
    enabled: true
    image: localhost/lab-ee:1.0
    pull:
      policy: never


[ansible@tower nav]$ cat inventory
tower
[web]
frontend-1 ansible_host=vm1
frontend-2 ansible_host=vm2
[db]
vm3
[ubuntu]
frontend-3 ansible_host=vm4

```

Afterwards, ran the playbook using the command below.

`ansible-navigator run community-ini.yml`

</details>


##### Key information for execution environment.

1. You need to install the ansible-builder command.
```shell
# enable the repository.

# For AAP 2.4 / 2.5 on RHEL 8
sudo subscription-manager repos --enable=ansible-automation-platform-2.5-for-rhel-9-x86_64-rpms

# install ansible-builder
sudo dnf install -y ansible-builder
```
2. `podman` by default downloads images to your home directory, and if that gets filled up before a build, you will error out. I used the steps below to direct it to another mount point so it doesn't run out of storage.

```shell
# create tmp directories
mkdir -p /var/tmp/ee-build /var/tmp/podman-tmp

# create ENV variables that ansible-builder will use.
 export TMPDIR=/var/tmp/podman-tmp
 export TMP=/var/tmp/podman-tmp

#run the builder command with the following flags

ansible-builder build -f ee/execution-environment.yml -t lab-ee:1.0 -c /var/tmp/ee-build --container-runtime podman -v 3

-f --> path to execution file
-t --> the tag for out image
-c --> container tmp location

```