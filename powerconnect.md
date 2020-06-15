# Dell PowerConnect how-to
For a copy of this page without the dropdowns see [the answer page](powerconnect-answers.md)

## Basic CLI usage

Show the running config:
<details><summary>show</summary>
<p>

```bash

console#show running-config

!Current Configuration:
!System Description "PowerConnect M6348, 5.1.16.1, VxWorks 6.6"
!System Software Version 5.1.16.1
!System Operational Mode "Normal"
!
configure
vlan 150,152-155,183,3800,3807
exit
vlan 150
name "e2e-oobmgmt"

```

</p>
</details>


Search for some output from a command:
<details><summary>show</summary>
<p>

```bash

console#show running-config | include 1/0/2

interface Gi1/0/2

```

</p>
</details>

Save the running config to startup:
<details><summary>show</summary>
<p>

```bash

console#copy running-config startup-config

This operation may take a few minutes.
Management interfaces will not be available during this time.

Are you sure you want to save? (y/n)

```

</p>
</details>


## VLAN Configuration

Show VLANs configured on the switch:
<details><summary>show</summary>
<p>

```bash
console# show vlan

VLAN   Name                             Ports          Type
-----  ---------------                  -------------  --------------
1      default                          Po1-128,       Default
                                        Gi1/0/33-48,
                                        Te1/0/1-2
150    oobmgmt                          Te1/0/1-2      Static
```

</p>
</details>

or

<details><summary>show</summary>
<p>

```bash
console# show interface status


Port       Description                Duplex  Speed    Neg   Link   Flow Control
                                                             State  Status
---------  -------------------------  ------  -------  ----  ------ ------------
Gi1/0/1    bm-hyper2                  Full    1000     Auto  Up     Active
```

</p>
</details>



Add a new VLAN to the switch:
<details><summary>show</summary>
<p>

```bash
console# configure
console(config)# vlan database
console(config)# vlan 12
console(config)# exit
```

</p>
</details>

View MAC address (layer 2) traffic details:
<details><summary>show</summary>
<p>

```bash
console#show mac address-table

Aging time is 300 Sec

Vlan     Mac Address           Type        Port
-------- --------------------- ----------- ---------------------
1        84C1.C142.3E85        Dynamic     Te1/0/2
1        F48E.3840.A727        Management  Vl1
12       F48E.3840.A727        Management  Vl12
```

</p>
</details>


## Configuring Switch Ports

Shut down a port:
<br/>
(native)
<details><summary>show</summary>
<p>

```bash
console# configure
console(config)# interface Gi1/0/1
console(config)# shutdown
console(config)# exit
```

</p>
</details>

Configure a Switch Port for Access Mode:
<br/>
(native)
<details><summary>show</summary>
<p>

```bash
console# configure
console(config)# interface Gi1/0/1
console(config)# description usefuldescription
console(config)# switchport access vlan 150
console(config)# exit
```

</p>
</details>

Configure a switch port for trunk mode:
<br/>
(Dell PowerConnects allow all VLANs in the VLAN database in trunk mode)
<details><summary>show</summary>
<p>

```bash
console# configure
console(config)# interface Gi1/0/1
console(config)# description usefuldescription
console(config)# switchport mode trunk
console(config)# exit
```

</p>
</details>


Configure a switch port for general mode:
<br/>
(Dell PowerConnects allow all VLANs in the VLAN database in trunk mode)
<details><summary>show</summary>
<p>

```bash
console# configure
console(config)# interface Gi1/0/1
console(config)# description usefuldescription
console(config)# switchport mode general
console(config)# switchport general pvid 150
console(config)# switchport general allowed vlan add 101, 102, 310 tagged
console(config)# exit
```

</p>
</details>


Configure a range of ports:
<br/>
(native)
<details><summary>show</summary>
<p>

```bash
console# configure
console(config)# interface range gigabitethernet 1/0/1-32
console(config)# description alltheports
console(config)# switchport access vlan 150
console(config)# exit
```

</p>
</details>

## Troubleshooting


Show spanning tree information or find a blocked switch port:

<details><summary>show</summary>
<p>

