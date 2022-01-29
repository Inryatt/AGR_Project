Router Interfaces

| Router | f0/0 | f0/1 | f1/0 | f2/0 | Lo0 | Lo99 | Lo98 | Lo97 | Lo50 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Madrid | CompanyD | 15.0.0.1
2002:F::1 | 14.0.0.1
2002:E::1 | 13.0.0.2
2002:D::2 | Internet | - | - | - | - |
| Roma | CompanyC | 12.0.0.1
2002:C::1 | 11.0.0.1
2002:B::1 | 13.0.0.1
2002:D::1 | Internet | - | - | - | - |
| OPorto | CompanyA@Porto | 15.0.0.2
2002:F::2 | 12.0.0.2
2002:C::2 | 10.0.0.1
2002:A::1 | - | 99.0.0.1
2020:0::1/128 | 99.0.0.2
2020:0::2/128 | 99.0.0.3
2020:0::3/128 | 101.0.0.1
2020:0::4/128 |
| Lisbon | CompanyB | 11.0.0.2
2002:B::2 | 14.0.0.2
2002:E::2 | 10.0.0.2
2002:A::2 | - | 98.0.0.1 
2021:0::1/128 | - | - | - |
| Aveiro | 10.0.1.1
2002:E:1::1 | 10.0.0.3
2002:A::3 | - | - | - | 97.0.0.1 
2022:0::1/128 | - | - | - |
| Ilhavo | CompanyA@Ilhavo | 10.0.1.2
2002:E:1::2 | - | - | - | 96.0.0.1 
2023:0::1/128 | 96.0.0.2 
2023:0::2/128 | 96.0.0.3
2023:0::3/128 | 100.0.0.1
2023:0::4/128 |

Machine IP’s

| Name | IP |
| --- | --- |
| CompanyA@Porto | 81.100.1.3
2001:81:1:0000::3 |
| CompanyA@Ilhavo | 81.100.1.131
2001:81:1:2000::3 |
| CompanyB | 81.100.2.3
2001:81:2:0000::3 |
| CompanyC | 80.1.1.3
2001:80:1::3 |
| CompanyD | 82.1.1.3
2001:82:1::3 |
| Internet | 0.0.0.0
2003:A::1 |

OSPF 

| Router | Router-id (for ospf) |
| --- | --- |
| OPorto | 1.1.1.1 |
| Lisbon | 2.2.2.2 |
| Aveiro | 3.3.3.3 |
| Ilhavo | 4.4.4.4 |

**Madrid** 

| interface | destination | dest. IP |
| --- | --- | --- |
| f0/0 | CompanyD | 82.1.1.3
2001:82:1::3 |
| f0/1 | OPorto | 15.0.0.2
2002:F::2/64 |
| f1/0 | Lisbon | 14.0.0.2 
2002:E::2/64  |
| f2/0 | Roma | 13.0.0.1 
2002:D::1/64 |
| Lo0 | Internet | 0.0.0.0
2003:A::1/64 |

```sql
conf t
ipv6 unicast-routing

int f0/0
no shut
ipv6 enable
ipv6 address 2001:82:1::1/48
ip address 82.1.1.1 255.255.192.0

int f0/1
no shut
ipv6 enable
ipv6 address 2002:F::1/64
ip address 15.0.0.1 255.255.255.0

int f1/0
no shut
ipv6 enable
ipv6 address 2002:E::1/64
ip address 14.0.0.1 255.255.255.0

int f2/0
no shut
ipv6 enable
ipv6 address 2002:D::2/64
ip address 13.0.0.2 255.255.255.0
 
int Lo0
ipv6 enable
ipv6 address 2003:A::1/64
ip address 20.0.0.1 255.255.255.252
end
write

conf t
router bgp 3000
neighbor 15.0.0.2 remote-as 1000
neighbor 14.0.0.2  remote-as 1000
neighbor 13.0.0.1  remote-as 2000

address-family ipv4
neighbor 15.0.0.2 activate
neighbor 14.0.0.2 activate
neighbor 13.0.0.1 activate

network 82.1.128.0 mask 255.255.192.0
network 13.0.0.0 mask 255.255.255.0
network 14.0.0.0 mask 255.255.255.0
network 15.0.0.0 mask 255.255.255.0
network 20.0.0.0 mask 255.255.255.252
default-information originate
exit-address-family

neighbor 2002:F::2 remote-as 1000
neighbor 2002:E::2  remote-as 1000
neighbor 2002:D::1  remote-as 2000

address-family ipv6
neighbor 15.0.0.2 activate
neighbor 14.0.0.2 activate
neighbor 13.0.0.1 activate
neighbor 2002:F::2 activate
neighbor 2002:E::2 activate
neighbor 2002:D::1 activate

network 2001:82:1::0/48
network 2002:D::0/64 
network 2002:E::0/64
network 2002:F::0/64
network 2003:A::0/64
default-information originate
exit-address-family

end
write
```

