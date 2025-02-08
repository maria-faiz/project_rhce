# 1. Ansible Installation and Configuration

* Install the ansible package on the control node
* Create `automation` user with `devops` password, the user should be allowed to execute any command without providing password to the prompt
* Create inventory on the control node at `/home/automation/plays/inventory`. Meet following requirements:
    * managed1.example.com should be a member of the proxy host group
    * managed2.example.com should be a member of the webservers host group
    * managed3.example.com should be a member of the webservers and database host group
    * managed4.example.com should be a member of the database host group
    * proxy and webservers belong to group named `public`
* Create a config file at `/home/automation/plays/ansible.cfg` with following requirements:
    * priviledged escalation is disabled by default
    * ansible should manage 8 hosts at a single time
    * use previously defined inventory file by default
    * uses `/var/log/ansible/execution.log` to save information related to playbook execution
    * roles path should include `/home/automation/plays/roles`
    * ensure that priviledge escalation method is set to `sudo`
    * do not allow ansible to ask for password when elevating privileges

#########################################################################

# 2. Ad-Hoc Commands

* Write a script that meets following requirements:
    * Generates a SSH keypair
    * Is stored at `/home/automation/plays/adhoc`
    * Uses ansible ad-hoc commands
    * Creates `automation` user on all inventory hosts, `devops` is used as its password
    * Populates SSH key to all inventory hosts, access of `automation` user to any of them is allowed without password
    * `automation` user is allowed to escalate privileges on all inventory hosts without providing password

Ensure that the script works properly when `automation` user executes it

#########################################################################

# 3. Archiving

* Create a playbook that meets following requirements:
    * Creates a gzip archive containing `/etc` and stores it at `/backup/configuration.gz` on the managed hosts
    * Is placed at `/home/automation/plays/archive.yml`
    * Runs against `all` host group 
    * Retrieves archives from the managed nodes and stores them at `/backup/<hostname>-configuration.gz` on the control node
    * User `automation` should be owner of `/backup` and everything underneath. Both on the managed hosts and the control node. Only owner and members of his group should be able to read and manage files inside. Anyone should be allowed to list contents of `/backup`.

#########################################################################

# 4. Group differentiation

* Create a playbook that meets following requirements:
    * Populates `/etc/motd` with text, its content depends on the group
    * `proxy` should use `Welcome to HAProxy server` as the message
    * `database` should use `Welcome to MySQL database` as the message
    * `webservers` should use `Welcome to Apache server` as the message
    * Is placed at `/home/automation/plays/motd.yml`

#########################################################################

# 5. Ansible Facts

* Create a playbook that meets following requirements:
    * Is placed at `/home/automation/plays/ansible_facts.yml` 
    * Runs against `proxy` group
    * Results in possiblity of getting a pair `name=haproxy` from ansible facts path `ansible_local.environment.application` after calling setup module

#########################################################################

# 6. Text Manipulation

* Create a playbook that customizes ssh configuration with following requirements:
    * Is placed at `/home/automation/plays/ssh_config.yml`
    * Is run against all servers
    * Disables X11 forwarding
    * Sets maxminal number of auth tries to 3

#########################################################################

# 7. Conditionals

* Create a playbook that meets following requirements:
    * Is placed at `/home/automation/plays/system_control.yml`
    * Runs against all hosts 
    * If a server has more than 1024MB of RAM, then use sysctl to set vm.swppiness to 10
    * If a server has less or equal to 1024MB of RAM exist with error message `Server has less than required 1024MB of RAM`
    * Configuration change should survive server reboots

#########################################################################

# 8. YUM repositories

* Create a playbook that meets following requirements:
    * Is placed at `/home/automation/plays/yum.yml`
    * Runs against `database` hosts 
    * Adds a definition of new yum repository
    * Enables this repository in yum
    * Enables GPG check for this repo, key can be found here `https://repo.mysql.com/RPM-GPG-KEY-mysql`
    * Sets description of the repo to `MySQL 5.7 Community Server`
    * Sets repo id to `mysql57-community`
    * Sets url of the repo to `http://repo.mysql.com/yum/mysql-5.7-community/el/6/$basearch/`

#########################################################################

# 9. Vault

* Create a file that meets following requirements:
    * Is placed at `/home/automation/plays/vars/regular_users.yml`
    * Is encrypted by Vault with id set to `users` and password to `eureka`
    * The file should contain a key `user_password`, its values should be set to `devops`

* Create a file that meets following requirements:
    * Is placed at `/home/automation/plays/vars/database_users.yml`
    * Is encrypted by Vault with password to `dbs-are-awesome`
    * The file should contain a key `db_password`, its values should be set to `devops`

* Create a file that meets following requirements:
    * Is placed at `/home/automation/plays/secrets/regular_users_password`
    * Contains `eureka` as the content

