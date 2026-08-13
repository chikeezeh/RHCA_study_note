#### Securing ssh connections.

To better secure our ssh connections, we can add a passphrase to our ssh key when we generate it. However, this means we will need to put in that passphrase everytime ansible runs.
Use the following steps to cache the ssh passphrase to the current shell connection using ssh-agent;
- `ssh-keygen` to generate ssh keys, add a passphrase
- `eval ssh-agent $SHELL` to start ssh-agent
- `ssh-add ~/.ssh/id_rsa` to add your identity to the agent.

#### Understanding Tags

A tag is a label that can be use in a playbook to identify specific playbook elements.

Using tags, you can run specific parts of the playbook only by using `ansible-playbook --tag=xxx`