**Roma**

| interface | destination | dest. IP |
| --- | --- | --- |
| f0/0 | CompanyC | 80.1.1.3
2001:80:1::3 |
| f0/1 | OPorto | 12.0.0.2
2002:C::2 |
| f1/0 | Lisbon | 11.0.0.2
2002:B::2 |
| f2/0 | Madrid | 13.0.0.2
2002:D::2 |
| Lo1 | Internet | 0.0.0.0
2003:A::1/64 |

```sql
conf t
ipv6 unicast-routing

int f0/0
no shut
ipv6 enable
ipv6 address 2001:80:1::1/48
ip address 80.1.1.1 255.255.255.0

int f0/1
no shut
ipv6 enable
ipv6 address 2002:C::1/64
ip address 12.0.0.1 255.255.255.0

int f1/0
no shut
ipv6 enable
ipv6 address 2002:B::1/64
ip address 11.0.0.1 255.255.255.0

int f2/0
no shut
ipv6 enable
ipv6 address 2002:D::1/64
ip address 13.0.0.1 255.255.255.0
exit

int Lo1
ipv6 enable
ipv6 address 2003:A::2/64
ip address 20.0.0.2 255.255.255.252
end 
write

conf t
router bgp 2000
neighbor 12.0.0.2 remote-as 1000
neighbor 11.0.0.2 remote-as 1000
neighbor 13.0.0.2 remote-as 3000

address-family ipv4
neighbor 12.0.0.2 activate
neighbor 11.0.0.2 activate
neighbor 13.0.0.2 activate

network 80.1.1.0 mask 255.255.255.0
network 12.0.0.0 mask 255.255.255.0
network 11.0.0.0 mask 255.255.255.0
network 13.0.0.0 mask 255.255.255.0
network 20.0.0.0 mask 255.255.255.252
default-information originate
exit-address-family

neighbor 2002:C::2 remote-as 1000
neighbor 2002:B::2 remote-as 1000
neighbor 2002:D::2 remote-as 3000

address-family ipv6
neighbor 12.0.0.2 activate
neighbor 11.0.0.2 activate
neighbor 13.0.0.2 activate
neighbor 2002:C::2 activate
neighbor 2002:B::2 activate
neighbor 2002:D::2 activate

network 2001:80:1::0/48
network 2002:C::0/64
network 2002:B::0/64
network 2002:D::0/64
network 2003:A::0/64
default-information originate
exit-address-family

end
write
```

**OPorto**

| interface | destination | dest. IP |
| --- | --- | --- |
| f0/0 | CompanyA@Porto | 81.100.1.3
2001:81:1:0000::3 |
| f0/1 | Madrid | 15.0.0.1
2002:F::1 |
| f1/0 | Roma | 12.0.0.1
2002:C::1 |
| f2/0 | Core | Aveiro/Lisbon
10.0.0.3/10.0.0.2
2002:A::3/2002:A::2 |

