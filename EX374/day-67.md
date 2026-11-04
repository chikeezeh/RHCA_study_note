#### Manage execution environments - Drills

The drills that follow are to practice the following exam objectives;

- Build an execution environment
- Run playbooks in a execution environment
- Upload execution environments into automation hub
- Using execution environments in automation controller

###### Task 1 — Build a custom EE
In the repo, create an EE definition (e.g. under ee/) based on the default base image ee-supported-rhel8, with a galaxy requirements file listing the collections you actually use (community.general, community.crypto — the ones from the earlier blocks).Build it with ansible-builder and tag it (e.g. lab-ee:1.0).





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