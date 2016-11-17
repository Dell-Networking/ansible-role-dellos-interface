Interface Role for Dell EMC Networking OS
=========================================

This role facilitates the configuration of interface attributes. It supports the configuration of admin state, description, MTU, IP address, IP helper, and port mode.
This role is abstracted for OS6, OS9, and OS10.

Installation
------------

```
ansible-galaxy install Dell-Networking.dellos-interface
```

Requirements
------------

This role requires an SSH connection for connectivity to your Dell EMC Networking OS device. You can use any of the built-in Dell EMC Networking OS connection variables, or the ``provider``
dictionary.

Role Variables
--------------

``dellos_interfaces`` (dictionary) contains the hostname (dictionary).
The hostname is the value of the variable ``hostname`` that corresponds to the name of the OS device.
This role is abstracted using the variable ``ansible_net_os_name`` that can take the following values: dellos6, dellos9 and dellos10.

Any role variable with corresponding state variable setting to *absent* negates the configuration of that variable. 
For variables with no state variable, setting empty value to the variable negates the corresponding configuration.
The variables and its values are **case-sensitive**.

The hostname (dictionary) holds a dictionary with the interface name. The interface name can correspond to any of the valid OS interfaces with the unique interface identifier name.

For physical interfaces, interface name format must be in the format `<interfacename> <tuple>`. For logical interfaces, the format is `<logical_interfacename> <id>`.
For example, the physical interface name can be ethernet 1/1/1 for OS10 devices, fortyGigE 1/1 for OS9 devices, or Te1/0/1 for OS6 devices.
The logical interface names can be Vlan 1 or Port-channel 12 for OS9 devices, vlan 1 or port-channel 12 for OS10 and OS6 devices.

Note: Only supported variables for the interface type should be defined. (e.g) The variable "switchport" should not be defined for logical interface.

**interface name** (dictionary) holds the following keys:

|        Key | Type                      | Notes                                                                                                                                                                                     |
|-----------:|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|   desc  | string         | Configures a one line description for the interface. |
| portmode | string |Configures portmode according to the type of the OS device.  OS9 devices supports portmode hybrid. OS10 and OS6 devices support portmodes access and trunk|
| switchport | boolean: true,false*  | Used to configure an interface in layer 2 mode. For OS10 devices the default switchport mode is *access*.  |
| admin      | string, choices: up, down*              |Configures the administrative state for the interface. Configuring the value as up administratively enables the interface; configuring the value as down administratively disables the interface. |
| mtu        | integer                       |Configures the MTU size for layer 2 and layer 3 interfaces. For example, the MTU is in the range of 594-12000 for OS9 devices and 1280-65535 for OS10 devices. For OS6 devices, MTU is set globally. |
| fanout     | boolean: true/false           |Configures fan-out on the port when set to true.                 |
|ip_and_mask | string |Configures specified IP address to the interface on OS9 and OS10 devices. Configures specified IP address to the interface VLAN for OS6 devices. The value should be in the following format: 192.168.11.1/24 |
|ipv6_and_mask | string |Configures specified IPv6 address to the interface on OS9 and OS10 devices. Configures specified IPv6 address to the interface VLAN for OS6 devices. The value should be in the following format: 2001:4898:5808:ffa2::1/126 |
| state_ipv6     | string, choices: absent,present*           | This key is valid only for OS10 devices. The key with value *absent* deletes the IPv6 address of the interface.                 |
| ipv6_reachabletime       | integer                       | Configures the reachability time for IPv6 neighbor discovery. The value is in the range of 0-3600000. For OS10 devices, this variable is not supported. |
| ip_helper | list | This key contains objects to configure DHCP server address. See the following ip_helper.* keys for each list item. |
| ip_helper.ip | string (required)         | Configures the IPv4 address of the DHCP Server. The value must be in the form of A.B.C.D  |
|    ip_helper.state | string, choices: absent, present* | The absent setting deletes the IP helper address.                                                                                                                                 |

```
Note: Asterisk (*) denotes the default value if none is specified.
```

Connection Variables
--------------------

Ansible Dell EMC Networking roles require the following connection information to establish 
communication with the nodes in your inventory. This information can exist in
the Ansible group_vars or host_vars directories, or in the playbook itself.

|         Key | Required | Choices    | Description                              |
| ----------: | -------- | ---------- | ---------------------------------------- |
|        host | yes      |            | The host name or address for connecting to the remote device over the specified *transport*. The value of *host* is the destination address for the transport. |
|        port | no       |            | The port used to build the connection to the remote device.  If the task does not specify the value, the port value defaults to 22. |
|    username | no       |            | Configures the username that authenticates the connection to the remote device. The value of *username* authenticates the CLI login. If the task does not specify the value, the value of environment variable ANSIBLE_NET_USERNAME is used instead. |
|    password | no       |            | Specifies the password that authenticates the connection to the remote device. If the task does not specify the value, the value of environment variable ANSIBLE_NET_PASSWORD is used instead. |
|   authorize | no       | yes, no*   | Instructs the module to enter privileged mode on the remote device before sending any commands. If not specified, the device attempts to execute all commands in non-privileged mode. If the task does not specify the value, the value of environment variable ANSIBLE_NET_AUTHORIZE is used instead. |
|   auth_pass | no       |            | Specifies the password to use if required to enter privileged mode on the remote device. If *authorize=no*, then this argument does nothing. If the task does not specify the value, the value of environment variable ANSIBLE_NET_AUTH_PASS is used instead. |
|   transport | yes      | cli*       | Configures the transport connection to use when connecting to the remote device. The *transport* argument supports connectivity to the device over CLI (SSH).  |
|    provider | no       |            | Convenient method that passes all of the above connection arguments as a dict object. All constraints (required, choices, etc.) must be met either by individual arguments or values in this dict. |


```
Note: Asterisk (*) denotes the default value if none is specified.
```

Dependencies
------------

The dellos-interface role is built on modules included in the core Ansible code.
These modules were added in Ansible version 2.2.0.


Example Playbook
----------------
The following example uses the delllos-interface role to setup description, MTU, admin status, portmode, and switchport details for an interface. This example creates a ``hosts`` file with the switch, then a corresponding ``host_vars`` file, and
then a simple playbook that references the dellos-interface role.

Sample ``hosts`` file:

    [spine]
    spine1

Sample ``host_vars/spine1``:
    
	hostname: "spine1"
    provider:
      host: "{{ hostname }}"
      username: xxxxx
      password: xxxxx
      authorize: yes
	  auth_pass: xxxxx 
      transport: cli

Sample ``vars/main.yaml``:

    dellos_interfaces:
      spine1:
        ethernet 1/1/31:
           desc: "Connected to Core 1"
           mtu: 2500
           portmode: ""
           switchport: False
           admin: up
           ip_and_mask: "192.168.11.1/24"
           ipv6_and_mask: 2001:4898:5808:ffa2::12/126
        Vlan 100:
           mtu: 4096
           admin: down
           ip_and_mask: 
           ipv6_and_mask: 2002:4898:5408:faaf::1/64
           state_ipv6: absent
           ip_helper:
             - ip: 10.0.0.36
               state: absent
           ipv6_reachabletime: 600000

A simple playbook to setup system, ``spine.yml``:

    - hosts: spine
      roles:
         - Dell-Networking.dellos-interface
 
Then run with:

    ansible-playbook -i hosts spine.yml

License
--------

Copyright (c) 2016, Dell Inc. All rights reserved.
 
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at
 
    http://www.apache.org/licenses/LICENSE-2.0
 
Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
