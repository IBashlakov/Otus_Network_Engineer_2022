## Lab 03

### DHCPv4/v6 и SLAAC

**Цели лабораторной работы:** 

* рассмотреть работу протокола DHCP для IPv4/v6;
* исследовать работу технологии SLAAC;
* получить практический опыт настройки различных видов работы протокола DHCP.

**Топология сети:**

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab03/Topology.png?raw=true)

## Раздел 1. DHCPv4

### Часть 1. Настройка сети и конфигурация портов устройств


**1.1**. **Произведем разделение сети 192.168.1.0/24 в соответствии с требованиями методички:**


Подсеть "A" 58 (62) хостов (Vlan100)- 192.168.1.0/26 gw 192.168.1.1

Подсеть "B" 28 (30) хостов (Vlan200) - 192.168.1.64/27 gw 192.168.1.65

Подсеть "C" 12 (14) хостов - 192.168.1.96/28 gw 192.168.1.97

Занесем данные в таблицу:


**Таблица адресации**


| Device | Interface | IP Address |	Subnet Mask |	Default Gateway |
|---|---|---|---|---|
| R1 | Et0/0	 | 10.0.0.1	| 255.255.255.252	| N/A |
| R1 | Et0/1 |	N/A |	N/A |	N/A |
| R1 | Et0/1.100	| 192.168.1.1 |	255.255.255.192 |	N/A |
| R1	| Et/0/1.200	 | 192.168.1.65 | 255.255.255.224 |	N/A |
| R1	| Et/0/1.1000 |	N/A |	N/A |	N/A |
| R2	| Et 0/0 | 10.0.0.2 | 255.255.255.252 | N/A |
| R2	| Et 0/1 | 192.168.1.97 | 255.255.255.240 | N/A |
| S1	| VLAN 200 |	192.168.1.66	| 255.255.255.224 | 192.168.1.65 |
| S2	| VLAN 1 | 192.168.1.98 |	255.255.255.240	| 192.168.1.97 |
| PC-A | NIC | DHCP |	DHCP |	DHCP |
| PC-B | NIC	| DHCP |	DHCP |	DHCP |

**Таблица VLAN**

| VLAN	| Name |	Interface Assigned |
|---|---|---|
| 1	 | N/A | S2: Et0/3 |
| 100	| Clients	| S1: Et0/3 |
| 200 |	Management |	S1: VLAN 200 |
| 999	| Parking_Lot	 | S1: Et0/0, Et0/2, Et0/4 |
| 1000	 | Native | N/A |

**1.2.** **Произведем базовую настройку устройств**


```
hostname "device_name"
no ip domain lookup
enable secret class
line console 0
password cisco
login
line vty 0 4
password cisco
login
service password-encryption
banner motd $ Authorized Access Only! $
```

**1.3. Настроим интерфейсы на устройствах**

R1:

```
R1(config)#interface Et0/1
R1(config-if)#no shutdown
R1(config-if)#interface Et0/1.100
R1(config-subif)#encapsulation dot1q 100
R1(config-subif)#ip address 192.168.1.1 255.255.255.192
R1(config-subif)#no shutdown
R1(config-subif)#interface Et0/1.200
R1(config-subif)#encapsulation dot1q 200
R1(config-subif)#ip address 192.168.1.65 255.255.255.224
R1(config-subif)#no shutdown
R1(config-subif)#interface Et0/1.1000
R1(config-subif)#encapsulation dot1q 1000
R1(config-subif)#no shutdown
R1(config-subif)#end
```
а так же интерфейс Et0/0

```
R1(config)#interface Et0/0
R1(config-if)#ip address 10.0.0.1 255.255.255.252
R1(config-if)#no shutdown
R1(config-if)#end
```
затем произведем настройку интерфейсов устройства R2:

```
R2(config)#interface Et0/0
R2(config-if)#ip address 10.0.0.2 255.255.255.252
R2(config-if)#no shutdown
R2(config-if)#interface Et0/1
R2(config-if)#ip address 192.168.1.97 255.255.255.240
R2(config-if)#no shutdown
R2(config-if)#end
```

После настройки интерфейсов укажем статические маршруты на обоих роутерах:

```
R1(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.2
```

```
R2(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.1
```

Выполним проверку маршрутизации:

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab03/ICMP_test.png?raw=true)


Произведем настройку интерфейсов на S1:

```
S1(config)#interface Vlan200
S1(config-if)#ip address 192.168.1.66 255.255.255.224
S1(config-if)#no shutdown
S1(config-if)#end
S1#conf t
S1(config)#vlan 999
S1(config-vlan)#name ParkingOfLot
S1(config-vlan)#end
S1#conf t
S1(config)#interface range Et0/0, Et0/2
S1(config-if-range)#switchport mode access
S1(config-if-range)#switchport access vlan 999
S1(config-if-range)#end
S1#conf t
S1(config)#vlan 100
S1(config-vlan)#name Clients
S1(config-vlan)#interface Et0/3
S1(config-if)#switchport mode acces
S1(config-if)#switchport acces vlan 100
S1(config-if)#end
S1#conf t
S1(config)#interface Et0/1
S1(config-if)#switchport trunk encapsulation dot1q
S1(config-if)#switchport mode trunk
S1(config-if)#switchport trunk native vlan 1000
S1(config-if)#switchport trunk allowed vlan 100,200,1000
S1(config-if)#end
```

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab03/S1_VLAN.png?raw=true)

И на S2:

```
S2(config)#interface Vlan1
S2(config-if)#ip address 192.168.1.98 255.255.255.240
S2(config-if)#no shutdown
S2(config-if)#end
S2(config)#interface range Et0/0, Et0/2
S2(config-if-range)#shutdown
S2(config)#end
```
### Часть 2. Настройка DHCPv4 

Зададим пуллы адресов на маршрутизаторе R1 для сети A и C и настроим лизы согласно методичке:


```
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#ip dhcp excluded-address 192.168.1.1 192.168.1.62
R1(config)#ip dhcp pool Subnet A
R1(dhcp-config)#network 192.168.1.0 255.255.255.192
R1(dhcp-config)#default-router 192.168.1.1
R1(dhcp-config)#lease 2 12 30
R1(dhcp-config)#end
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#ip dhcp excluded-addres 192.168.1.97 192.168.1.110
R1(config)#ip dhcp pool Subnet C
R1(dhcp-config)#network 192.168.1.96 255.255.255.240
R1(dhcp-config)#default-router 192.168.1.97
R1(dhcp-config)#lease 2 12 30
R1(dhcp-config)#end
```
![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab03/DHCP_POOLS.png?raw=true)

