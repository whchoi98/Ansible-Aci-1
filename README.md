
# AS-Evry-IBM-Norwegian-Wood

Ansible - ACI project repository for IBM Evry

# Project structure

1. All the playbooks are in [plays](./plays) folder. Each play corresponds to a User Story. The name of the play file uses this convention:
    * pb-aci-X.yml  where X is the name of the Use Case as it appears in IBM requirement doc.
        
2. Each playbook is composed by roles, so playbooks can reuse same roles to deploy different User Stories
    
3. Each role has some task list in /tasks directory. Please be advised that default variables might be defined, per each role, in /vars folder

4. Each playbook reads configuration files (called SSoT) from the folder /environment/Norwegian/ (eg. /environment/Norwegian/lab/aci )  **add here customer specific files, if variables are missing here the playbooks will fail!**
    
5. ACI_HOSTS is the file that manages all the IP:PORT address of `*SSH*` ACI connection. (do not point to HTTPS port!) 
       Append to this file the ACI IPs you want to control with Ansible.
       
6. ACI_HOSTS is (might be) grouped in groups that share same configs. 
   Those configs are specified in the [group_vars](./group_vars) folder that is sitting at project top level directory. Each file in `group_vars` folder    MUST be called  as a the group named specified in the ACI_HOSTS file. Ansible will automatically load those variables and settings and    make them available in Playbooks, roles, etc..


# Important

To launch the ansible playbooks see [launch_playbooks.txt](./launch_playbooks.txt).

-- 
Francesco Di Benedetto


# Notes

Most roles use the 'aci_rest' module to interact with the APIC's REST API. Taking as an example the role where an external EPG is deployed:

```
# aci-create-extr-epg.yml

- name: Deploy an EPG               # name of the task
  aci_rest:                         # name of the used module
    hostname: '{{ aci_ip }}'            
    username: '{{ aci_username }}'     # parameters that the module takes
    password: '{{ aci_password }}'
    validate_certs: no                 
    path: api/node/mo/uni/tn-{{ item.tenant_name }}/out-{{ item.tenant_external_routed_network_name }}/instP-{{item.tenant_external_routed_network_epg_name}}.json   # this path contains some variables that are replaced with jinja templating. The variables' values are obtained in the YAML pre-defined file through the 'with_items' loop.
    method: '{{ method }}'     # the method is either POST or DELETE in this case. By default it's POST.
    content:              # this is the REST API's request's content. The indentation is critical as it indicates the nested level of the attribute.
        l3extInstP:
            attributes:
               name: "{{item.tenant_external_routed_network_epg_name}}"
           
  with_items: 
    - '{{ SSOT_tenant|aci_listify("tenant","external_routed_network","epg") }}'   
```

 'with_items' loops through the SSOT_tenant or, in some cases, the SSOT_fabric file (the path to which is given through the command line, see [Important](#important)). The aci_listify filter flattens the YAML indentation. For example, given the following file:

```
# Customer01.yml file

---
  tenant:
    - name: Customer01
      external_routed_network:
        - name: l3out
          vrf: Customer01_vrf_1
          external_routed_domain: 99_router_01
          epg: 
            - name: pippo
```
'aci_listify("tenant","external_routed_network","epg")' makes it so the epg name attribute is recognised as 'tenant_external_routed_network_epg_name'.
