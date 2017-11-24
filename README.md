
# Custumer detail free project 

Ansible - ACI project repository. This is a skeleton structure for starting deployment with Cisco ACI fabric.

# Project structure

1. All the playbooks are in `plays` folder. Each play correspond to a User Story. The name of the play file uses this convention:
    * pb-aci-X.yml  where X is the name of the Use Case as it appears in Custumer requirement doc.
        
2. Each playbook is composed by roles, so playbooks can reuse same roles to deploy different User Stories. Roles are in `/roles` folder .
    
3. Each role has some task list in /tasks directory. Please be advised that default variables might be defined, per each role, in `/vars` folder

4. Each playbook reads configuration files (called SSoT in playbooks and roles) from the folder `/environment/aci/  **add here customer specific files, if variables are missing here the playbooks will fail!**
    
5. ACI_HOSTS is the file that manages all the IP:PORT address of `**SSH**` ACI connection. (do not point to HTTPS port!) 
       Append to this file the ACI IPs you want to control with Ansible.
       
6. ACI_HOSTS is (might be) grouped in groups that share same configs. 
   Those configs are specified in the `group_vars` folder that is sitting at project top level directory. Each file in `group_vars` folder    MUST be called  as a the group named specified in the ACI_HOSTS file. Ansible will automatically load those variables and settings and    make them available in Playbooks, roles, etc..


# Important

To launch the ansible playbooks see `launch_playbooks.txt`

-- 
Francesco Di Benedetto
