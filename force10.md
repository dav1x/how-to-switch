# Dell Force10 how-to
For a copy of this page without the dropdowns see [the answer page](force10-answers.md)

## Basic CLI usage

Show the running config:
<details><summary>show</summary>
<p>

```bash

Dell#show running-config
Current Configuration ...
! Version 9.13(0.1)
! Last configuration change at Mon Jun  8 16:34:05 2020 by default
! Startup-config last updated at Mon Jun  8 16:34:23 2020 by default
!
boot system stack-unit 0 primary system://B
boot system stack-unit 0 secondary system://B
!
no logging console
!
hostname Dell
!
eula-consent support-assist reject
!
protocol lldp
 advertise management-tlv system-description system-name
!
redundancy auto-synchronize full


```

</p>
</details>


Search for some output from a command:
<details><summary>show</summary>
<p>

```bash

Dell#show running-config | grep 0/2  
interface TenGigabitEthernet 0/2

```

</p>
</details>

Save the running config to startup:
<details><summary>show</summary>
<p>

```bash

Dell#copy running-config startup-config
File with same name already exist.
Proceed to copy the file [confirm yes/no]:

```

</p>
</details>


## VLAN Configuration

Show VLANs configured on the switch:
<details><summary>show</summary>
<p>

```bash
Dell#show vlan

Codes: * - Default VLAN, G - GVRP VLANs, R - Remote Port Mirroring VLANs, P - Primary, C - Community, I - Isolated
       O - Openflow, Vx - Vxlan
Q: U - Untagged, T - Tagged
   x - Dot1x untagged, X - Dot1x tagged
   o - OpenFlow untagged, O - OpenFlow tagged
   G - GVRP tagged, M - Vlan-stack, H - VSN tagged
   i - Internal untagged, I - Internal tagged, v - VLT untagged, V - VLT tagged

    NUM    Status    Description                     Q Ports
*   1      Active                                    U Te 0/9-11,37-38
    150    Active                                    T Te 0/8,13-16,20,24,29-32
```

</p>
</details>

or

<details><summary>show</summary>
<p>

```bash
Dell#show interface status
Port                 Description  Status Speed        Duplex Vlan  
Te 0/1               bm-c2hyp-pr  Up     10000 Mbit   Full   1234,3792,4000
Te 0/2               bm-hub1-pr   Up     10000 Mbit   Full   1234,3790-3792,4000
```

</p>
</details>



Add a new VLAN to the switch:
<details><summary>show</summary>
<p>

```bash
Dell#configure
Dell(conf)#int vlan 1235
Dell(conf-if-vl-1235)#tagged tengigabitethernet 0/37-38
Dell(conf-if-vl-1235)#exit
```

</p>
</details>

View MAC address (layer 2) traffic details:
<details><summary>show</summary>
<p>

```bash
Dell#show cam mac stack-unit 0 port-set 0
VlanId     Mac Address      Region    Interface
 1234   24:6e:96:84:46:10   DYNAMIC     Te 0/14
 1234   f8:b1:56:7c:12:84   DYNAMIC     Te 0/38
 1      24:6e:96:84:50:f0   DYNAMIC     Te 0/9
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
Dell#configure
Dell(conf)#interface te0/17
Dell(conf-if-te-0/17)#shutdown
```

</p>
</details>

Configure a native VLAN for a Port:
<br/>
(native)
<details><summary>show</summary>
<p>

```bash
Dell#configure
Dell(conf-if-te-0/17)#int vlan 183
Dell(conf-if-vl-183)#untagged tengigabitethernet 0/17
Dell(conf-if-vl-183)#exit
```

</p>
</details>

Configure a tagged VLAN for a Port:
<br/>
<details><summary>show</summary>
<p>

```bash
Dell#configure
Dell(conf-if-te-0/17)#int vlan 183
Dell(conf-if-vl-183)#tagged tengigabitethernet 0/17
Dell(conf-if-vl-183)#exit
```

</p>
</details>


Configure a range of ports:
<br/>
(native)
<details><summary>show</summary>
<p>

```bash
Dell#configure
Dell(conf-if-vl-183)#int range te0/1-12
Dell(conf-if-range-te-0/1-12)#exit
```

</p>
</details>

## Troubleshooting


Show spanning tree information or find a blocked switch port:

<details><summary>show</summary>
<p>

```bash

Dell#show spanning-tree 0

```

</p>
</details>


