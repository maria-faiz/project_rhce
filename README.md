# project_rhcse

create inventory and ansible.cfg file

1) inventory:

   - controller.example.com with variable ansible_connection=local
   - servera.example.com is a member of the dev host group
   - serverb.example.com is a member of the test host group
   - serverc.example.com is a member of the prod host group
   - serverd.example.com is a member of the balancers host group
   - The prod group is a member of the webservers host group

ansible-inventory -i inventory --graph

2)   ansible.cfg

   [defaults]
   inventory=/home/ansi-user/ansible/inventory
   remote_user=ansi-user
   #roles_path=
   #collections_path=

   [privilege_escalation]
   become=yes
   become_user=root


5) Test the connectivity by running adhoc command with ping module
       ansible all -m ping