```sql
conf t
ip cef
mpls traffic-eng tunnels
ipv6 unicast-routing
ipv6 router ospf 1
router-id 1.1.1.1

int f0/0
no shut

ipv6 enable
ipv6 address 2001:81:1:0000::1/52
ip address 81.100.1.1 255.255.255.192
ipv6 ospf 1 area 0
ip ospf 1 area 0
mpls traffic-eng tunnels
ip rsvp bandwidth 512 512

int f0/1
no shut

ipv6 enable
ipv6 address 2002:F::2/64
ip address 15.0.0.2 255.255.255.0
ipv6 ospf 1 area 0
ip ospf 1 area 0
mpls traffic-eng tunnels
ip rsvp bandwidth 512 512

int f1/0
no shut
 
ipv6 enable
ipv6 address 2002:C::2/64
ip address 12.0.0.2 255.255.255.0
ipv6 ospf 1 area 0
ip ospf 1 area 0
mpls traffic-eng tunnels
ip rsvp bandwidth 512 512
 
int f2/0
no shut

ipv6 enable
ipv6 address 2002:A::1/64
ip address 10.0.0.1 255.255.255.0
ipv6 ospf 1 area 0
ip ospf 1 area 0
mpls traffic-eng tunnels
ip rsvp bandwidth 512 512

int Lo99
no shut
ipv6 enable
ipv6 address 2020:0::1/128
ip address 99.0.0.1 255.255.255.255
ipv6 ospf 1 area 0
ip ospf 1 area 0
 
exit

int Lo50
no shut
ipv6 enable
ipv6 address 2020:0::4/128
ip address 101.0.0.1 255.255.255.255
ipv6 ospf 1 area 0
ip ospf 1 area 0
 
exit
end
write

conf t
router bgp 1000
neighbor 15.0.0.1 remote-as 3000
neighbor 12.0.0.1 remote-as 2000
neighbor 10.0.0.3 remote-as 1000
neighbor 10.0.0.2 remote-as 1000

address-family ipv4
neighbor 15.0.0.1 activate
neighbor 12.0.0.1 activate
neighbor 10.0.0.3 activate
neighbor 10.0.0.2 activate

network 81.100.1.0 mask 255.255.255.192
network 12.0.0.0 mask 255.255.255.0
network 15.0.0.0 mask 255.255.255.0
network 10.0.0.0 mask 255.255.255.0
default-information originate
exit-address-family

neighbor 2002:F::1 remote-as 3000
neighbor 2002:C::1 remote-as 2000
neighbor 2002:A::3 remote-as 1000
neighbor 2002:A::2 remote-as 1000

address-family ipv6
neighbor 15.0.0.1 activate
neighbor 12.0.0.1 activate
neighbor 10.0.0.3 activate
neighbor 10.0.0.2 activate
neighbor 2002:F::1 activate
neighbor 2002:C::1 activate
neighbor 2002:A::3 activate
neighbor 2002:A::2 activate

network 2001:81:1:0000::0/48
network 2002:C::0/64
network 2002:F::0/64
network 2002:A::0/64
default-information originate
exit-address-family

ip as-path access-list 1 permit ^$
router bgp 1000
redistribute ospf 1
neighbor 15.0.0.1 filter-list 1 out
neighbor 12.0.0.1 filter-list 1 out
neighbor 10.0.0.3 filter-list 1 out
neighbor 10.0.0.2 filter-list 1 out
neighbor 2002:F::1 filter-list 1 out
neighbor 2002:C::1 filter-list 1 out
neighbor 2002:A::3 filter-list 1 out
neighbor 2002:A::2 filter-list 1 out

end
write

conf t
router ospf 1
redistribute bgp 1000 subnets
mpls traffic-eng area 0
mpls traffic-eng router-id Loopback 99
end
write

#Tunel para ilhavo
conf t
crypto isakmp policy 30
authentication pre-share 
crypto isakmp key companya address 81.100.1.129 
crypto ipsec transform-set authT ah-sha-hmac 
crypto ipsec transform-set cipherT esp-des 
crypto ipsec transform-set auth_ciphT ah-sha-hmac esp-des 
crypto ipsec profile ARipsec 
set transform-set authT cipherT auth_ciphT

int Tunnel 1
ip unnumbered Lo99
tunnel source 81.100.1.1
tunnel destination 81.100.1.129
tunnel mode ipsec ipv4
tunnel protection ipsec profile ARipsec
exit
ip route 100.0.0.1 255.255.255.255 Tunnel 1

int Tunnel 2
ip unnumbered Lo99
tunnel source 2002:A::1
tunnel destination 2002:E:1::2
tunnel mode ipsec ipv4
tunnel protection ipsec profile ARipsec
exit
ipv6 route 2020:0::4/128 Tunnel 2

access-list 103 permit ip host 81.100.1.9 host 81.100.1.139 

class-map match-all Voip
match access-group 103

policy-map EDGE
class Voip
police 2000000 2000000 2000000 conform-action set-dscp-transmit 0
int f0/1
service-policy output EDGE

```