```bash

console#show spanning-tree

Spanning tree :Enabled - BPDU Flooding :Disabled - Portfast BPDU filtering :Disabled - mode :rstp
CST Regional Root:        80:00:F4:8E:38:40:A7:25
Regional Root Path Cost:  0
ROOT ID
              Priority        17618
              Address         DC38.E19C.5E01
              Path Cost       24000
              Root Port       Te1/0/2
              Hello Time 2 Sec Max Age 20 sec Forward Delay 15 sec TxHoldCount 6 sec
              Bridge Max Hops 20
Bridge ID
              Priority        32768
              Address         F48E.3840.A725
              Hello Time 2 Sec Max Age 20 sec Forward Delay 15 sec
Interfaces

Name     State    Prio.Nbr  Cost      Sts  Role  Restricted
------   -------- --------- --------- ---- ----- ----------
Gi1/0/1  Enabled  128.1     20000     FWD  Desg  No
Gi1/0/2  Enabled  128.2     20000     FWD  Desg  No
Gi1/0/3  Enabled  128.3     20000     FWD  Desg  No
Gi1/0/4  Enabled  128.4     20000     FWD  Desg  No

```

</p>
</details>


Show switch logs:

<details><summary>show</summary>
<p>

```bash

console#show logging

Logging is enabled
Console Logging: Level warnings. Messages : 1 logged, 24284 ignored
Monitor Logging: disabled
Buffer Logging: Level informational. Messages : 23662 logged, 623 ignored
File Logging: Level emergencies. Messages : 0 logged, 24285 ignored
Switch Auditing : enabled
CLI Command Logging: disabled
Web Session Logging : disabled
SNMP Set Command Logging : disabled
Logging facility level : local7
425 Messages dropped due to lack of resources
Buffer Log:
<190> Oct 14 00:38:38 10.19.0.98-1 CLI_WEB[219199344]: cmd_logger_api.c(260) 24285 %% [CLI:----:EIA-232] User ---- logged in to enable mode.
<190> Oct 14 00:36:02 10.19.0.98-1 CLI_WEB[213919568]: cmd_logger_api.c(260) 24284 %% [CLI:----:EIA-232] Disconnected due to Idle Timeout
<190> Oct 14 00:22:00 10.19.0.98-1 CLI_WEB[219199344]: cmd_logger_api.c(260) 24283 %% [CLI:----:EIA-232] User ---- logged in to enable mode.
<189> Oct 13 23:24:00 10.19.0.98-1 TRAPMGR[277167328]: traputil.c(638) 24282 %% Spanning Tree Topology Change Received: MSTID: 0 Te1/0/2
<189> Oct 13 23:23:59 10.19.0.98-1 TRAPMGR[277167328]: traputil.c(638) 24281 %% Spanning Tree Topology Change Received: MSTID: 0 Te1/0/2

```

</p>
</details>

Check Switch Firmware Revision:

<details><summary>show</summary>
<p>

```bash

console#show version

System Description................ Dell Ethernet Switch
System Up Time.................... 33 days, 15h:51m:10s
System Contact....................
System Name.......................
System Location...................
Burned In MAC Address............. XXXX.XXXX.XXXX
System Object ID.................. 1.3.6.1.4.1.674.10895.3025
System Model ID................... PCM6348
Machine Type...................... PowerConnect M6348

unit image1      image2      current-active next-active
---- ----------- ----------- -------------- --------------
1    5.1.16.1    5.1.10.1    image1         image1

```

</p>
</details>

Find an upstream switch link:
<details><summary>show</summary>
<p>

```bash
console# show  lldp remote-device all

..omitted..
Te1/0/2    1       XX:XX:XX:XX:XX:XX     xe-6/0/2            juniper-vc0

```

</p>
</details>

Show switch port counters:

<details><summary>show</summary>
<p>

```bash

console#show interfaces counters gi1/0/1

  Port      InTotalPkts      InUcastPkts      InMcastPkts      InBcastPkts
--------- ---------------- ---------------- ---------------- ----------------
Gi1/0/1           12778926         12647225           131316              385

  Port      OutTotalPkts     OutUcastPkts     OutMcastPkts     OutBcastPkts
--------- ---------------- ---------------- ---------------- ----------------
Gi1/0/1          124119278         69159295         44478037         10481946

FCS Errors: ................................... 0
Single Collision Frames: ...................... 0
Late Collisions: .............................. 0
Excessive Collisions: ......................... 0

```

</p>
</details>


Update switch firmware:

<details><summary>show</summary>
<p>