* Create a file that meets following requirements:
    * Is placed at `/home/automation/plays/secrets/database_users_password`
    * Contains `dbs-are-awesome` as the content

#########################################################################

# 10. User Accounts

You have been given defintions of variables for the task
```yml
---
users:
- username: adam
  uid: 2000
- username: greg
  uid: 2001
- username: robby
  uid: 3001
- username: xenia
  uid: 3002
...
```

Store a file with the above content at `/home/automation/plays/vars/users.yml`

Create a playbook that meets following requirements:
* Is placed at `/home/automation/plays/users.yml`
* Creates users whose id starts with `2` on webservers host group. Password should be taken from the variable stored at `/home/automation/plays/vars/user_password.yml` (created in previous exercise)
* Creates users whose id starts with `3` on database host group. Password should be taken from the variable stored at `/home/automation/plays/vars/user_password.yml` (created in previous exercise)
* Users should be part of supplementary group `wheel`
* Users' shell should be set to `/bin/bash`
* Password should use SHA512 hash format
* Each user should have a SSH key uploaded - use the key from the first exercise
* After running the playbook users should be able to SSH into their servers without providing password to prompt

#########################################################################

# 11. Software Packages

Create a playbook that meets following requirements:
* Is placed at `/home/automation/plays/software_packages.yml`
* Installs `tmux` on all targets
* Installs `tcpdump` and `lsof` on `database` group
* Installs `mailx` and `lsof` on `proxy` group

#########################################################################

# 12. Periodic jobs

Create a playbook that meets following requirements:
* Is placed at `/home/automation/plays/periodic_jobs.yml`
* Is executed against `proxy` group
* Schedules a periodic job which runs every hour on workdays. It should be executed by the `root`. Every time the job is span it adds separator `-----` which is followed by date and list of currently pluged devices below that. Please look at provided example. Make note of date format. Save the log to `/var/log/devices.log`. Set group and owner to the root user
* Uses `at` to schedule a job that is going to be executed after 1 minute and dumps output from `vmstat` to `/var/log/vmstat.log`. The file should be owned by automation user and group. The file should be recreated each time the playbook is executed. If the playbook is executed a second time within a minute second job should not be scheduled


Example entry from two hours might look like it is presented below
```
----- 09/21/21 21:11 -----
sda      8:0    0   64G  0 disk 
├─sda1   8:1    0  2.1G  0 part [SWAP]
└─sda2   8:2    0 61.9G  0 part /

----- 09/21/21 22:11 -----
sda      8:0    0   64G  0 disk 
├─sda1   8:1    0  2.1G  0 part [SWAP]
└─sda2   8:2    0 61.9G  0 part /
sdb      8:16   0    5G  0 disk 
```
#########################################################################

# 13. SWAP

Create a playbook that meets following requirements:
* Is placed at `/home/automation/plays/swap.yml`
* Runs against `database` group
* Creates a partition on sdb drive on the managed hosts of size between 1000MB-1100MB
* Uses this partition to extend available swap
* Ensures that the partition is part of the swap pool on boot

#########################################################################

# 14. System target

Create a playbook that meets following requirements:
* Is placed at `/home/automation/plays/system_target.yml`
* Runs against all managed hosts
* Sets target to `multi-user.target`
* Must be idempotent - subsequent execution of playbook shouldn't result in `changed` state

#########################################################################

# 15. Dynamic inventories

Create file in form of a dynamic inventory which meets following requirements:
* Is placed at `/home/automation/plays/scripts/dynamic_inventory`
* Contains definitions of 3 three groups - database, proxy, webservers
* Returns following hosts for `proxy` group
    * managed1.example.com
* Returns following hosts for `webservers` group
    * managed2.example.com
    * managed3.example.com
* Returns following hosts for `database` group
    * managed3.example.com
    * managed4.example.com
* Defines following vars for `database` group
    * `accessibility` with value `private`
* Defines following vars for `proxy` group
    * `accessibility` with value `public`
* Defines following vars for `managed2.example.com` host
    * `accessibility` with value `unknown`
* Returns json to stdout when called 
* Must be parsable by ansible

#########################################################################

# 16. Roles

1. Create a role called `apache` that meets following requirements:
* Is placed at `/home/automation/plays/roles/apache`
* Installs `httpd` and `firewalld` packages
* Allows ports 80 and 443 to be accessible through firewall
* Ensures that httpd and firewalld services are started at boot time
* Deploys an index page that presents following message: `Welcome, you have conntected to <fqdn>`

2. Create a playbook that meets following requirements:
* Is placed at `/home/automation/plays/apache.yml`
* Runs role apache against `webservers` hosts group

#########################################################################

# 17. Requirements

Create a requirements file that meets following objectives:
* Is placed at `/home/automation/plays/requirements.yml`
* Installs git role with following params:
    * Repository `https://github.com/geerlingguy/ansible-role-git`
    * Uses `git` as name of the role
    * Is checkout from tag `3.0.0`