- MPLS VPN
    
    ```sql
    conf t
    ip vrf CompanyAVPN_Porto
    rd 65000:1 
    route-target import 65000:1 
    route-target export 65000:1 
    ip vrf CompanyAVPN_Ilhavo
    rd 65000:2
    route-target import 65000:2 
    route-target export 65000:2 
    
    int Lo98
    no shut
    ipv6 enable
    ipv6 address 2023:0::2/128
    ip vrf forwarding CompanyAVPN_Ilhavo
    ip address 96.0.0.2 255.255.255.252 
    
    int Lo97
    no shut
    ipv6 enable
    ipv6 address 2023:0::3/128
    ip vrf forwarding CompanyAVPN_Porto
    ip address 99.100.1.3 255.255.255.252
    
    exit
    
    end
    write
    
    ```
    
    ```sql
    
    router bgp 65000
    address-family vpnv4
    address-family vpnv4
    neighbor 96.0.0.1 activate
    neighbor 96.0.0.1 send-community both
    exit-address-family
    
    end
    write
    ```
    

```sql
crypto isakmp policy 30
authentication pre-share 
crypto isakmp key companya address 81.100.1.1 
crypto ipsec transform-set authT ah-sha-hmac 
crypto ipsec transform-set cipherT esp-des 
crypto ipsec transform-set auth_ciphT ah-sha-hmac esp-des 
crypto ipsec profile ARipsec 
set transform-set authT cipherT auth_ciphT

```

**Lisbon**

| interface | destination | dest. IP |
| --- | --- | --- |
| f0/0 | CompanyB | 81.100.2.3
2001:81:2:0000::3 |
| f0/1 | Roma | 11.0.0.1
2002:B::1 |
| f1/0 | Madrid | 14.0.0.1
2002:E::1 |
| f2/0 | Core | Aveiro/OPorto
10.0.0.3/10.0.0.1
2002:A::3/2002:A::1 |

