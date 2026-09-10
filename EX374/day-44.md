#### Understanding Ansible Automation Platform API
AAP provides a REST API that allows for the controlling of AAP from playbooks. Clients can perform `GET`, `POST`, `PUT` and `DELETE` methods on the AAP server. 

See example `GET` request below. 

```shell
[ansible@tower ~]$ curl -k -u "$username:$password" https://tower.cezeh.lab/api/controller/v2/ping/ | jq .
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   455  100   455    0     0   2676      0 --:--:-- --:--:-- --:--:--  2676
{
  "ha": false,
  "version": "4.6.30",
  "active_node": "tower.cezeh.lab",
  "instances": [
    {
      "node": "tower.cezeh.lab",
      "node_type": "hybrid",
      "heartbeat": "2026-09-10T01:14:49.320243Z",
      "capacity": 60,
      "version": "4.6.30"
    }
  ],
  "instance_groups": [
    {
      "name": "controlplane",
      "capacity": 60,
      "instances": [
        "tower.cezeh.lab"
      ]
    },
    {
      "name": "default",
      "capacity": 60,
      "instances": [
        "tower.cezeh.lab"
      ]
    }
  ]
}
```