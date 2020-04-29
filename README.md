This repsitory stores Anwible training for A10 Thunder ADC. 
Almost all documents are written in Japanese.

Translated (online tools) to English

Exercise 1.1 - Configuring VLANs and Assigning Data Ports 
The number of
•	Purpose of this exercise
•	Create a Playbook that configures VLANs
•	Running the Playbook That Configures VLANs
•	Create and run a Playbook to delete VLANs の作成と実行
•	Create and run a Playbook that configures multiple VLANs の作成と実行
•	Create a Playbook to save the configuration
•	Run the Playbook to save the configuration
•	Create a Playbook that makes up a virtual interface
•	Running the Playbook that makes up the virtual interface
•	Check the parameters that you can set
The eye of this exercise
In this exercise,you will use the a10_network_vlan and a10_interface_ve_ip modules to configure the vThunder network. You will also learn how to add and remove configurations using Ansible, isobility, and how to configure multiple settings using with_items at the same time.  The a10_write_memory module also stores configuration information for vThunder. Save configuration information for
In this exercise, the networks connected to The on-the- ethernet 1 and ethernet 2 invThunder are different, and for convenience, you will allocate VLAN IDs The on-the- 10 and 20 for convenience. Communication between different networks within vThunder is forwarded.  
 
Create a Playbook that configures vlans
In the playbook directory of the Ansible execution server, create a playbook named In the directory、a10_network_vlan_create.yaml.  The editor uses vi here, but you can use macs, etc.  
[root@ansible playbook]# vi a10_network_vlan_create.yaml
First, write the following Playbook definition:  
---
- hosts: 10.255.0.1
  connection: local
gather_facts: no
  
•	The --- at the beginning of the file is written to indicate that this file is YAML.  
•	hosts: 10.255.0.1 indicates that this Playbook will run against vThunder.  
•	connection: local indicates that this Playbook runs locally (on anAnsible execution server) rather than on vThunder.  
•	gather_facts: no is to disable the collection of fact information.  VThunder Fact information cannot be collected because vThunder the Playbook runs locally.  
Next, you'll write the variables you want to use in the Playbook as vars.  If necessary, this variable can be described in an external file, etc.  
---
- hosts: 10.255.0.1
  connection: local
gather_facts: no

  vars:
    a10_host: "10.255.0.1"
  
Here, you specify the IP address of the vThunder administrative port as vThunder the variable a10_host.  
Then, describe what you want to do in the Playbook as tasks.  
---
- hosts: 10.255.0.1
  connection: local
gather_facts: no

  vars:
    a10_host: "10.255.0.1"
  tasks:
  - name: Create VLAN
    a10_network_vlan:
      a10_host: "{{ a10_host }}"
      a10_port: "{{ a10_port }}"
      a10_username: "{{ a10_username }}"
      a10_password: "{{ a10_password }}"
      a10_protocol: "{{ a10_protocol }}"
      vlan_num: "10"
      untagged_eth_list:
        - untagged_ethernet_start: "1"
          untagged_ethernet_end: "1"
      ve: "10"
      state: present