```sql
conf t
ip cef
mpls traffic-eng tunnels
ipv6 unicast-routing
ipv6 router ospf 1
router-id 2.2.2.2

int f0/0
no shut
ipv6 enable
ipv6 address 2001:81:2:0000::1/52
ip address 81.100.2.1 255.255.255.192
ipv6 ospf 1 area 0
ip ospf 1 area 0
mpls traffic-eng tunnels
ip rsvp bandwidth 512 512

int f0/1
no shut
ipv6 enable
ipv6 address 2002:B::2/64
ip address 11.0.0.2 255.255.255.0
ipv6 ospf 1 area 0
ip ospf 1 area 0
mpls traffic-eng tunnels
ip rsvp bandwidth 512 512

int f1/0
no shut
ipv6 enable
ipv6 address 2002:E::2/64
ip address 14.0.0.2 255.255.255.0
ipv6 ospf 1 area 0
ip ospf 1 area 0
mpls traffic-eng tunnels
ip rsvp bandwidth 512 512

int f2/0
no shut
ipv6 enable
ipv6 address 2002:A::2/64
ip address 10.0.0.2 255.255.255.0
ipv6 ospf 1 area 0
ip ospf 1 area 0
mpls traffic-eng tunnels
ip rsvp bandwidth 512 512

int Lo99
no shut
ipv6 enable
ipv6 address 2021:0::1/128
ip address 98.0.0.1 255.255.255.255
ipv6 ospf 1 area 0
ip ospf 1 area 0

exit

end
write

conf t
router bgp 1000
redistribute ospf 1 
neighbor 11.0.0.1 remote-as 2000
neighbor 14.0.0.1 remote-as 3000
neighbor 10.0.0.3 remote-as 1000
neighbor 10.0.0.1 remote-as 1000

address-family ipv4
neighbor 11.0.0.1 activate
neighbor 14.0.0.1 activate
neighbor 10.0.0.3 activate
neighbor 10.0.0.1 activate

network 81.100.2.0 mask 255.255.255.192
network 14.0.0.0 mask 255.255.255.0
network 11.0.0.0 mask 255.255.255.0
network 10.0.0.0 mask 255.255.255.0
default-information originate
exit-address-family

neighbor 2002:B::1 remote-as 2000
neighbor 2002:E::1 remote-as 3000
neighbor 2002:A::3 remote-as 1000
neighbor 2002:A::1 remote-as 1000

address-family ipv6
neighbor 11.0.0.1 activate
neighbor 14.0.0.1 activate
neighbor 10.0.0.3 activate
neighbor 10.0.0.1 activate
neighbor 2002:B::1 activate
neighbor 2002:E::1 activate
neighbor 2002:A::3 activate
neighbor 2002:A::1 activate

network 2001:81:2:0000::0/52
network 2002:E::0/64
network 2002:B::0/64
network 2002:A::0/64
default-information originate
exit-address-family

ip as-path access-list 1 permit ^$
router bgp 1000
neighbor 11.0.0.1 filter-list 1 out
neighbor 14.0.0.1 filter-list 1 out
neighbor 10.0.0.3 filter-list 1 out
neighbor 10.0.0.1 filter-list 1 out
neighbor 2002:B::1 filter-list 1 out
neighbor 2002:E::1 filter-list 1 out
neighbor 2002:A::3 filter-list 1 out
neighbor 2002:A::1 filter-list 1 out

end
write

conf t
router ospf 1
redistribute bgp 1000 subnets
mpls traffic-eng area 0
mpls traffic-eng router-id Loopback 99
end
write
```

**Aveiro**

| interface | destination | dest. IP |
| --- | --- | --- |
| f0/0 | Ílhavo | 10.0.1.2
2002:E:1::2 |
| f0/1 | Core | OPorto/Lisbon
10.0.0.1/10.0.0.2
2002:A::1/2002:A::2 |

```sql
conf t
ip cef
mpls traffic-eng tunnels
ipv6 unicast-routing
ipv6 router ospf 1
router-id 3.3.3.3

int f0/0
no shut
ipv6 enable
ipv6 address 2002:E:1::1/64
ip address 10.0.1.1 255.255.255.0
ipv6 ospf 1 area 0
ip ospf 1 area 0
mpls traffic-eng tunnels
ip rsvp bandwidth 512 512

int f0/1
no shut
ipv6 enable
ipv6 address 2002:A::3/64
ip address 10.0.0.3 255.255.255.0
ipv6 ospf 1 area 0
ip ospf 1 area 0
mpls traffic-eng tunnels
ip rsvp bandwidth 512 512

int Lo99
no shut
ipv6 enable
ipv6 address 2022:0::1/128
ip address 97.0.0.1 255.255.255.255
ipv6 ospf 1 area 0
ip ospf 1 area 0
exit

end
write

conf t
router ospf 1
mpls traffic-eng area 0
mpls traffic-eng router-id Loopback 99
end
write

conf t

access-list 101 permit ip any 80.1.1.0 0.0.0.255
route-map toC4 permit 10
match ip address 101
set ip next-hop 10.0.0.2

int f0/0
ip policy route-map toC4

end
write

```