Show switch logs:

<details><summary>show</summary>
<p>

```bash

Dell#show logging
Syslog logging: enabled
    Console logging: disabled
    Monitor logging: level debugging
    Buffer logging: level debugging, 394 Messages Logged, Size (40960 bytes)
    Trap logging: level informational
Jun 18 13:52:30 %STKUNIT0-M:CP %SEC-5-AUTHENTICATION_ENABLE_SUCCESS: Enable authentication success  on console
Jun 18 13:52:30 %STKUNIT0-M:CP %SEC-4-ENABLE_PASSW_NOT_CONFIGURED: Enable password is required for authentication but not configured (by default from console)
Jun 18 13:52:01 %STKUNIT0-M:CP %SEC-5-LOGIN_SUCCESS: Login successful on console
Jun 18 13:51:59 %STKUNIT0-M:CP %SEC-5-LOGOUT: Exec session is terminated on console  (Reason : User Request)
Jun 18 13:51:44 %STKUNIT0-M:CP %SYS-5-CONFIG_I: Configured from  console
Jun 18 13:42:16 %STKUNIT0-M:CP %SYS-5-CONFIG_I: Configured from  console

```

</p>
</details>

Check Switch Firmware Revision:

<details><summary>show</summary>
<p>

```bash

Dell#show version
Dell Real Time Operating System Software
Dell Operating System Version:  2.0
Dell Application Software Version:  9.13(0.1)
Copyright (c) 1999-2018 by Dell Inc. All Rights Reserved.
Build Time: Tue Feb 27 09:25:55 2018
Build Path: /build/build05/SW/SRC
Dell Networking OS uptime is 28 week(s), 0 day(s), 15 hour(s), 52 minute(s)

System image file is "system://B"

System Type: MXL-10/40GbE
Control Processor: MIPS RMI XLP with 2 Gbytes (2147483648 bytes) of memory, core(s) 1.

256M bytes of boot flash memory.

  1 34-port GE/TE/FG (XL)
 52 Ten GigabitEthernet/IEEE 802.3 interface(s)


```

</p>
</details>

Find an upstream switch link:
<details><summary>show</summary>
<p>

```bash
Dell# show  lldp neighbor

..omitted..
 Te 0/38        juniper-storage...xe-4/1/1                           dc:38:e1:9c:5e:00

```

</p>
</details>

Show switch port counters:

<details><summary>show</summary>
<p>

```bash

Dell# show hardware counters interface tengigabitethernet 0/2
unit: 0 port: 2 (interface Te 0/2)
Description                                      Value

RX - IPV4 L3 Unicast Frame Counter                 0
RX - IPV4 L3 Routed Multicast Packets              0
RX - IPV6 L3 Unicast Frame Counter                 0
RX - IPV6 L3 Routed Multicast Packets              0
RX - Unicast Packet Counter                        3470462
RX - 64 Byte Frame Counter                         18607564
RX - 65 to 127 Byte Frame Counter                  31422212
RX - 128 to 255 Byte Frame Counter                 43781694

```

</p>
</details>


Update switch firmware:

<details><summary>show</summary>
<p>

```bash
Dell# copy running-config startup-config

Dell#show boot system stack-unit all

Current system image information in the system:
===============================================

Type          Boot Type       A                                  B
----------------------------------------------------------------------------------------------
stack-unit 0  FLASH BOOT      9.10(0.1P10)                    9.13(0.1)[boot]                   
stack-unit 1 is not present.
stack-unit 2 is not present.

Dell#upgrade system tftp://10.0.0.1/FTOS-XL-9.12.0.0P2.bin a:

Dell#show boot system stack-unit all

Current system image information in the system:
===============================================

Type          Boot Type       A                                  B
----------------------------------------------------------------------------------------------
stack-unit 0  FLASH BOOT      9.12(P2)                    9.13(0.1)[boot]                   
stack-unit 1 is not present.
stack-unit 2 is not present.

Dell# conf t
Dell# boot system stack-unit all primary system a:
Dell# exit
Dell# copy run start
Dell# reload
```

</p>
</details>

## Scenario
You have been tasked with adding routable VLAN 165 to a switch and ensure connectivity. You need to identify which port is the uplink and add the applicable VLAN to the host ports in question. Blades 1, 2, 3 will be used for VLAN 165 as the native VLAN. Blade 4 will tag the VLAN inside the operating system.

