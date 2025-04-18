# Cisco Router and Switch Initial Configuration

![](/img/img.jpg)

## 1) Configure Router 1 with the hostname `R1`
```
Router(config)#hostname R1
R1(config)#
```

## 2) Configure Router 2 with the hostname `R2`
```
Router(config)#hostname R2
R2(config)#
```

## 3) Configure Switch 1 with the hostname `SW1`
```
Switch(config)#hostname SW1
SW1(config)#
```

## 4) Configure the IP address on R1
```
R1(config)#interface FastEthernet0/0
R1(config-if)#ip address 10.10.10.1 255.255.255.0
R1(config-if)#no shutdown
```

## 5) Configure the IP address on R2
```
R2(config)#interface FastEthernet0/0
R2(config-if)#ip address 10.10.10.2 255.255.255.0
R2(config-if)#no shutdown
```

## 6) Assign management IP to SW1
```
SW1(config)#interface vlan1
SW1(config-if)#ip address 10.10.10.10 255.255.255.0
SW1(config-if)#no shutdown
```

## 7) Set default gateway on SW1
```
SW1(config)#ip default-gateway 10.10.10.2
```

## 8) Verify SW1 can ping its default gateway
```
SW1#ping 10.10.10.2

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.10.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/2/8 ms
```

## 9) Add descriptions to interfaces
```
R1(config)#interface FastEthernet 0/0
R1(config-if)#description Link to SW1

R2(config)#interface FastEthernet 0/0
R2(config-if)#description Link to SW1

SW1(config)#interface FastEthernet 0/1
SW1(config-if)#description Link to R1

SW1(config)#interface FastEthernet 0/2
SW1(config-if)#description Link to R2
```

## 10) Verify auto-negotiated speed and duplex on SW1's link to R1
```
SW1#show interface f0/1
FastEthernet0/1 is up, line protocol is up (connected)
Full-duplex, 100Mb/s
```

## 11) Manually configure duplex and speed on SW1 and R2
```
SW1(config)#interface FastEthernet 0/2
SW1(config-if)#speed 100
SW1(config-if)#duplex full

R2(config)#interface FastEthernet 0/0
R2(config-if)#speed 100
R2(config-if)#duplex full
```

## 12) Check IOS version on SW1
```
SW1#show version
Cisco IOS Software, C2960 Software (C2960-LANBASE-M), Version 12.2(25)FX, RELEASE SOFTWARE (fc1)
```

## 13) Verify neighbors with CDP
```
SW1#show cdp neighbors

Capability Codes: R - Router, T - Trans Bridge, B - Source Route Bridge
S - Switch, H - Host, I - IGMP, r - Repeater, P - Phone

Device ID  Local Intrfce  Holdtme  Capability  Platform  Port ID
R1         Fas 0/1        170      R           C2800     Fas 0/0
R2         Fas 0/2        134      R           C2800     Fas 0/0
```

## 14) Disable CDP on SW1’s interface to R1
```
SW1(config)#interface FastEthernet 0/1
SW1(config-if)#no cdp enable
```

## 15) Flush CDP cache on R1
```
R1(config)#no cdp run
R1(config)#cdp run
```

## 16) Verify CDP info on R1
```
R1#show cdp neighbors

Capability Codes: R - Router, T - Trans Bridge, B - Source Route Bridge
S - Switch, H - Host, I - IGMP, r - Repeater, P - Phone, D - Remote, C - CVTA, M - Two-port Mac Relay

(no SW1 should appear)
```

---

# Switch Troubleshooting

## 17) Verify status of SW1 interface to R2
```
SW1#show ip interface brief

Interface        IP-Address     OK? Method Status                Protocol
Vlan1            10.10.10.10    YES manual up                    up
FastEthernet0/1  unassigned     YES unset  up                    up
FastEthernet0/2  unassigned     YES unset  up                    up
```

## 18) Shutdown the interface and verify status
```
SW1(config)#interface FastEthernet 0/2
SW1(config-if)#shutdown

*Mar 1 00:44:34.212: %LINK-5-CHANGED: Interface FastEthernet0/2, changed state to administratively down
*Mar 1 00:44:35.219: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/2, changed state to down

SW1(config-if)#do show ip interface brief

Interface        IP-Address     OK? Method Status                Protocol
Vlan1            10.10.10.10    YES manual up                    up
FastEthernet0/1  unassigned     YES unset  up                    up
FastEthernet0/2  unassigned     YES unset  administratively down down
```

## 19) Bring the interface back up and verify
```
SW1(config)#interface FastEthernet 0/2
SW1(config-if)#no shutdown

*Mar 1 00:45:52.637: %LINK-3-UPDOWN: Interface FastEthernet0/2, changed state to up
*Mar 1 00:45:53.644: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/2, changed state to up

SW1#show interface f0/2
Full-duplex, 100Mb/s
```

## 20) Set duplex to half on SW1 (R2 remains unchanged)
```
SW1(config-if)#duplex half

%LINK-5-CHANGED: Interface FastEthernet0/2, changed state to down
%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/2, changed state to down
```

## 21) Verify interface state
```
SW1#show ip interface brief

Interface        IP-Address     OK? Method Status                Protocol
FastEthernet0/1  unassigned     YES manual up                    up
FastEthernet0/2  unassigned     YES manual down                  down
```

## 22) Set duplex back to full
```
SW1(config)#int f0/2
SW1(config-if)#duplex full

%LINK-5-CHANGED: Interface FastEthernet0/2, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/2, changed state to up
```

## 23) Set speed to 10 Mbps
```
SW1(config)#int f0/2
SW1(config-if)#speed 10

%LINK-5-CHANGED: Interface FastEthernet0/2, changed state to down
%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/2, changed state to down
```

## 24) Check if interface is operational
```
SW1#show ip interface brief

Interface        IP-Address     OK? Method Status                Protocol
Vlan1            10.10.10.10    YES manual up                    up
FastEthernet0/1  unassigned     YES unset  up                    up
FastEthernet0/2  unassigned     YES unset  down                  down
```

## 25) Check R2 interface status
```
R2#show ip interface brief

Interface        IP-Address     OK? Method Status                Protocol
FastEthernet0/0  10.10.10.2     YES manual up                    down
```

---


