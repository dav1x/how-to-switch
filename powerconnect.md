# Dell PowerConnect how-to

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

## Configuring Switch Ports

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

<br/>

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
