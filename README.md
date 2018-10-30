# ansible-azure-provisioning

## Authenticating to your Azure account for Ansible

Connecting to your Azure account

The first thing to do is to create and connect to a service principal.

* Install the Azure-CLI on your ansible control host using this procedure: [Azure-Cli-Latest](https://docs.microsoft.com/en-us/cli/azure/?view=azure-cli-latest "Azure-CLI-Latest")

* Login to the Azure-CLI

> az login -u

This will produce a login that will return a jason object that containes two items you will need to gather for your credentials:

Microsoft Returned Value | Value used in credentials file
-------------------------| ------------------------------
id: | subscription_id
tenantId: | tenant:

Now, create a new service principal.   You will have to provide a new service principal name and and supply a new password:

| az ad sp create-for-rbac --name MeetupDemo --password Meetup123!

This will produce an "App Registration" entry under the Active Directory section of your Azure console.   It creates a service principal, and the json object returns two more items you need for your authentication file (secrets.yml) in these playbooks.

Microsoft Returned Value | Value used in credentials file
-------------------------| ------------------------------
appId: | client_id:
password: | secret:

So the end result should be this file, the template is in defaults/secrets.yml (you probably want to put your public key for VM access in here too):

![Credentials File Entries](/images/secrets.yml.png)

You should encyrpt the file using ansible-vault, or another method with which you feel comfortable.
> ansible-vault encrypt vars/secrets.yml  ( you will be prompted for a new vault password)

## Using the playbooks

To lay out the full sample infrastructure, use the following sequence in the command line, or with an Ansible Tower Workflow:

ansible-playbook az-cr-resource-group.yml --ask-vault-pass

ansible-playbook az-cr-network.yml --ask-vault-pass

ansible-playbook az-cr-storage-acct.yml --ask-vault-pass

ansible-playbook az-cr-securitygroups.yml --ask-vault-pass

ansible-playbook az-cr-publicip.yml --ask-vault-pass

ansible-playbook az-cr-nic.yml --ask-vault-pass

ansible-playbook az-cr-instance.yml --ask-vault-pass

## Using Ansible Tower

Enter your credentials into a "Microsoft Azure Resource Manager" credentials object from the WebGUI:

![Tower Credential File Entries](/images/tower-creds.png)

The secret is entered in the above image but is shown as "Encrypted"

## Azure SDK for Tower

You may need to install the Azure Python SDK into Tower.   Here is how that is doen:

Log into your tower instance, and gain root access.

Open the Python VirtualEnv:

| source /var/lib/awx/venv/ansible/bin/activate

You will see a prompt with a (venv) prefix such as: (ansible) [root@tower-3 ~]#

Now issue the following commands

| umask 0022

| pip install ansible[azure]

| deactivate

| exit

Tower will now be able to use the APIs within the Azure Infrastructure

## Credits

Jason Horn did an excellent job of outlining the credentials process, and I lifted his technique here:
[Jason Horn's Playbooks](https://github.com/hornjason/ansible-ocp-azure "Jason Horn's OCP Ansible Example")
