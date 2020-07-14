# Juniper JuneOS how-to
For a copy of this page without the dropdowns see [the answer page](juneos-answers.md)
Additionally, for a more complete guide of commands see [the vendor documentation](https://www.juniper.net/documentation/en_US/junos/information-products/pathway-pages/junos-cli/junos-cli.html)

For a quick juneos environment:

```
podman run -p 8022:22 -d -e SWITCH_MODEL="juniper_generic" internap/fake-switches
ssh root@localhost -p 8022
password is root
```

## Basic CLI usage
Once you log on to the switch you need to move to privileged mode to configure things.
(There is no enable password)
```
root@juniper-vc0:RE:3% cli
root@juniper-vc0> configure 
Entering configuration mode
```

Show the running config:
<details><summary>show</summary>
<p>

```bash

root@juniper-vc0# show 
## Last changed: 2020-07-13 13:18:29 EDT
version 15.1R7.9;
system {
    host-name juniper-vc0;
    auto-snapshot;
    domain-name cloud.lab.eng.bos.redhat.com;
    time-zone America/New_York;
    root-authentication {
        encrypted-password "$1$vyg5ZHCT$Y8So4XvPeqKkZJViRy5Wr/"; ## SECRET-DATA
    }
    login {
        class readonly {
            permissions view-configuration;
            allow-commands show;
            deny-commands "(clear)|(file)|(file show)|(help)|(load)|(monitor)|(op)|(request)|(save)|(set)|(start)|(test)";
            allow-configuration show;
            deny-configuration all;
        }
        user rancid {
            uid 2000;
            class super-user;
            authentication {
                encrypted-password "$1$XckToyvc$HIMyD7XlnwzRzLCYIUjcD1"; ## SECRET-DATA
            }
        }
        user readonly {
            uid 2007;
            class readonly;
            authentication {
                encrypted-password "$1$riOSC3cP$K6M3OeZrTcNv246RPUiyY/"; ## SECRET-DATA
            }
        }

..omitted..

```

</p>
</details>


Search for some output from a command:
<details><summary>show</summary>
<p>

```bash
root@juniper-vc0# show | grep 0/2 
    ge-0/0/2 {
    xe-0/0/2 {

```

</p>
</details>

Save the running config to startup:
<details><summary>show</summary>
<p>

```bash

root@juniper-vc0# commit     
fpc3: 
configuration check succeeds
fpc0: 

```

</p>
</details>


## VLAN Configuration

Show VLANs configured on the switch:
<details><summary>show</summary>
<p>

```bash
root@juniper-vc0# show vlans 
Cloud {
    vlan-id 168;
    l3-interface vlan.2;
}
SysEng {
    vlan-id 163;
    l3-interface vlan.1;
}

```

</p>
</details>


Add a new VLAN to the switch:
<details><summary>show</summary>
<p>

```bash
root@juniper-vc0# set vlans vlan173 vlan-id 173
root@juniper-vc0# set vlans vlan173 description vl173
```

</p>
</details>

View MAC address (layer 2) traffic details:
<details><summary>show</summary>
<p>

```bash
root@juniper-vc0# run show ethernet-switching table 
Ethernet-switching table: 811 entries, 733 learned, 0 persistent entries
  VLAN              MAC address       Type         Age Interfaces
  Cloud             *                 Flood          - All-members
  Cloud             00:0e:1e:88:91:b0 Learn          0 ge-1/0/31.0
  Cloud             00:0e:1e:88:91:b1 Learn          0 ge-1/0/31.0
  Cloud             00:0e:1e:88:a1:40 Learn          0 ge-1/0/32.0
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
root@juniper-vc0# set interfaces xe-0/0/1.0 disable
```

Bring up a port:
<br/>
(native)
<details><summary>show</summary>
<p>

```bash
root@juniper-vc0# delete interfaces xe-0/0/1.0 disable
```


</p>
</details>

Configure a native VLAN for a Port:
<br/>
(native)
<details><summary>show</summary>
<p>

```bash
root@juniper-vc0# configure
root@juniper-vc0# set interfaces ge-1/0/47 unit 0 family ethernet-switching port-mode access
root@juniper-vc0# set interfaces ge-1/0/47 unit 0 family ethernet-switching vlan members vlan212
```

</p>
</details>

Configure a tagged VLAN for a Port:
<br/>
<details><summary>show</summary>
<p>

```bash
root@juniper-vc0# configure
root@juniper-vc0# set interfaces xe-0/0/0 unit 0 family ethernet-switching port-mode trunk
root@juniper-vc0# set interfaces xe-0/0/0 unit 0 family ethernet-switching vlan members 209 
root@juniper-vc0# set interfaces xe-0/0/0 unit 0 family ethernet-switching vlan members 4061-4079
```

</p>
</details>


Configure a range of ports:
<br/>
(native)
<details><summary>show</summary>
<p>

```bash
root@juniper-vc0# configure
root@juniper-vc0# member-range ge-0/0/0 to ge-4/0/40;
```

</p>
</details>

## Troubleshooting


Show spanning tree information or find a blocked switch port:

<details><summary>show</summary>
<p>

```bash

root@juniper-vc0# run show spanning-tree bridge       

STP bridge parameters 
Context ID                          : 0
Enabled protocol                    : RSTP
  Root ID                           : 17618.dc:38:e1:9c:5e:01
  Root cost                         : 22000
  Root port                         : xe-6/0/5.0
  Hello time                        : 2 seconds
  Maximum age                       : 20 seconds
  Forward delay                     : 15 seconds
  Message age                       : 2 
  Number of topology changes        : 10224
  Time since last topology change   : 137 seconds
  Topology change initiator         : xe-0/0/4.0
  Topology change last recvd. from  : f8:b1:56:97:6b:39
  Local parameters 
    Bridge ID                       : 24576.3c:8a:b0:eb:47:01
    Extended system ID              : 0
    Internal instance ID            : 0

root@juniper-vc0# run show spanning-tree interface 

Spanning tree interface parameters for instance 0

Interface            Port ID   Designated    Designated       Port   State Role
                                port ID      bridge ID        Cost
ae4.0                    128:5     128:5 24576.3c8ab0eb4701     1000 FWD   DESG 
xe-0/0/0.0             128:513   128:513 24576.3c8ab0eb4701     2000 FWD   DESG 
xe-0/0/2.0             128:515   128:515 24576.3c8ab0eb4701     2000 FWD   DESG 
xe-0/0/3.0             128:516   128:516 24576.3c8ab0eb4701     2000 FWD   DESG 
xe-0/0/4.0             128:517   128:517 24576.3c8ab0eb4701     2000 FWD   DESG 
xe-0/0/5.0             128:518   128:518 24576.3c8ab0eb4701     2000 FWD   DESG 

```

</p>
</details>


Show switch logs:

<details><summary>show</summary>
<p>

```bash

root@juniper-vc0# run show log messages   
Jul 14 08:00:00 juniper-vc0 newsyslog[36050]: logfile turned over due to size>128K
Jul 14 08:00:01  juniper-vc0 /kernel: ae_bundlestate_ifd_change: bundle ae0: bundle IFD minimum bandwidth or minimum links not met, Bandwidth (Current : Required) 0 : 1 Number of links (Current : Required) 0 : 1
Jul 14 08:00:01  juniper-vc0 /kernel: KERN_LACP_INTF_STATE_CHANGE: lacp_update_state_userspace: cifd xe-3/0/6 - ATTACHED state - acting as standby link
Jul 14 08:00:01  juniper-vc0 mib2d[1260]: SNMP_TRAP_LINK_DOWN: ifIndex 745, ifAdminStatus up(1), ifOperStatus down(2), ifName ae0
Jul 14 08:00:01  juniper-vc0 chassism[1165]: IFCM: no handler for command subtype 179
Jul 14 08:00:02  juniper-vc0 last message repeated 3 times
Jul 14 08:00:04  juniper-vc0 /kernel: KERN_LACP_INTF_STATE_CHANGE: lacp_update_state_userspace: cifd xe-3/0/2 - CD state - ready to carry traffic
Jul 14 08:00:04  juniper-vc0 /kernel: KERN_LACP_INTF_STATE_CHANGE: lacp_update_state_userspace: cifd xe-3/0/4 - CD state - ready to carry traffic
Jul 14 08:00:04  juniper-vc0 /kernel: KERN_LACP_INTF_STATE_CHANGE: lacp_update_state_userspace: cifd xe-3/0/6 - CD state - ready to carry traffic
Jul 14 08:00:04  juniper-vc0 /kernel: KERN_LACP_INTF_STATE_CHANGE: lacp_update_state_userspace: cifd xe-3/0/2 - ATTACHED state - acting as standby link


```

</p>
</details>

Check Switch Firmware Revision:

<details><summary>show</summary>
<p>

```bash

root@juniper-vc0# show version   
## Last changed: 2020-07-14 06:56:49 EDT
version 15.1R7.9;

```

</p>
</details>

Find an upstream switch link:
<details><summary>show</summary>
<p>

```bash

root@juniper-vc0# run show lldp neighbors 
Local Interface    Parent Interface    Chassis Id          Port info          System Name
xe-3/1/7.0         -                   00:01:e8:8c:04:90   TenGigabitEthernet 0/47
xe-0/0/31.0        -                   18:a9:05:ea:64:55   X6                 VcD_90eae38215d7    
xe-0/0/5.0         -                   18:a9:05:ea:74:11   X5                 VcD_90eae38215d7    
xe-0/0/8.0         -                   18:a9:05:ea:74:11   X6                 VcD_90eae38215d7    
xe-0/0/26.0        -                   d0:67:e5:97:91:7a   Te1/0/19
xe-0/0/29.0        -                   d0:67:e5:c6:1a:9c   Te1/0/20
xe-0/0/9.0         -                   d0:67:e5:d8:68:5d   Te1/0/1
xe-0/0/10.0        -                   f4:8e:38:00:90:c0   TenGigabitEthernet 0/33

```

</p>
</details>

Show switch port counters:

<details><summary>show</summary>
<p>

```bash

root@juniper-vc0# run show interfaces xe-0/0/0 extensive 
Physical interface: xe-0/0/0, Enabled, Physical link is Up
  Interface index: 292, SNMP ifIndex: 502, Generation: 295
  Link-level type: Ethernet, MTU: 1514, LAN-PHY mode, Speed: 10Gbps, Duplex: Full-Duplex, BPDU Error: None, MAC-REWRITE Error: None, Loopback: Disabled, Source filtering: Disabled,
  Flow control: Enabled, Media type: Copper
  Device flags   : Present Running
  Interface flags: SNMP-Traps Internal: 0x0
  Link flags     : None
  CoS queues     : 8 supported, 8 maximum usable queues
  Hold-times     : Up 0 ms, Down 0 ms
  Current address: 84:b5:9c:49:db:03, Hardware address: 84:b5:9c:49:db:03
  Last flapped   : 2019-12-03 05:33:55 EST (32w0d 01:32 ago)
  Statistics last cleared: Never
  Traffic statistics:
   Input  bytes  :        2387384178247                35528 bps
   Output bytes  :        2564048686165               490520 bps
   Input  packets:           1981179488                    9 pps
   Output packets:           7018921484                  354 pps
   IPv6 transit statistics:
    Input  bytes  :                   0
    Output bytes  :                   0
    Input  packets:                   0
    Output packets:                   0
  Input errors:
    Errors: 0, Drops: 0, Framing errors: 0, Runts: 0, Policed discards: 0, L3 incompletes: 0, L2 channel errors: 0, L2 mismatch timeouts: 0, FIFO errors: 0, Resource errors: 0
  Output errors:
    Carrier transitions: 1, Errors: 0, Drops: 5594, Collisions: 0, Aged packets: 0, FIFO errors: 0, HS link CRC errors: 0, MTU errors: 0, Resource errors: 0
  Egress queues: 8 supported, 4 in use
  Queue counters:       Queued packets  Transmitted packets      Dropped packets
    0                                0           6976960540                 5594
    1                                0                    0                    0
    5                                0                    0                    0
    7                                0             41961121                    0
  Queue number:         Mapped forwarding classes
    0                   best-effort
    1                   assured-forwarding
    5                   expedited-forwarding
    7                   network-control
  Active alarms  : None
  Active defects : None
  MAC statistics:                      Receive         Transmit
    Total octets                 2387384178247    2564048686165
    Total packets                   1981179488       7018921484
    Unicast packets                 1973270417       2002181920
    Broadcast packets                  1212754        510422285
    Multicast packets                  6696317       4506317279
    CRC/Align errors                         0                0
    FIFO errors                              0                0
    MAC control frames                       0                0
    MAC pause frames                         0                0
    Oversized frames                         0
    Jabber frames                            0
    Fragment frames                          0
    Code violations                          0
  Packet Forwarding Engine configuration:
    Destination slot: 0
  CoS information:
    Direction : Output
    CoS transmit queue               Bandwidth               Buffer Priority   Limit

```

</p>
</details>



## Scenario
You have been tasked with adding routable VLAN 165 to a switch and ensure connectivity. You need to identify which port is the uplink and add the applicable VLAN to the host ports in question. Servers 1, 2, 3 will be used for VLAN 165 as the native VLAN on ports xe-0/0/1-3. Server 4 will tag the VLAN inside the operating system on port xe-0/0/4.

1. Add the native VLANs to servers 1,2,3.
<details><summary>show</summary>
<p>

```bash
root@juniper-vc0# configure
root@juniper-vc0# set interfaces xe-0/0/1 unit 0 family ethernet-switching port-mode access
root@juniper-vc0# set interfaces xe-0/0/1 unit 0 family ethernet-switching vlan members 165
root@juniper-vc0# set interfaces xe-0/0/2 unit 0 family ethernet-switching port-mode access
root@juniper-vc0# set interfaces xe-0/0/2 unit 0 family ethernet-switching vlan members 165
root@juniper-vc0# set interfaces xe-0/0/3 unit 0 family ethernet-switching port-mode access
root@juniper-vc0# set interfaces xe-0/0/3 unit 0 family ethernet-switching vlan members 165

root@juniper-vc0# run show ethernet-switching table | grep 165               
Ethernet-switching table: 817 entries, 739 learned, 0 persistent entries
  165             00:0e:1e:88:91:b1 Learn          0 xe-0/0/1.0
  165             00:0e:1e:88:a1:40 Learn          0 xe-0/0/2.0
```

</p>
</details>
3. Ensure the VLAN is assigned to server 4.
<br/>
<details><summary>show</summary>
<p>

```bash
root@juniper-vc0# set interfaces xe-0/0/4 unit 0 family ethernet-switching port-mode trunk
root@juniper-vc0# set interfaces xe-0/0/4 unit 0 family ethernet-switching vlan members 165

root@juniper-vc0# run show ethernet-switching table | grep 165
Ethernet-switching table: 817 entries, 739 learned, 0 persistent entries
  165             00:0e:1e:88:91:bc Learn          0 xe-0/0/4.0

```

</p>
</details>
4. Identify the uplink port and make sure the VLAN is assigned to it.
<details><summary>show</summary>
<p>

```bash

root@juniper-vc0# run show lldp neighbors 
Local Interface    Parent Interface    Chassis Id          Port info          System Name
xe-0/0/25.0        -                   d0:67:e5:89:f7:7f   Te1/0/1
xe-0/0/22.0        -                   d0:67:e5:97:8e:f2   Te1/0/20

root@juniper-vc0# show | display set | grep xe-0/0/22 
set interfaces xe-0/0/22 mtu 9216
set interfaces xe-0/0/22 unit 0 family ethernet-switching port-mode trunk
set interfaces xe-0/0/22 unit 0 family ethernet-switching vlan members 165
```

</p>
</details>