```sql

conf t
router bgp 1000
neighbor 10.0.1.2 remote-as 1000
neighbor 10.0.0.1 remote-as 1000
neighbor 10.0.0.2 remote-as 1000

address-family ipv4
neighbor 10.0.1.2 activate
neighbor 10.0.0.1 activate
neighbor 10.0.0.2 activate
neighbor 10.0.1.2 route-reflector-client

network 10.0.1.0 mask 255.255.255.0
network 10.0.0.0 mask 255.255.255.0
default-information originate
exit-address-family

neighbor 2002:E:1::2 remote-as 1000
neighbor 2002:A::1 remote-as 1000
neighbor 2002:A::2 remote-as 1000

address-family ipv6
neighbor 10.0.1.2 activate
neighbor 10.0.0.1 activate
neighbor 10.0.0.2 activate
neighbor 10.0.1.2 route-reflector-client
neighbor 2002:E:1::2 activate
neighbor 2002:A::1 activate
neighbor 2002:A::2 route-reflector-client
neighbor 2002:A::2 activate

network 2002:E:1::0/64
network 2002:A::0/64
default-information originate
exit-address-family

end
write
```

**Ilhavo**

| interface | destination | dest. IP |
| --- | --- | --- |
| f0/0 | CompanyA@Ilhavo | 81.100.1.131
2001:81:1:2000::3 |
| f0/1 | Aveiro | 10.0.1.1
2002:E:1::1 |

```sql
conf t
ip cef
mpls traffic-eng tunnels
ipv6 unicast-routing
ipv6 router ospf 1
router-id 4.4.4.4

int f0/0
no shut
ipv6 enable
ipv6 address 2001:81:2:2000::2/52
ip address 81.100.1.129 255.255.255.192
ipv6 ospf 1 area 0
ip ospf 1 area 0
mpls traffic-eng tunnels
ip rsvp bandwidth 512 512

int f0/1
no shut
ipv6 enable
ipv6 address 2002:E:1::2/64
ip address 10.0.1.2 255.255.255.0
ipv6 ospf 1 area 0
ip ospf 1 area 0
mpls traffic-eng tunnels
ip rsvp bandwidth 512 512

 
int Lo99
no shut
ipv6 enable
ipv6 address 2023:0::1/128
ip address 96.0.0.1 255.255.255.255
ipv6 ospf 1 area 0
ip ospf 1 area 0

int Lo50
no shut
ipv6 enable
ipv6 address 2023:0::4/128
ip address 100.0.0.1 255.255.255.255
ipv6 ospf 1 area 0
ip ospf 1 area 0

exit

end
write

conf t
router ospf 1
mpls traffic-eng area 0
mpls traffic-eng router-id Loopback 99
end
write

conf t

access-list 101 permit ip 81.100.1.0 host 80.1.1.3
access-list 101 permit ip 81.100.1.128 0.0.0.63 80.1.1.0 0.0.0.255

ipv6 access-list C6
permit ip any host 2001:80:1::3
exit

access-list 103 permit ip any host 82.1.1.3

ipv6 access-list D6
permit ip any host 2001:82:1::3
exit

route-map toD4 permit 10
match ip address 103
set ip next-hop 10.0.0.1
exit

route-map toC6 permit 10
match ip address C6
set ipv6 next-hop 2002:A::2
exit

route-map toC4 permit 10
match ip address 101
set ip next-hop 10.0.0.2
exit

route-map toD6 permit 10
match ip address D6
set ipv6 next-hop 2002:A::1
exit

int f0/0
ip policy route-map toC4
ip policy route-map toC6
ip policy route-map toD4
ip policy route-map toD6
end 
write

#Tunel para porto
conf t
crypto isakmp policy 30
authentication pre-share 
crypto isakmp key companya address 81.100.1.1 
crypto ipsec transform-set authT ah-sha-hmac 
crypto ipsec transform-set cipherT esp-des 
crypto ipsec transform-set auth_ciphT ah-sha-hmac esp-des 
crypto ipsec profile ARipsec 
set transform-set authT cipherT auth_ciphT

int Tunnel 0
ip unnumbered Lo99
tunnel source 81.100.1.129
tunnel destination 81.100.1.1 
tunnel mode ipsec ipv4
tunnel protection ipsec profile ARipsec
exit
ip route 101.0.0.1 255.255.255.255 Tunnel 0

int Tunnel 2
ip unnumbered Lo99
tunnel source 2002:E:1::2
tunnel destination 2002:A::1
tunnel mode ipsec ipv4
tunnel protection ipsec profile ARipsec
exit
ipv6 route 2023:0::4/128 Tunnel 2

access-list 103 permit ip host 81.100.1.139 host 81.100.1.9 

class-map match-all Voip
match access-group 103

policy-map EDGE
class Voip
police 2000000 2000000 2000000 conform-action set-dscp-transmit 0
int f0/1
service-policy output EDGE

```

