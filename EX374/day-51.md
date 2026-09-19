#### Managing AWS with ansible 

##### Requirements for managing AWS with ansible.

At a high level, we need to create a `user` on AWS that will be used for managing aws with ansible. We need to create an `access key` for that user and attache the `admin access policy` when setting up their permissions. 


##### Accessing Instance from AWS 
You will need to copy over your access keys, and store in an encrypted vault file. 

Also, `AWS`requires Python Boto libraries installed on the control node, use `sudo pip3 install boto boto3`.

The instances need and SSH Key pair for remote access, on the EC2 dashboard, create key pairs and download/copy over to the control machine, save the `.pem` file to the `.ssh` directory in the `home` directory on your control machine. To use the key pair in your playbook, add `key_name:instancekey`.
