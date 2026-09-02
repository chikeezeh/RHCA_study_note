#### Example of workflow template.

First we need to create two job templates, first one will be `installjob`, second one will be `removejob`. 

![Install Job](images/install_job.jpg)

![Remove Job](images/remove_job.jpg)

After creating the two job templates, we will create the workflow template. 

Under `Automation Execution`, select `Templates` then click on `Create template` and select `Create workflow job template`.

![Create Workflow](images/workflow_create.jpg)

Fill out the form as shown below;

![Workflow Form](images/workflow_form.jpg)

After creating th workflow template, go back to templates, select the workflow template and click on `View workflow visualizer`.

![Workflow visualizer](images/workflow_visualizer.jpg)

Click on `Add step` button.

I added the `installjob` template, and the `removejob` template will only run when the `installjob` succeeds. 

![Workflow flow](images/workflow_flow.jpg)

