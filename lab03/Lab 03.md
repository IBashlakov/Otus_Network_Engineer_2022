## Lab 03

### DHCPv4/v6 и SLAAC

**Цели лабораторной работы:** 

* рассмотреть работу протокола DHCP для IPv4/v6;
* исследовать работу технологии SLAAC;
* получить практический опыт настройки различных видов работы протокола DHCP.

**Топология сети:**

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab03/Topology.png?raw=true)

## Часть 1. DHCP для IPv4.

Произведем разделение сети 192.168.1.0/24 в соответствии с требованиями методички:


Подсеть "A" 58 (62) хостов - 192.168.1.0/26 gw 192.168.1.1

Подсеть "B" 28 (30) хостов - 192.168.1.64/27 gw 192.168.1.65

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
| S2	| VLAN 1 | blank |	blank	| blank |
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


R1

conf t
ip dhcp-pool LAN-1
network 192.168.1.0 255.255.255.0
default-router 192.168.1.1
end

conf t
interface Et0/1.100
encapsulation dot1.q 100
ip address 192.168.1.1 255.255.255.0
end

S1

conf t
interface Vlan 200
ip address 192.168.1.2 255.255.255.0
ip default-gateway 192.168.1.1
no shutdown
end




