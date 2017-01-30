 Interface Role for Dell EMC Networking OS
=========================================

This role facilitates the configuration of interface attributes. It supports the configuration of admin state, description, MTU, IP address, IP helper, suppress_ra and port mode.
This role is abstracted fo OS9,OS6 and OS10.

Installation
------------

```
ansible-galaxy install Dell-Networking.dellos-interface
```

Requirements
------------

This role requires an SSH connection for connectivity to your Dell EMC Networking device. You can use any of the built-in Dell EMC Networking OS connection variables, or the ``provider``
dictionary.

Role Variables
--------------

``dellos_interface`` (dictionary) contains the hostname (dictionary).
The hostname is the value of the variable ``hostname`` that corresponds to the name of the OS device.
This role is abstracted using the variable ``ansible_net_os_name`` that can take the following values: dellos9, dellos6, dellos10.

Any role variable with a corresponding state variable setting to absent negates the configuration of that variable. 
For variables with no state variable, setting an empty value for the variable negates the corresponding configuration.
The variables and its values are case-sensitive.

The hostname (dictionary) holds a dictionary with the interface name. The interface name can correspond to any of the valid OS interfaces with the unique interface identifier name.

For physical interfaces, the interface name must be in the format `<interfacename> <tuple>`. For logical interfaces, the format is `<logical_interfacename> <id>`.
For example, the physical interface name can be in the format fortyGigE 1/1 for OS9 devices, ethernet 1/1/32 for OS10 devices and Te1/0/1 for OS6 devices.
The logical interface names can be in the format vlan 1 or port-channel 12 for OS9 devices, OS6 devices and OS10 devices.

```
Note: Only define supported variables for the interface type. For example, do not define the ``switchport`` variable for a logical interface; do not configure portmode when ``switchport`` is present in OS9 devices; do not define ip address for physical interfaces in OS6 devices.
```


``interface name`` holds the following keys:

|        Key | Type                      | Notes                                                                                                                                                                                     |
|-----------:|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|   desc  | string         | Configures a one line description for the interface. |
| portmode | string |Configures portmode according to the type of the OS device. OS9 devices support portmode hybrid. OS10 and OS6 devices support portmode access and trunk. |
| switchport | boolean: true, false*  | Used to configure an interface in layer 2 mode. |
| admin      | string, choices: up, down*              |Configures the administrative state for the interface. Configuring the value as up administratively enables the interface; configuring the value as down administratively disables the interface. |
| mtu        | integer                       |Configures the MTU size for layer 2 and layer 3 interfaces. For example, the MTU range is 594-12000 for OS9 devices, 1280-65535 on OS10 devices and mtu is set globally on OS6 devices.|
| fanout     | boolean: true, false           |Configures fan-out on the port when set to true.                 |
| suppress_ra | string     |Configures ipv6 router advertisements if this key is set to present. |
| ip_type_dynamic | boolean: true, false           |Configures IP address DHCP when set to true. The ``ip_and_mask`` key is ignored when set to true.This key is supported only in OS9 and OS6 devices. |
| class_vendor_identifier | string, choices: present,absent,string | Configures vendor class identifier without user-defined string when set to present. Configures vendor class identifier with user defined string, when a string is specified. This key is Ignored when ``ip_type_dynamic`` is set to false.This key is supported only in OS9 devices.  |
|option82 | boolean: true, false* | Configures option82 with remote-id mac, if ``remote_id`` undefined. This key is Ignored when ``ip_type_dynamic`` is set to false.This key is supported only in OS9 devices.  |
|remote_id |string, choices: hostname,mac,string | Configures option82 with given ``remote-id``. Ignored when ``option82`` is set to false.This key is supported only in OS9 devices.   |
|ip_and_mask | string |Configures specified IP address to the interface on OS9 and OS10 devices. Configures specified IP address to the interface VLAN on OS6 devices.The value should be in the following format: 192.168.11.1/24 |
|ipv6_and_mask | string |Configures a specified IPv6 address to the interface on OS9 and OS10 devices. Configures specified IP address to the interface VLAN on OS6 devices. The value must be in the following format: 2001:4898:5808:ffa2::1/126 |
| ipv6_reachabletime       | integer                       | Configures the reachability time for IPv6 neighbor discovery. The time range is 0-3600000.For OS10 devices, this key is not supported.|
| ip_helper | list | Contains objects to configure the DHCP server address. See the following ip_helper.* keys for each list item. |
| ip_helper.ip | string (required)         | Configures the IPv4 address of the DHCP Server. The value must be in the form of A.B.C.D  |
|    ip_helper.state | string, choices: absent, present* | Configuring this key as absent deletes the IP helper address.               |

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
|        host | yes      |            | Hostname or address for connecting to the remote device over the specified ``transport``. The value of this key is the destination address for the transport. |
|        port | no       |            | Port used to build the connection to the remote device. If this key does not specify the value, the value defaults to 22. |
|    username | no       |            | Configures the username that authenticates the connection to the remote device. The value of this key authenticates the CLI login. If this key does not specify the value, the value of environment variable ANSIBLE_NET_USERNAME is used instead. |
|    password | no       |            | Specifies the password that authenticates the connection to the remote device. If this key does not specify the value, the value of environment variable ANSIBLE_NET_PASSWORD is used instead. |
|   authorize | no       | yes, no*   | Instructs the module to enter privileged mode on the remote device before sending any commands. If this key does not specify the value, the value of environment variable ANSIBLE_NET_AUTHORIZE is used instead. If not specified, the device attempts to execute all commands in non-privileged mode.|
|   auth_pass | no       |            | Specifies the password to use if required to enter privileged mode on the remote device. If this key is set to no, then this argument not applicable. If the ``auth_pass`` does not specify the value, the value of environment variable ANSIBLE_NET_AUTH_PASS is used instead. |
|   transport | yes      | cli*       | Configures the transport connection to use when connecting to the remote device. This key supports connectivity to the device over CLI (SSH).  |
|    provider | no       |            | Convenient method that passes all of the above connection arguments as a dictonary object. All constraints (such as required or choices) must be met either by individual arguments or values in this dictonary. |

