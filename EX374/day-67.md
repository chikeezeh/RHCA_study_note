#### Manage execution environments - Drills

The drills that follow are to practice the following exam objectives;

- Build an execution environment
- Run playbooks in a execution environment
- Upload execution environments into automation hub
- Using execution environments in automation controller

###### Task 1 — Build a custom EE
In the repo, create an EE definition (e.g. under ee/) based on the default base image ee-supported-rhel8, with a galaxy requirements file listing the collections you actually use (community.general, community.crypto — the ones from the earlier blocks).Build it with ansible-builder and tag it (e.g. lab-ee:1.0).

