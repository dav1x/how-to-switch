# Dell PowerConnect how-to

Show VLANs configured on the switch.
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

Find an upstream switch link

```bash
console# show  lldp remote-device all

..omitted..
Te1/0/2    1       XX:XX:XX:XX:XX:XX     xe-6/0/2            juniper-vc0

```

</p>
</details>
