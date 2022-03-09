# Learn Ansible
I'm learning Ansible and taking notes to help me (and perhaps you) remember what "seemed important at the time, but is now sadly forgotten".

## Step 1 (Installation & Configuration)

Install ansible in Linux using the following command:
```
sudo dnf install ansible -y
```
or
```
sudo apt install ansible -y
```
Once Ansible is installed. We need a list of hosts to Automate (an inventory).

This list of hosts can live in /etc/ansible/hosts, or just be a file you call with the -i option.

e.g:
```
ansible -i inventory -m ping
```
We can create a dynamic inventory via a cloud provider cli (i.e. aws ec2 describe-instances --query "Reservations[*].Instances[*].{Address:PublicIpAddress}") or manually with a list of host-names or IP addresses.

Next, we need to set-up password less SSH access to out inventory of hosts, using our public key file. We can create Private and Public Key using the following command locally.
```
ssh-keygen -t rsa
```
Then copy the public key to each host in our inventory, using the following command locally.
```
ssh-copy-id -i ~/.ssh/id_rsa.pub USER@IPADDRESS
```
Now we should be able to connect to each host in our inventory using SSH without a password.
```
ssh USER@IPADDRESS
```
Finally, we can test ansible password-less connectivity to each host using the following command:
```
ansible all -m ping
```
## Step 2 (Write and Run a playbook)
Now that we have ansible installed, configured and tested, we will need to create a playbook.

Here is a working playbook, that does nothing more than connect to each device.

cat Ansible_Playbook_Template.yml
```
---
- hosts: all
  tasks:
```
We can run this playbook (or any other) with the following command:
```
ansible-playbook Ansible_Playbook_Template.yml
```
 Note: even though we have defined no tasks within your playbook. At the beginning of each play, Ansible executes by default an additional task that gathers information, referred to as facts, about the remote nodes. Because facts can be used on playbooks to better customize the behaviour of tasks, the fact-gathering task must happen before any other tasks are executed.

## Step 4 (Doing Useful Stuff In Playbooks)

A task is the smallest unit of action you can automate using an Ansible playbook. Playbooks typically contain a series of tasks that serve a goal. Ansible executes tasks in the same order they are defined inside a playbook. Modules offer shortcuts to execute operations that you would otherwise have to run as raw commands.

Ansible supports the use of variables to customise the execution of tasks. Variables can come from different sources, such as the playbook file or external files.

---
---
- hosts: all # The hosts directive that defines the targets for that play.
  vars: # The vars section of the playbook defines a list of variables that will be injected in the scope of that play.
    - var1: variable #
    - var2: /also/a/variable #
  tasks: # A task is the smallest unit of action you can automate using an Ansible playbook.
    - name: Task 1 - print variables # The name property defines the output that will be printed when that task is about to be executed.
      debug: # (The debug module) Modules offer shortcuts to execute operations that you would otherwise have to run as raw bash commands.
        msg: "I am a {{ var1 }}, I am {{ var2 }}" # Modules set of options and properties.

```