```sql

#OLD

#Tunel para porto
conf t

int Tunnel 14
ip unnumbered Lo99
tunnel source 81.100.1.129
tunnel destination 81.100.1.1 
tunnel mode  mpls traffic-eng 
tunnel protection ipsec profile ARipsec
tunnel mpls traffic-eng autoroute announce
tunnel mpls traffic-eng bandwidth 2000
tunnel mpls traffic-eng path-option 1 dynamic
ip route 81.100.1.3 255.255.255.255 Tunnel VC14

int Tunnel 16
ip unnumbered Lo99
tunnel source 2001:81:2:2000::2
tunnel destination 2001:81:1:0000::1
tunnel mode ipsec mpls traffic-eng
tunnel protection ipsec profile ARipsec
tunnel mpls traffic-eng autoroute announce
tunnel mpls traffic-eng bandwidth 2000
tunnel mpls traffic-eng path-option 1 dynamic
ip route 2001:81:1:0000::3/128 Tunnel VC16
end 
write

#OLD

int Tunnel 24
ip unnumbered Lo99
tunnel source 81.100.1.1
tunnel destination 81.100.1.129
tunnel mode ipsec mpls traffic-eng
tunnel protection ipsec profile ARipsec
tunnel mpls traffic-eng autoroute announce
tunnel mpls traffic-eng bandwidth 2000
tunnel mpls traffic-eng path-option 1 dynamic
ip route 81.100.1.131 255.255.255.255 Tunnel VC14

int Tunnel 26
ip unnumbered Lo99
tunnel source 2001:81:1:0000::1
tunnel destination 2001:81:2:2000::2
tunnel mode ipsec mpls traffic-eng
tunnel protection ipsec profile ARipsec
tunnel mpls traffic-eng autoroute announce
tunnel mpls traffic-eng bandwidth 2000
tunnel mpls traffic-eng path-option 1 dynamic
ip route 2001:81:1:2000::3/128 Tunnel VC14
```

- MPLS VPN
    
    ```sql
    conf t
    ip vrf CompanyAVPN_Ilhavo
    rd 65000:2
    route-target import 65000:2 
    route-target export 65000:2 
    ip vrf CompanyAVPN_Porto
    rd 65000:1 
    route-target import 65000:1 
    route-target export 65000:1 
    
    int Lo98
    no shut
    ipv6 enable
    ipv6 address 2023:0::2/128
    ip vrf forwarding CompanyAVPN_Ilhavo
    ip address 96.0.0.2 255.255.255.252 
    
    int Lo97
    no shut
    ipv6 enable
    ipv6 address 2023:0::3/128
    ip vrf forwarding CompanyAVPN_Porto
    ip address 96.0.0.3 255.255.255.252
    
    exit
    
    end
    write
    
    conf t
    router bgp 65000
    neighbor 
    ```
    

CompanyA@Porto

```sql
ip 81.100.1.3 81.100.1.1
ip 2001:81:1:0000::3
save
```

CompanyA@Ilhavo

```sql
ip 81.100.1.131 81.100.1.129
ip 2001:81:1:2000::3
save
```

CompanyB

```sql
ip 81.100.2.3 81.100.2.1
ip 2001:81:2:0000::3
save
```

CompanyC