1. Add the native VLANs to blades 1,2,3.
<details><summary>show</summary>
<p>

```bash
Dell#configure
Dell# int vlan 165
Dell(conf-if-vl-165)#untagged te0/1-3
```

</p>
</details>
3. Ensure the VLAN is assigned to blade 4.
<br/>
<details><summary>show</summary>
<p>

```bash
Dell#configure
Dell# int vlan 165
Dell(conf-if-vl-165)#tagged te0/4

Dell#show interfaces switchport tengigabitethernet 0/4

Codes:  U - Untagged, T - Tagged
        x - Dot1x untagged, X - Dot1x tagged
        G - GVRP tagged, M - Trunk, H - VSN tagged
        i - Internal untagged, I - Internal tagged, v - VLT untagged, V - VLT tagged

Name: TenGigabitEthernet 0/4
Description: bm-hub3-pr
802.1QTagged: Hybrid
Vlan membership:
Q       Vlans
U       3790
T       165
..omitted..

```

</p>
</details>
4. Identify the uplink port and make sure the VLAN is assigned to it.
<details><summary>show</summary>
<p>

```bash
Dell# show lldp neighbor

..omitted..
 Te 0/38        juniper-storage...xe-4/1/1                           dc:38:e1:9c:5e:00

```

</p>
</details>
5. Verify layer 2 connectivity on the switch.
<details><summary>show</summary>
<p>

```bash
Dell#show cam mac stack-unit 0 port-set 0 | grep 165

165       84C1.C142.3E85        Dynamic     Te0/38
165       F48E.3840.A727        Dynamic     Te0/1
165       F48E.3840.A728        Dynamic     Te0/2
165       F48E.3840.A729        Dynamic     Te0/3
165       F48E.3840.A730        Dynamic     Te0/4

```

</p>
</details>

## Anatomy of a running config

```bash
Dell#show running-config
Current Configuration ...
! Version 9.13(0.1)  # FW version
! Last configuration change at Thu Jun 18 08:51:42 2020 by default
! Startup-config last updated at Mon Jun  8 16:34:23 2020 by default
!
boot system stack-unit 0 primary system://B  # Running image
boot system stack-unit 0 secondary system://B
!
no logging console
!
hostname Dell
!
eula-consent support-assist reject
!
protocol lldp
 advertise management-tlv system-description system-name  # LLDP information
!
redundancy auto-synchronize full
!
username root password 7 7cd5b00cb6fef1a3 privilege 15 role sysadmin
!
stack-unit 0 provision MXL-10/40GbE  # stack info
!
stack-unit 0 port 33 portmode quad   # 40GB ports break out cables
!
stack-unit 0 port 37 portmode quad
!
stack-unit 0 port 49 portmode quad
!
stack-unit 0 port 53 portmode quad
!
interface TenGigabitEthernet 0/1  # Internal switchport
 description bm-c2hyp-pr
 no ip address
 mtu 12000
 portmode hybrid
 switchport
 flowcontrol rx on tx off  
 no shutdown
 !
 interface TenGigabitEthernet 0/38 # External uplink port
  no ip address
  mtu 12000
  portmode hybrid
  switchport
  flowcontrol rx on tx off  
  no shutdown
  !
  interface ManagementEthernet 0/0 # Management IP address
   ip address 10.19.0.96/24
   no shutdown
  !       
  interface Vlan 1
   ip address dhcp
  !untagged TenGigabitEthernet 0/9-11,37-38
   no shutdown
  !
  interface Vlan 150  # Interfaces are configured in VLANs
   no ip address
   tagged TenGigabitEthernet 0/8,13-16,20,24,29-32
   shutdown
  !
  management route 0.0.0.0/0 10.19.0.254  # management default GW
  !
  service-class dynamic dot1p
  !
  snmp-server community sys rw  # SNMP information
  !
  ntp server 10.5.27.10 # NTP info
  !
  clock timezone Eastern -5
  !
  wred-profile mem
  !
  no dcb enable  # Datacenter bridging config
  !
  dcb-map FLEXIO_DCB_MAP_PFC_OFF
   no pfc mode on
  !
  line console 0
  line vty 0
  line vty 1
  line vty 2
  line vty 3
  line vty 4
  line vty 5
  line vty 6
  line vty 7
  line vty 8
  !
  reload-type
   boot-type normal-reload
   config-scr-download enable
  !
  no iscsi enable  # iSCSI automation disabled
  !
  end
```
