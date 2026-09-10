#### Understanding Ansible Automation Platform API contd.

AAP provides that graphical website to explore the API, go to `https://<towerhostname>/api` and you will get a website as shown below where you can interact with the API. 

![Tower Api](images/tower_api.jpg)

Also, you can make the output of the API more readable by either using `jq` or `json_pp`. 

```shell
[ansible@control ansible_work]$ curl -X GET https://tower.cezeh.lab/api/ -k | json_pp
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0Duplicate specification "V" for option "v"
100   147  100   147    0     0   4771      0 --:--:-- --:--:-- --:--:--  4900
{
   "apis" : {
      "controller" : "/api/controller/",
      "eda" : "/api/eda/",
      "galaxy" : "/api/galaxy/",
      "gateway" : "/api/gateway/"
   },
   "description" : "AAP gateway REST API"
}
```
Some, API request requires authentication, for example, we can make an API request to run a job template. The command for doing that is;

`curl -ks -X POST -u "$username:$password" https://tower.cezeh.lab/api/controller/v2/job_templates/${TEMPLATE_ID}/launch/ | jq .`

Here, `-k` Tells curl to skip SSL/TLS certificate validation. This allows the connection to succeed even if the target server uses a self-signed, untrusted, or expired certificate (common in internal .lab environments).

`-s` (or `--silent`): Enables silent/quiet mode. It suppresses progress meters, download statistics, and general status messages from being printed to the terminal.

`-X` Specifies the custom HTTP request method to send to the server (e.g., `POST`, `GET`, `PUT`, `DELETE`, `PATCH`).

`-u` (or `--user`): Specifies the username and password used for server authentication, formatted as `username:password`.

Use the command below to get the status of a job.

`curl -ks -X GET -u "$username:$password" https://tower.cezeh.lab/api/controller/v2/jobs/${Job_ID}/ | jq .`


