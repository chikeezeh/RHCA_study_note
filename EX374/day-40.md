#### Configuring my Ansible Automation Platform (AAP) to run from my github

These are the steps I used to get my AAP to utilize playbooks stored in my git repository.

- Created a directory in my existing repository to hold my playbooks. 
- On AAP web gui, go to `Automation Execution`  &rarr; `Projects` and click on `Create project` button.
![Create Project](images/create_project.jpg)
- Fill out the form that shows up, select `Git` as the `Source control Type`, and paste the `url` of the git repository into the `Source control URL` link. Then click on `Create project`
![Project Form](images/project_form.jpg)


To use the above created project, head back to job template you want to use, and edit it to use the project. While editing this, you can pick the playbook you want from the `Playbook` dropdown as shown below;

![Project Template](images/project_template.jpg)