```sql
ip 80.1.1.3 80.1.1.1
ip 2001:80:1::3
save
```

CompanyD

```sql
ip 82.1.1.3 82.1.1.1
ip 2001:82:1::3
save
```

- Ex.3
    
    ```sql
    conf t
    no route-map toC4
    no access-list 101
    end
    
    conf t
    access-list 101 permit ip any host 80.1.1.3
    
    route-map toC4 permit 10
    match ip address 101
    set ip next-hop 10.0.0.2 
    exit
    
    int f0/1
    ip policy route-map toC4
    
    int f0/0
    ip policy route-map toC4
    
    end 
    write
    ```
    
    ```sql
    ipv6 access-list C6
    permit ipv6 any host 2001:80:1::3
    exit
    
    ipv6 access-list D6
    permit ipv6 any host 2001:82:1::3
    exit
    
    route-map toC6 permit 10
    set ipv6 next-hop 2002:A::2
    match ipv6 address C6
    exit
    
    route-map toD6 permit 10
    set ipv6 next-hop 2002:A::1
    match ipv6 address D6
    exit
    
    ```
    
    ```sql
    
    conf t
    
    access-list 103 permit ip any 82.1.1.0 0.0.0.255
    
    route-map toD4 permit 20
    set ip next-hop recursive 10.0.0.1
    match ip address 103
    exit
    
    int f0/1
    ip policy route-map toD4
    
    int f0/0
    ip policy route-map toD4
    ```
    

```sql

conf t

router bgp 1000
neighbor 10.0.1.1 remote-as 1000 

address-family ipv4
neighbor 10.0.1.1 activate

network 81.100.1.128 mask 255.255.255.192
network 10.0.1.0 mask 255.255.255.0
default-information originate
exit-address-family

neighbor 2002:E:1::1 remote-as 1000 

address-family ipv6
neighbor 10.0.1.1 activate
neighbor 2002:E:1::1 activate

network 2001:81:1:2000::0/52
network 2002:E:1::0/64
default-information originate
exit-address-family

end
write
```

```sql
conf t
ip cef
mpls traffic-eng tunnels
ipv6 unicast-routing
ipv6 router ospf 1
router-id 4.4.4.4

int f0/0
no shut
ipv6 enable
ipv6 address 2001:81:2:2000::2/52
ip address 81.100.1.129 255.255.255.192
ipv6 ospf 1 area 0
ip ospf 1 area 0
mpls traffic-eng tunnels
ip rsvp bandwidth 512 512

int f0/1
no shut
ipv6 enable
ipv6 address 2002:E:1::2/64
ip address 10.0.1.2 255.255.255.0
ipv6 ospf 1 area 0
ip ospf 1 area 0
mpls traffic-eng tunnels
ip rsvp bandwidth 512 512

 
int Lo99
no shut
ipv6 enable
ipv6 address 2023:0::1/128
ip address 96.0.0.1 255.255.255.255
ipv6 ospf 1 area 0
ip ospf 1 area 0

exit

end
write

conf t
router ospf 1
mpls traffic-eng area 0
mpls traffic-eng router-id Loopback 99
end
write

conf t

access-list 101 permit ip 81.100.1.0 host 80.1.1.3
access-list 101 permit ip 81.100.1.128 0.0.0.63 80.1.1.0 0.0.0.255

ipv6 access-list C6
permit ip any host 2001:80:1::3
exit

access-list 103 permit ip any host 82.1.1.3

ipv6 access-list D6
permit ip any host 2001:82:1::3
exit

route-map toD4 permit 10
match ip address 103
set ip next-hop 10.0.0.1
exit

route-map toC6 permit 10
match ip address C6
set ipv6 next-hop 2002:A::2
exit

route-map toC4 permit 10
match ip address 101
set ip next-hop 10.0.0.2
exit

route-map toD6 permit 10
match ip address D6
set ipv6 next-hop 2002:A::1
exit

int f0/0
ip policy route-map toC4
ip policy route-map toC6
ip policy route-map toD4
ip policy route-map toD6
end 
write

```
