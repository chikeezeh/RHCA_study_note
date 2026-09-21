#### Managing AWS with ansible 

##### Requirements for managing AWS with ansible.

At a high level, we need to create a `user` on AWS that will be used for managing aws with ansible. We need to create an `access key` for that user and attache the `admin access policy` when setting up their permissions. 


##### Accessing Instance from AWS 
You will need to copy over your access keys, and store in an encrypted vault file. 

Also, `AWS`requires Python Boto libraries installed on the control node, use `sudo pip3 install boto boto3`.

The instances need and SSH Key pair for remote access, on the EC2 dashboard, create key pairs and download/copy over to the control machine, save the `.pem` file to the `.ssh` directory in the `home` directory on your control machine. To use the key pair in your playbook, add `key_name:instancekey`.

##### Configuring EC2 vault-based Access

- Create a project directory
- Create a `vault` encrypted file containing the AWS credentials; `aws_access_key`, `aws_secret_key`. 
- Create a playbook to use the credentials, make sure to set the `region`. 
    ```yaml
    - name: Gather information about all instance
      ec2_instance_info:
        region: us-west-2
    ```
- Run the playbook with the `--ask-vault-pass` flag to be prompted for the vault password, or `--vault-password-file=vault-file` where `vault-file` contains unencrypted vault password. Make sure to secure the `vault` file with `chmod 600 vault-file`.
  