#### Advanced controller task - drills

###### Task 1 — Dynamic inventory script
Write an executable inventory script in the repo (e.g. inventory/cmdb.py) that reads hosts from a data file (e.g. inventory/cmdb.json) — the data file stands in for your CMDB/database. The script must support --list (full inventory JSON with _meta.hostvars) and --host <name> (that host's variables).Prove: --list output, --host output for one host, and ansible-inventory --graph against the script showing the groups.Then prove dynamism: add a host to the data file and show it appears in --list output with no change to the script.

<details>

<summary> Click to open solution </summary>

See below json file created for this; note for initial testing vm4 was not added. 

```json
{
  "frontend-1": {
    "ansible_host": "vm1",
    "groups": ["web"]
  },
  "frontend-2": {
    "ansible_host": "vm2",
    "groups": ["web"]
  },
  "vm3": {
    "ansible_host": "vm3",
    "groups": ["db"]
  },
   "vm4": {
    "ansible_host": "vm4",
    "groups": ["ubuntu"]
  }
}

```

See below the Python script for reading the json file and turning it into an ansible inventory, made the script executable after creating it. 

```python
#!/usr/bin/env python3
"""
Custom dynamic inventory script reading from cmdb.json.
Supports:
  --list          Outputs inventory with groups and _meta.hostvars
  --host <name>   Outputs variables for a specific host
"""

import argparse
import json
import os
import sys

DATA_FILE = os.path.join(os.path.dirname(os.path.abspath(__file__)), "cmdb.json")


def load_cmdb():
    try:
        with open(DATA_FILE, "r") as f:
            return json.load(f)
    except FileNotFoundError:
        sys.stderr.write(f"Error: Database file not found at {DATA_FILE}\n")
        sys.exit(1)
    except json.JSONDecodeError as exc:
        sys.stderr.write(f"Error parsing {DATA_FILE}: {exc}\n")
        sys.exit(1)


def build_inventory_list(data):
    inventory = {
        "_meta": {
            "hostvars": {}
        },
        "all": {
            "hosts": list(data.keys())
        }
    }

    for hostname, attributes in data.items():
        # Copy attributes so we don't mutate the source
        host_attrs = dict(attributes)
        groups = host_attrs.pop("groups", [])

        # Assign hostvars
        inventory["_meta"]["hostvars"][hostname] = host_attrs

        # Assign to groups
        for group in groups:
            if group not in inventory:
                inventory[group] = {"hosts": [], "vars": {}}
            if hostname not in inventory[group]["hosts"]:
                inventory[group]["hosts"].append(hostname)

    return inventory


def get_host_vars(data, hostname):
    if hostname in data:
        host_attrs = dict(data[hostname])
        host_attrs.pop("groups", None)
        return host_attrs
    return {}


def main():
    parser = argparse.ArgumentParser(description="Ansible Dynamic Inventory Script")
    group = parser.add_mutually_exclusive_group(required=True)
    group.add_argument("--list", action="store_true", help="List all hosts and groups")
    group.add_argument("--host", action="store", help="Get variables for a single host")

    args = parser.parse_args()
    data = load_cmdb()

    if args.list:
        output = build_inventory_list(data)
    elif args.host:
        output = get_host_vars(data, args.host)

    print(json.dumps(output, indent=2))


if __name__ == "__main__":
    main()

```

See below when you run the script with the `--list` option.

```shell
[ansible@tower ansible-lab (main %)]$ ./inventorydir/cmdb.py --list
{
  "_meta": {
    "hostvars": {
      "frontend-1": {
        "ansible_host": "vm1"
      },
      "frontend-2": {
        "ansible_host": "vm2"
      },
      "vm3": {
        "ansible_host": "vm3"
      },
      "vm4": {
        "ansible_host": "vm4"
      }
    }
  },
  "all": {
    "hosts": [
      "frontend-1",
      "frontend-2",
      "vm3",
      "vm4"
    ]
  },
  "web": {
    "hosts": [
      "frontend-1",
      "frontend-2"
    ],
    "vars": {}
  },
  "db": {
    "hosts": [
      "vm3"
    ],
    "vars": {}
  },
  "ubuntu": {
    "hosts": [
      "vm4"
    ],
    "vars": {}
  }
}
```

Also, using the `--graph` flag.

```shell
[ansible@tower ansible-lab (main %)]$  ansible-inventory -i inventorydir/cmdb.py --graph
@all:
  |--@ungrouped:
  |--@web:
  |  |--frontend-1
  |  |--frontend-2
  |--@db:
  |  |--vm3
  |--@ubuntu:
  |  |--vm4
```



</details>

###### Task 2 — Dynamic inventory in controller

Create an inventory in controller (name it dynamic-lab) with a source of type "Sourced from a Project" pointing at the script in your ansible-lab-dev project. 

Sync it.Prove: the controller's host list shows the script's hosts, and a job template run against dynamic-lab executes on them.

Then prove controller-side dynamism: edit the data file, push, re-sync the source, and show the new host appearing in the controller inventory.

See below the dynamic-lab source, reading from the python script.

![Dynamic Lab](images/dynamic-lab.jpg)

###### Task 3 — Workflow with an approval gate

Build a workflow job template (name it drill-workflow-approval) with three nodes: Job A → Approval → Job B. Job A and B can be simple existing playbooks (a config write and a verification read).Prove: launch the workflow and show it pausing at the approval node, then approve it in the UI and show Job B running to success. The workflow output must show all three nodes in order.

<details>

<summary>Click to open solution</summary>

Will use community.yml as Job A and configuressh.yml as the Job B. 

See below the visualizer.

![Approval Visualizer](images/drill-approval.jpg)

</details>

###### Task 4 — Workflow branching: success and failure paths

Build a second workflow (drill-workflow-branch) whose first node runs a job template that accepts an extra variable (e.g. force_fail, default false) and fails the play when it's true. The workflow must route the node's success edge to one job and its failure edge to a different job.Prove both paths with two launches: once with the variable false (success branch runs, failure branch does not), once with it true (failure branch runs, success branch does not). Both workflow outputs required.

<details>

<summary>Click to open solution</summary>

I created 3 simple playbooks, the main node which has a variable that will be set by survey prompt in the AAP UI.

```yaml
---
- name: playbook to practice fail branching
  hosts: localhost
  gather_facts: false
  tasks:
  - name: Use the failvalue
    debug:
      msg: " Hello World "
    failed_when: failvalue | bool

```

When the main node passes;

```yaml
---
- name: playbook to practice fail branching
  hosts: localhost
  gather_facts: false
  tasks:
  - name: Use when pass
    debug:
      msg: "My parent job passed"

```

When the main node fails;

```yaml
---
- name: playbook to practice fail branching
  hosts: localhost
  gather_facts: false
  tasks:
  - name: Use when failed
    debug:
      msg: "My parent job failed"
```

See images from AAI UI.

![Main flow](images/branchdrill.jpg)

---

![True Flow](images/branchtrue.jpg)

---

![Fail Flow](images/branchfail.jpg)

</details>

###### Task 5 — Schedules and a webhook notification

Create a one-time schedule on an existing job template set to fire ~15 minutes out, and show the resulting job in the Jobs list after it fires. 

Also create a recurring schedule (e.g. weekdays) and show it listed on the template's Schedules tab.
Create a webhook notification template pointing at a simple listener on tower (a one-liner HTTP server logging POSTs to a file), attach it to a job template for both success and failure, run the job, and show the listener log receiving the payload.
Prove: the fired scheduled job, the recurring schedule listing, and the listener log lines.

<details>
<summary> Click to open solution </summary>

For the one time schedule, created a schedule to run in 15 mins, and set it to 1 occurence. See image below.

![15 minuets](images/15minsschedule.jpg)

For the reoccuring schedule, created a schedule to run every day at 12 noon. See image below.

![Fail Daily](images/faildaily.jpg)

Finally, created a webhook under notifiers. 

![Webhook ](images/webhook.jpg)

Then, attached the webhook to the notifier of an existing `Job Template`.

![Main flow webhook](images/mainflowwebhook.jpg)

On the host itself, I ran a python online that listens for a post request, and writes to a file called `/tmp/webhook.log`.

```shell
python3 -c '
import http.server, sys

class H(http.server.BaseHTTPRequestHandler):
    def do_POST(self):
        length = int(self.headers.get("Content-Length", 0))
        body = self.rfile.read(length).decode("utf-8", errors="replace")
        with open("/tmp/webhook.log", "a") as f:
            f.write(f"\n--- Webhook received at {self.date_time_string()} ---\n")
            f.write(f"Headers:\n{self.headers}\nPayload:\n{body}\n")
        self.send_response(200)
        self.end_headers()
        self.wfile.write(b"OK\n")

http.server.HTTPServer(("0.0.0.0", 8888), H).serve_forever()
'
```

See example payload received when I ran the `Job Template`.

```shell
--- Webhook received at Sun, 01 Nov 2026 19:47:10 GMT ---
Headers:
Host: host.containers.internal:8888
User-Agent: Red Hat Ansible Automation Platform 4.6.30 (enterprise)
Accept-Encoding: gzip, deflate, br, zstd
Accept: */*
Connection: keep-alive
Content-Type: application/json
Content-Length: 576


Payload:
{"id": 372, "name": "drill-mainflow", "url": "https://platformhost/execution/jobs/playbook/372", "created_by": "admin", "started": "2026-11-01T19:47:07.150422+00:00", "finished": "2026-11-01T19:47:09.865586+00:00", "status": "successful", "traceback": "", "inventory": "lab", "project": "AAP drill project", "playbook": "mainworkflow.yml", "credential": "ansible_user", "limit": "", "extra_vars": "{\"failvalue\": \"false\"}", "hosts": {"localhost": {"failed": false, "changed": 0, "dark": 0, "failures": 0, "ok": 1, "processed": 1, "skipped": 0, "rescued": 0, "ignored": 0}}}

--- Webhook received at Sun, 01 Nov 2026 19:47:58 GMT ---
Headers:
Host: host.containers.internal:8888
User-Agent: Red Hat Ansible Automation Platform 4.6.30 (enterprise)
Accept-Encoding: gzip, deflate, br, zstd
Accept: */*
Connection: keep-alive
Content-Type: application/json
Content-Length: 405


Payload:
{"id": 374, "name": "drill-mainflow", "url": "https://platformhost/execution/jobs/playbook/374", "created_by": "admin", "started": "2026-11-01T19:47:58.228066+00:00", "finished": null, "status": "running", "traceback": "", "inventory": "lab", "project": "AAP drill project", "playbook": "mainworkflow.yml", "credential": "ansible_user", "limit": "", "extra_vars": "{\"failvalue\": \"true\"}", "hosts": {}}

--- Webhook received at Sun, 01 Nov 2026 19:48:01 GMT ---
Headers:
Host: host.containers.internal:8888
User-Agent: Red Hat Ansible Automation Platform 4.6.30 (enterprise)
Accept-Encoding: gzip, deflate, br, zstd
Accept: */*
Connection: keep-alive
Content-Type: application/json
Content-Length: 570


Payload:
{"id": 374, "name": "drill-mainflow", "url": "https://platformhost/execution/jobs/playbook/374", "created_by": "admin", "started": "2026-11-01T19:47:58.228066+00:00", "finished": "2026-11-01T19:48:00.970068+00:00", "status": "failed", "traceback": "", "inventory": "lab", "project": "AAP drill project", "playbook": "mainworkflow.yml", "credential": "ansible_user", "limit": "", "extra_vars": "{\"failvalue\": \"true\"}", "hosts": {"localhost": {"failed": true, "changed": 0, "dark": 0, "failures": 1, "ok": 0, "processed": 1, "skipped": 0, "rescued": 0, "ignored": 0}}}

```


</details>

###### Task 6 — Capstone: the full advanced stack

Assemble a workflow (drill-workflow-capstone) that runs against the dynamic-lab inventory, includes an approval node and both success/failure branches, has a schedule attached, and has the webhook notification attached.Prove: a full workflow run showing the approval gate and the success branch, the schedule listed on the workflow, and a notification payload in the listener log from the run.