•	name: Create VLAN is a task description that displays this content when you run the Playbook.  
•	a10_network_vlan: indicates the name of the module used in the task. This is a module that configures VLANs for vThunder.  
•	a10_host: "{{ a10_host }}" is a module parameter that specifies which vThunder IP address the module connects to. Here you see the a10_host defined in vars above. 
•	a10_port: "{{ a10_port }}" is a module parameter that specifies the port of vThunder to which the module connects. Here yousee the a10_port defined in the inventory(hosts file).  
•	a10_username: "{{ a10_username }}" is a module parameter thatspecifies the user name to runthe vThunder aXAPI.  This section refers to the a10_username defined in the inventory(hosts file).  
•	a10_password: "{{ a10_password }}" is a module parameter thatspecifies a password for the user runningvThunder's aXAPI.  Here yousee the a10_password defined in the inventory(hosts file).  
•	a10_protocol: "{{ a10_protocol }}" is a module parameter thatspecifies the protocol to use when runningvThunder's aXAPI.  This section refers to the a10_protocol defined in the inventory(hosts file).  
•	vlan_num: "10" is a module parameter thatspecifies the IDENTIFICATION number of the VLAN to be configured in the a10_network_vlan.  
•	untagged_eth_list: is a parameter for a listed module thatsets the ethernet number corresponding to the VLAN that you want to configure in と a10_network_vlan, with the start and end numbers in The number of、untagged_ethernet_start and untagged_ethernet_end.  
•	ve: "10" is a module parameter thatspecifies the ID number of the virtual interface that corresponds to the VLAN that you want to configure in the a10_network_vlan.  Must be the same as the The on-the- IDENTIFICATION number of the VLAN.  
•	state: present is a module parameter that specifies that the configuration specified in this task is set on vThunder. specifies that it will be set on
a10_host、a10_port、a10_username、a10_password、a10_protocol、state、is common to almost all Ansible modules for A10 Thunder.
Now that you've written, save thePlaybook and return to the command line.  
Running the Playbook that makes up the VLAN
When you return to the command line, run the Playbook with the following command: Specify -i hosts in the command line options to load the inventory file.  
[root@ansible playbook]# ansible-playbook -i hosts a10_network_vlan_create.yaml
If there are no problems, you will get the following response: 
PLAY [10.255.0.1] *********************************************************************************************************************************

TASK [Create VLAN] ********************************************************************************************************************************
changed: [10.255.0.1]

PLAY RECAP ****************************************************************************************************************************************
10.255.0.1                 : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