After creating the file install the role at `/home/automation/plays/roles`

#########################################################################

# 18. Templating

Create a new folder named `templates` at `/home/automation/plays` and prepare there a template that is going to be used later to generete hosts file for each node from the inventory. General idea is described by the below scratch
```
127.0.0.1 localhost <local_short_name> <local_fqdn>
127.0.1.1 localhost
<ip_address_host1> <short_name_host1> <fqdn_host1>
<ip_address_host2> <short_name_host2> <fqdn_host2>
[...]
```
Set the name to `hosts.j2`

Create a playbook named `hosts.yml` that meets following requirements:
* Is placed at `/home/automation/plays/roles`
* Runs against all hosts
* Uses templating to populate `hosts.j2` created before to all hosts from the inventory
* After playbook execution it should be possible to reach from any node to a different one using ip, short name or fqdn

#########################################################################

# 19. System roles

Use NTP system role to configure all hosts time synchronization.
To achieve that create a playbook named `time.yml` that meets following requirements:
* Is placed at `/home/automation/plays`
* Uses `1.pl.pool.ntp.org` and `2.pl.pool.ntp.org` server pool to synchronize time
* Enables `iburst`
* Configure timezone as `UTC`

#########################################################################

# 20. Advanced SSH

Aim of this task is to make ssh-server listen on non-standard port on top of commonly used - `22`. To ensure that servers are secured properly firewall service must be running as well as selinux mode set to `enforcing`

Create a playbook named `ssh.yml` that meets following requirements:
* Ensures that firewalld is installed and running on boot
* Opens 20022 in firewall for incoming connections
* Modifies sshd config to listen on both 22 and 20022
* Is executed against `webservers` group
* Uses system role selinux to enable enforcing mode and allow connection on port 20022
* Reboots machines as the last task that playbook executes
* Ensure that during playbook execution at least one server is available, configure it to run sequentially
Ensure that after reboot firewalld service is running, selinux mode set to `enforcing` and the machine can be accessed by both ports

You can use below command to verify the result
```bash
ssh -p 22 managed4.example.com exit && ssh -p 20022 -o StrictHostKeyChecking=no managed4.example.com exit
```

#########################################################################

# 21. Networking

Create a playbook named `network.yml` at `/home/automation/plays` that configures eth2 interface on **managed1.example.com** and **managed4.example.com**

Meet following objectives:
* It defines new connection named `Internal`
* Addresses for hosts are defined as below, each having 24-bit mask 
    * `192.168.57.101` - managed1.example.com
    * `192.168.57.104` - managed4.example.com 
* Connection should be up on boot
* Type of the connection is set to ethernet
* Uses system role for it

#########################################################################

# 22. Extending facts

Write a playbook named `additional_facts.yml` that meets following requirements:
* Is placed at `/home/automation/plays`
* Gathers facts about all hosts
* Gets data related to installed packages on `database` belonging hosts
* Prints facts of each host to the console

#########################################################################

# 23. Extending facts

1. Create a script which is going to be used as a dynamic fact and and store it at `/home/automation/plays/files`. The script should print out what is disk usage of `/usr/share` folder. Once present on a machine after running setup module against it you should be able to see result as below

```
"ansible_facts": {
    [...]
    "ansible_local": {
        "usage": {
            "/usr/share": 11488
        }
    }
    [...]
```

2. Create a playbook that deploys the script to all machines. Ensure that group and owner is set to root but let anyone execute the script. Store the playbook at `/home/automation/plays/dynamic_facts.yml`

#########################################################################

# 24. Reachable hosts

Aim of this task is to write a dynamic inventory script that returns a given host only if it is reachable. 
The idea is to avoid attempts of interacting with a server that is shut off. 
Use script from 15th exercise as the base for development. 
Store the script at `/home/automation/plays/scripts/reachable_hosts`. 
You can use `ssh` or `ping` command to verify that a host responds.
Meet the same requirements in terms of defined hosts' variables as in 15th exercise

#########################################################################

# 25. Prompt

You were asked to write a playbook that creates account for new employees. The idea is to execute the playbook each time a new person joins the company. To ease the boarding process your playbook should ask the user for his username and password while executing. All the people that are going to execute the playbook are suppossed to be part of networking team. From time to time they will need to interact with nmcli tool but despite that they shouldn't have access to privileged commands

To achieve that create a playbook named `prompt.yml` at `/home/automation/plays` that meets following requirements:
* Asks for username of a new user
* Runs against all hosts
* Asks for his password - ensure that it is hidden while the user is typing it. The user should type the password twice
* Creates networking group
* Allows the networking group to call `nmcli` tool with `sudo` without password. Ensure that nmcli is the ONLY tool that the group is allowed to execute with root privileges
* Assigns the user to `networking` supplementary group
