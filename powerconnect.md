# Dell PowerConnect how-to

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


Configure a switch port for access mode:
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