```bash
console# copy running-config startup-config

console#copy tftp://10.10.10.200/PC8024v5.1.16.1.stk image
Transfer Mode.................................. TFTP
Server IP Address.............................. 10.10.10.200
Source File Path............................... /
Source Filename................................ PC8024v5.1.16.1.stk
Data Type...................................... Code
Destination Filename........................... image

Management access will be blocked for the duration of the transfer
Are you sure you want to start? (y/n) y
TFTP code transfer starting
12309128 bytes transferred
Verifying CRC of file in Flash File System
Distributing the code to the members of the stack!
File transfer operation completed successfully.

console# show version
Image Descriptions
image1 : default image
image2 :
Images currently available on Flash
--------------------------------------------------------------------
unit image1     image2    current-active  next-active
--------------------------------------------------------------------
1    4.2.1.3    5.1.16.1  image1          image1

console# boot system image2
Activating image image2 ..

console#update bootcode
Update bootcode and reset (Y/N)?y

```

</p>
</details>

## Scenario
You have been tasked with adding routable VLAN 165 to a switch and ensure connectivity. You need to identify which port is the uplink and add the applicable VLAN to the host ports in question. Blades 1, 2, 3 will be used for VLAN 165 as the native VLAN. Blade 4 will tag the VLAN inside the operating system.

1. Add the VLAN
<details><summary>show</summary>
<p>

```bash
console# configure
console(config)# vlan database
console(config)# vlan 165
console(config)# exit
```

</p>
</details>
2. Add the native VLANs to blades 1,2,3.
<details><summary>show</summary>
<p>

```bash
console# configure
console(config)# interface range Gi1/0/1-3
console(config)# description new_BM_hosts
console(config)# switchport access vlan 165
console(config)# exit
```

</p>
</details>
3. Ensure the VLAN is assigned to blade 4.
<br/>
HINT: Dell PowerConnects allow all VLANs in the VLAN database in trunk mode
<details><summary>show</summary>
<p>

```bash
console# configure
console(config)# interface Gi1/0/4
console(config)# description usefuldescription
console(config)# switchport mode trunk
console(config)# exit

console#show interfaces switchport Gi1/0/4

Port: Gi1/0/4
VLAN Membership Mode: Trunk Mode
Access Mode VLAN: 1 (default)
General Mode PVID: 1 (default)
General Mode Ingress Filtering: Enabled
General Mode Acceptable Frame Type: Admit All
General Mode Dynamically Added VLANs:
General Mode Untagged VLANs: 1
General Mode Tagged VLANs:
General Mode Forbidden VLANs:
Trunking Mode Native VLAN: 1 (default)
Trunking Mode Native VLAN Tagging: Disabled
Trunking Mode VLANs Enabled: All
..omitted..

```

</p>
</details>
4. Identify the uplink port and make sure the VLAN is assigned to it.
<details><summary>show</summary>
<p>

```bash
console# show  lldp remote-device all

..omitted..
Te1/0/2    1       XX:XX:XX:XX:XX:XX     xe-6/0/2            juniper-vc0

```

</p>
</details>
5. Verify layer 2 connectivity on the switch.
<details><summary>show</summary>
<p>

```bash
console#show mac address-table | include 165

Aging time is 300 Sec


165       84C1.C142.3E85        Dynamic     Te1/0/2
165       F48E.3840.A727        Dynamic     Gi1/0/1
165       F48E.3840.A728        Dynamic     Gi1/0/2
165       F48E.3840.A729        Dynamic     Gi1/0/3
165       F48E.3840.A730        Dynamic     Gi1/0/4

```

</p>
</details>

## Anatomy of a running config

```bash
console#show running-config

!Current Configuration:
!System Description "PowerConnect M6348, 5.1.16.1, VxWorks 6.6"
!System Software Version 5.1.16.1 # Firmware version
!System Operational Mode "Normal"
!
configure
vlan 150  # VLAN database
exit
vlan 150
name "e2e-oobmgmt"
exit
slot 1/0 1    ! PowerConnect M6348 # Stack information
stack
member 1 1    ! PCM6348
exit
interface out-of-band # Management IP address
ip address 10.x.y.98 255.255.255.0 10.x.y.254
exit
interface vlan 1 1
exit
interface vlan 150 6
exit
username "root" password danjndjawjdnajndjadjawndjw privilege 15 encrypted # root password and encryption
line ssh # ssh enabled
exec-timeout 60
enable authentication enableList
exit
ip ssh server # ssh enabled
interface Gi1/0/1 # blade facing 1G host port
description "bm-hyper2"
switchport access vlan 150
exit
..omitted..
interface Te1/0/1 # 10G uplink port
description "juniper-vc0-xe6/0/2"
switchport mode trunk
exit
!
snmp-server engineid local 800002a203f48e3840a725 # SNMP information
snmp-server community "switches" ro ipaddress 10.x.y.55
exit
```
