#### Scheduling jobs.

Jobs and workflow templates can be scheduled to run at certain cadence. 
To accomplish this, go to `Automation Execution` click on `Schedules` then on `Create schedule` button. 

![Create schedule](images/create_schedule.jpg)

Pick the resource type, for this example we will go with `Job template`.

![resource type](images/resource_type.jpg)

Fill out the form and click on next. 

![Schedule form](images/schedule_form_1.jpg)

Go to `Rules` and enter how you want the schedule to behave. 

![Schedule Rules](images/schedule_rules.jpg)

Save the rules and click on next. 

You can add an exception, but skipping that for this test. 

Review the schedule then click finish to save it, address any errors that pop up.

![Schedule Review](images/schedule_review.jpg)

#### Importing Static Inventory to AAP.

The first step is to create the inventory name `controlinventory` in AAP, see image below.

![Create Inventory](images/create_inventory.jpg)

Move the inventory file to a directory that is readable to all users. 

```shell
cp ~/inventory /tmp/inventory
chmod 644 /tmp/inventory
```


Use `awx-manage` cli utility to do the import. 

`podman exec -it automation-controller-task awx-manage inventory_import --source=/tmp/inventory --inventory-name="controlinventory" --overwrite`


Before importing:

![Control before](images/control_before.jpg)

After importing:

![Control After](images/control_after.jpg)