```
Note: Asterisk (*) denotes the default value if none is specified.
```

Dependencies
------------

The dellos-interface role is built on modules included in the core Ansible code.
These modules were added in Ansible version 2.2.0.


Example Playbook
----------------
The following example uses the Dell-Networking.delllos-interface role to set up description, MTU, admin status, portmode, and switchport details for an interface. This example creates a ``hosts`` file with the switch, a corresponding ``host_vars`` file, and
then a simple playbook that references the dellos-interface role.

Sample ``hosts`` file:

    leaf3 ansible_host= <ip_address> ansible_net_os_name= <OS name(dellos9/dellos6/dellos10)>

Sample ``host_vars/leaf3``:
    
	hostname: "leaf3"
    provider:
      host: "{{ hostname }}"
      username: xxxxx
      password: xxxxx
      authorize: yes
	  auth_pass: xxxxx 
      transport: cli

Sample ``vars/main.yaml``:

    dellos_interface:
      leaf3:
        fortyGigE 1/8:
          desc: "Connected to Spine1"
          portmode:
          switchport: False
          mtu: 2500
          admin: up
          ipv6_and_mask: 2001:4898:5808:ffa2::5/126
          suppress_ra : present
          ip_type_dynamic: true
          ip_and_mask: 192.168.23.22/24
          class_vendor_identifier: present
          option82: true
          remote_id: hostname
        fortyGigE 1/9:
          desc: "Connected to Spine2"
          switchport: False
          mtu: 2500
          admin: up
          ip_and_mask: 192.168.234.20/31
          suppress_ra: absent
          ip_type_dynamic: false
          class_vendor_identifier: absent
          option82: true
          remote_id: hostname
          ipv6_and_mask: 2001:4898:5808:ffa2::9/126
         Vlan 100:
           mtu: 4096
           admin: down
           ip_and_mask:
           ipv6_and_mask: 2002:4898:5408:faaf::1/64
           suppress_ra: present
           state_ipv6: absent
           ip_helper:
              - ip: 10.0.0.36
                state: absent
            ipv6_reachabletime: 600000


Simple playbook to setup system, ``leaf.yaml``:

    - hosts: leaf3
      roles:
         - Dell-Networking.dellos-interface
 
Then run with:

    ansible-playbook -i hosts leaf.yaml

License
--------

Copyright (c) 2017, Dell Inc. All rights reserved.
 
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at
 
    http://www.apache.org/licenses/LICENSE-2.0
 
Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