The above indicates that the TASK CREATEVLAN was executed for host 10.255.0.1, the configuration 1 waschanged (changed),andone overall task succeeded(ok=1) and one configuration change was made(changed=1).) shows that was done (
Log in to vThunder to see if the configuration has actually changed.  
vThunder#show running-config
! Current configuration: 325 bytes
! Configuration last updated at 11:08:41 IST Thu Sep 12 2019
! Configuration last saved at 17:31:02 IST Wed Sep 11 2019
!64-bit Advanced Core OS (ACOS) version 4.1.4-GR1, build 78 (Jan-18-2019,16:02)
!
multi-config enable
!
terminal idle-timeout 0
!
vlan 10
  untagged ethernet 1
  router-interface ve 10
!
interface management
  ip address 10.255.0.1 255.255.0.0
  ip default-gateway 10.255.255.1
!
interface ethernet 1
!
interface ethernet 2
!
interface ve 10
!
!
sflow setting local-collection
!
sflow collector ip 127.0.0.1 6343
!
!
end
! Current config commit point for partition 0 is 0 & config mode is classical-mode
You can see thatvlan 10 is linked to the network interface, andthat the virtual interface(ve)is configured.  
Now let's run the same Playbook again on the Ansible execution server.  
[root@ansible playbook]# ansible-playbook -i hosts a10_network_vlan_create.yaml

PLAY [10.255.0.1] *********************************************************************************************************************************

TASK [Create VLAN] ********************************************************************************************************************************
ok: [10.255.0.1]

PLAY RECAP ****************************************************************************************************************************************
10.255.0.1                 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

Because the results are different from the previous results, and the same configuration is already on vThunder, it is confirmed that there is no configuration change(changed=0)and that it has already been configured there are no configuration changes ((ok=1). This module maintains this way, so youcan check the configuration described inthe Playbook without changing the configuration in a duplex. 
Create a Playbook to delete VLANs and Yes
Next, create a Playbook to remove this configuration. Copy and edit the created Playbooka10_network_vlan_create.yaml You can use the as a10_network_vlan_delete.yaml.  
[root@ansible playbook]# cp a10_network_vlan_create.yaml a10_network_vlan_delete.yaml
[root@ansible playbook]# vi a10_network_vlan_delete.yaml
Save the contents of the Playbook by rewriting the state from present to absent.  
---
- hosts: 10.255.0.1
  connection: local
gather_facts: no

  vars:
    a10_host: "10.255.0.1"
  tasks:
  - name: Delete VLAN
    a10_network_vlan:
      a10_host: "{{ a10_host }}"
      a10_port: "{{ a10_port }}"
      a10_username: "{{ a10_username }}"
      a10_password: "{{ a10_password }}"
      a10_protocol: "{{ a10_protocol }}"
      vlan_num: "10"
      untagged_eth_list:
        - untagged_ethernet_start: "1"
          untagged_ethernet_end: "1"
      ve: "10"
      state: absent
Try running this Playbook.  
[root@ansible example_playbook]# ansible-playbook -i hosts a10_network_vlan_delete.yaml

PLAY [10.255.0.1] *********************************************************************************************************************************

TASK [Delete VLAN] ********************************************************************************************************************************
changed: [10.255.0.1]

PLAY RECAP ****************************************************************************************************************************************
10.255.0.1                 : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

You VLAN can see that the VLAN was deleted and a configuration change was made. Let's check the vThunder settings.  
vThunder#show running-config
! Current configuration: 251 bytes
! Configuration last updated at 11:24:13 IST Thu Sep 12 2019
! Configuration last saved at 17:31:02 IST Wed Sep 11 2019
!64-bit Advanced Core OS (ACOS) version 4.1.4-GR1, build 78 (Jan-18-2019,16:02)
!
multi-config enable
!
terminal idle-timeout 0
!
interface management
  ip address 10.255.0.1 255.255.0.0
  ip default-gateway 10.255.255.1
!
interface ethernet 1
!
interface ethernet 2
!
!
sflow setting local-collection
!
sflow collector ip 127.0.0.1 6343
!
!
end
! Current config commit point for partition 0 is 0 & config mode is classical-mode
You can see that vlan 10 is no longer configured.  
In this way, you can specifystate: absent to achieve a state that does not have the configuration described in the Playbook.  
Create a Yes playbook that configures Playbook multiple VLANs and
Next, you'll create a playbook that sets up multiple VLAN VLANs, Set up a set ofnot just one VLAN. Copy and edit the created Playbooka10_network_vlan_create.yaml を as a10_network_vlans_create.yaml.  
[root@ansible playbook]# cp a10_network_vlan_create.yaml a10_network_vlans_create.yaml
[root@ansible playbook]# vi a10_network_vlans_create.yaml
Save the contents of the Playbook by rewriting it as follows:  
---
- hosts: 10.255.0.1
  connection: local
gather_facts: no

  vars:
    a10_host: "10.255.0.1"
  tasks:
  - name: Create VLAN
    a10_network_vlan:
      a10_host: "{{ a10_host }}"
      a10_port: "{{ a10_port }}"
      a10_username: "{{ a10_username }}"
      a10_password: "{{ a10_password }}"
      a10_protocol: "{{ a10_protocol }}"
      vlan_num: "{{ item.vlan_num }}"
      untagged_eth_list:
        - untagged_ethernet_start: "{{ item.untagged_ethernet_start }}"
          untagged_ethernet_end: "{{ item.untagged_ethernet_end }}"
      ve: "{{ item.ve }}"
      state: present
    with_items:
      - { vlan_num: 10, untagged_ethernet_start: 1, untagged_ethernet_end: 1, ve: 10 }
      - { vlan_num: 20, untagged_ethernet_start: 2, untagged_ethernet_end: 2, ve: 20 }
with_items allows you to assign multiple variables to perform iterations in a task. This examplesetsvlan IDs 10 10 and 20, and assigns ethernet 1 and 2 to virtual interfaces 10 and 20, , set up a virtual interface, andrespectively.  
When you run this Playbook, it looks like this:  
[root@ansible playbook]# ansible-playbook -i hosts a10_network_vlans_create.yaml

PLAY [10.255.0.1] *********************************************************************************************************************************

TASK [Create VLAN] ********************************************************************************************************************************
changed: [10.255.0.1] => (item={'vlan_num': 10, 'untagged_ethernet_start': 1, 'untagged_ethernet_end': 1, 've': 10})
changed: [10.255.0.1] => (item={'vlan_num': 20, 'untagged_ethernet_start': 2, 'untagged_ethernet_end': 2, 've': 20})

PLAY RECAP ****************************************************************************************************************************************
10.255.0.1                 : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
You can see that two configuration changes are made in a single task, and the configuration change has been made to ensure that the task is completed successfully. Let's check the vThunder settings.  
vThunder#show running-config
! Current configuration: 399 bytes
! Configuration last updated at 11:38:08 IST Thu Sep 12 2019
! Configuration last saved at 17:31:02 IST Wed Sep 11 2019
!64-bit Advanced Core OS (ACOS) version 4.1.4-GR1, build 78 (Jan-18-2019,16:02)
!
multi-config enable
!
terminal idle-timeout 0
!
vlan 10
  untagged ethernet 1
  router-interface ve 10
!
vlan 20
  untagged ethernet 2
  router-interface ve 20
!
interface management
  ip address 10.255.0.1 255.255.0.0
  ip default-gateway 10.255.255.1
!
interface ethernet 1
!
interface ethernet 2
!
interface ve 10
!
interface ve 20
!
!
sflow setting local-collection
!
sflow collector ip 127.0.0.1 6343
!
!
end
! Current config commit point for partition 0 is 0 & config mode is classical-mode
You can see that both VLAN 10 and 20 are configured.  
Try running the same Playbook again. 
[root@ansible playbook]# ansible-playbook -i hosts a10_network_vlans_create.yaml

PLAY [10.255.0.1] *********************************************************************************************************************************

TASK [Create VLAN] ********************************************************************************************************************************
ok: [10.255.0.1] => (item={'vlan_num': 10, 'untagged_ethernet_start': 1, 'untagged_ethernet_end': 1, 've': 10})
ok: [10.255.0.1] => (item={'vlan_num': 20, 'untagged_ethernet_start': 2, 'untagged_ethernet_end': 2, 've': 20})

PLAY RECAP ****************************************************************************************************************************************
10.255.0.1                 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

You can see that the equality is maintained here as well. 
Create a Playbook The Work ofto save the configuration
If you make a configuration change on Thunder, the results of the configuration change are immediately reflected, but the configuration content is not stored in thunder storage and will revert to the original configuration if you restart it. To save the results of a configuration change on thunder storage and start with the same settings the next timeyou start, you must use thea10_write_memory module to save the configuration changes. 
Run the following command on vThunder to try toview startup-config for startup:  
vThunder#show startup-config
! Current configuration: 517 bytes
! Configuration last updated at 17:30:59 IST Wed Sep 11 2019
! Configuration last saved at 17:31:02 IST Wed Sep 11 2019
!64-bit Advanced Core OS (ACOS) version 4.1.4-GR1, build 78 (Jan-18-2019,16:02)
!
multi-config enable
!
terminal idle-timeout 0
!
!
interface management
  ip address 10.255.0.1 255.255.0.0
  ip default-gateway 10.255.255.1
  exit-module
!
interface ethernet 1
  exit-module
!
interface ethernet 2
  exit-module
!
!
sflow setting local-collection
!
sflow collector ip 127.0.0.1 6343
!
!
end
As mentioned above, the configuration changes are not reflected, and if you restart as it is, you will start without vlan configuration. 
Now let's create a Playbook to save the current configuration(running-config)as astartup setting.  Createplaybooka10_write_memory.yaml in the playbook directoryof the Ansible execution server. Create a
[root@ansible playbook]# vi a10_write_memory.yaml
Save the Playbook as follows:  
---
- hosts: 10.255.0.1
  connection: local
gather_facts: no

  vars:
    a10_host: "10.255.0.1"
  tasks:
  - name: Write memory
    a10_write_memory:
      a10_host: "{{ a10_host }}"
      a10_port: "{{ a10_port }}"
      a10_username: "{{ a10_username }}"
      a10_password: "{{ a10_password }}"
      a10_protocol: "{{ a10_protocol }}"
      state: present
      partition: all
If you want to run this module, it must bestate: present.  Partition:all is also specified to store information for all logical partitions.  
The actual line of the Playbook where you want to save the configuration
When you run this Playbook, it looks like this:  
[root@ansible playbook]# ansible-playbook -i hosts a10_write_memory.yaml

PLAY [10.255.0.1] *********************************************************************************************************************************

TASK [Write memory] *******************************************************************************************************************************
changed: [10.255.0.1]

PLAY RECAP ****************************************************************************************************************************************
10.255.0.1                 : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

If you run this module, it will always be changed=1. になります  
Try to run the following command again onvThunder to displaystartup-configsettings for startup: 
vThunder#show startup-config
! Current configuration: 729 bytes
! Configuration last updated at 11:38:08 IST Thu Sep 12 2019
! Configuration last saved at 13:51:40 IST Thu Sep 12 2019
!64-bit Advanced Core OS (ACOS) version 4.1.4-GR1, build 78 (Jan-18-2019,16:02)
!
multi-config enable
!
terminal idle-timeout 0
!
vlan 10
  untagged ethernet 1
  router-interface ve 10
  exit-module
!
vlan 20
  untagged ethernet 2
  router-interface ve 20
  exit-module
!
!
interface management
  ip address 10.255.0.1 255.255.0.0
  ip default-gateway 10.255.255.1
  exit-module
!
interface ethernet 1
  exit-module
!
interface ethernet 2
  exit-module
!
interface ve 10
  exit-module
!
interface ve 20
  exit-module
!
!
sflow setting local-collection
!
sflow collector ip 127.0.0.1 6343
!
!
end
You can make sure that the running settings are saved as startup settings. 
Create a Playbook that makes up the Yes virtual interface
Then, to assign an IP address to the virtual interface,create a playbook named Playbook a10_interface_ve_ip_create.yaml in the playbook directoryof theAnsible execution server.  This Playbook And now、uses a10_interface_ve_ip as an Ansible module.  
[root@ansible playbook]# vi a10_interface_ve_ip_create.yaml
As you VLAN did earlier, assign IP addresses to multiple virtual interfaces in succession. Also, add tasks to save the configuration after the configuration change to run continuously.  
---
- hosts: 10.255.0.1
  connection: local
gather_facts: no

  vars:
    a10_host: "10.255.0.1"
  tasks:
  - name: Add IP to VE
    a10_interface_ve_ip:
      a10_host: "{{ a10_host }}"
      a10_port: "{{ a10_port }}"
      a10_username: "{{ a10_username }}"
      a10_password: "{{ a10_password }}"
      a10_protocol: "{{ a10_protocol }}"
      ve_ifnum: "{{ item.ve_ifnum }}"
      address_list:
        - ipv4_address: "{{ item.ipv4_address }}"
          ipv4_netmask: "{{ item.ipv4_netmask }}"
      state: present
    with_items:
      - { ve_ifnum: "10", ipv4_address: 192.168.1.254, ipv4_netmask: 255.255.255.0 }
      - { ve_ifnum: "20", ipv4_address: 192.168.2.254, ipv4_netmask: 255.255.255.0 }

  - name: Write memory
    a10_write_memory:
      a10_host: "{{ a10_host }}"
      a10_port: "{{ a10_port }}"
      a10_username: "{{ a10_username }}"
      a10_password: "{{ a10_password }}"
      a10_protocol: "{{ a10_protocol }}"
      state: present
      partition: all
•	ve_ifnum: "{{item.ve_ifnum }}" is a module parameter that specifies the ID number of the virtual interface(VE)）の to set ina10_interface_ve_ip.  
•	を address_list: is a parameter for a list-formatted module that specifies the IP address that you want to set in the virtual interface that you want to set in a10_interface_ve_ip, ipv4_address,and that netmask in ipv4_netmask.  
If you want to run multiple tasks in a row, writetasks: Write more than one task below
Now that you've written, save thePlaybook and return to the command line.  
The actual line of the Playbook that makes up the virtual interface
When you run this Playbook, it looks like this:  
[root@ansible playbook]# ansible-playbook -i hosts a10_interface_ve_ip_create.yaml

PLAY [10.255.0.1] *********************************************************************************************************************************

TASK [Add IP to VE] *******************************************************************************************************************************
changed: [10.255.0.1] => (item={'ve_ifnum': '10', 'ipv4_address': '192.168.1.254', 'ipv4_netmask': '255.255.255.0'})
changed: [10.255.0.1] => (item={'ve_ifnum': '20', 'ipv4_address': '192.168.2.254', 'ipv4_netmask': '255.255.255.0'})

TASK [Write memory] *******************************************************************************************************************************
changed: [10.255.0.1]

PLAY RECAP ****************************************************************************************************************************************
10.255.0.1                 : ok=2    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

You can see that the two tasks are executed continuously, thefirst task (AddIP to VE)has ip addresses set to virtual interfaces 10 and 20, andthe configuration that you changed in thesecond task (Writememory)has been saved as a startup setting.  
Let's check the vThunder settings. 
vThunder#show running-config
! Current configuration: 365 bytes
! Configuration last updated at 14:07:47 IST Thu Sep 12 2019
! Configuration last saved at 14:07:50 IST Thu Sep 12 2019
!64-bit Advanced Core OS (ACOS) version 4.1.4-GR1, build 78 (Jan-18-2019,16:02)
!
multi-config enable
!
terminal idle-timeout 0
!
vlan 10
  untagged ethernet 1
  router-interface ve 10
!
vlan 20
  untagged ethernet 2
  router-interface ve 20
!
interface management
  ip address 10.255.0.1 255.255.0.0
  ip default-gateway 10.255.255.1
!
interface ethernet 1
!
interface ethernet 2
!
interface ve 10
  ip address 192.168.1.254 255.255.255.0
!
interface ve 20
  ip address 192.168.2.254 255.255.255.0
!
!
sflow setting local-collection
!
sflow collector ip 127.0.0.1 6343
!
!
end
! Current config commit point for partition 0 is 0 & config mode is classical-mode
The IP addresses are set to 10 and 20 of the virtual interfaces (VE).  
Try to run the same Playbook again. 
[root@ansible playbook]# ansible-playbook -i hosts a10_interface_ve_ip_create.yaml

PLAY [10.255.0.1] *********************************************************************************************************************************

TASK [Add IP to VE] *******************************************************************************************************************************
ok: [10.255.0.1] => (item={'ve_ifnum': '10', 'ipv4_address': '192.168.1.254', 'ipv4_netmask': '255.255.255.0'})
ok: [10.255.0.1] => (item={'ve_ifnum': '20', 'ipv4_address': '192.168.2.254', 'ipv4_netmask': '255.255.255.0'})

TASK [Write memory] *******************************************************************************************************************************
changed: [10.255.0.1]

PLAY RECAP ****************************************************************************************************************************************
10.255.0.1                 : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

You can see that the IP address setting partibility to the virtual interface is maintained.  
Checking parameters that can be set
You have now configured the VLAN and assigned the data port. In the next exercise, you'll enable the data port to ensure communication with the client.  
If you want to see the parameters that you can set in the Ansible module, you can use the ansible-doc command below.  
[root@ansible playbook]# ansible-doc a10_network_vlan


