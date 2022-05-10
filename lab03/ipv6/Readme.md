###Lab03.2

##DHCP IPv6/SLAAC

Цели лабораторной работы описаны в [первой части отчета.](https://github.com/IBashlakov/Otus_Network_Engineer_2022/tree/main/lab03)

Топология сети аналогична первой части лабораторной работы и представлена на схеме ниже:

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/raw/main/lab03/ipv4/Topology.png?raw=true)

### **Таблица адресации**


| Device	 | Interface | IPv6 Address |
|----------|-----------|-------------|
| R1 | Et0/0 | 2001:db8:acad:2::1/64	|
| | | fe80::1 |
| | Et0/1 | 2001:db8:acad:1::1/64 |
| | | fe80::1 |
| R2 | Et0/0 | 2001:db8:acad:2::2/64	|
| | | fe80::2 |
| | Et0/1 | 2001:db8:acad:3::1/64 |
| | | fe80::1 |
|PC-A| Eth0 | DHCP |
|PC-B| Eth0 | DHCP |


Базовая настройка маршрутизаторов и коммутаторов аналогична первой части работы "*LINK*"

### **Настройка интерфейсов и маршрутизации**


### R1

```
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#ipv6 unicast-routing
R1(config)#interface Et0/0
R1(config-if)#ipv6 add 2001:db8:acad:2::1/64
R1(config-if)#ipv6 add fe80::1 link-local
R1(config-if)#no sh
R1(config-if)#interface Et0/1
R1(config-if)#ipv6 add 2001:db8:acad:1::1/64
R1(config-if)#ipv6 add fe80::1 link-local
R1(config-if)#no sh
R1(config-if)#end
R1(config)#ipv6 route ::/0 2001:DB8:ACAD:2::2
R1(config)#exit
```

###R2
```
R2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R2(config)#ipv6 unicast-routing
R2(config)#interface Et0/0
R2(config-if)#ipv6 add 2001:db8:acad:2::2/64
R2(config-if)#ipv6 add fe80::2 link-local
R2(config-if)#no sh
R2(config-if)#interface Et0/1
R2(config-if)#ipv6 add 2001:db8:acad:3::1/64
R2(config-if)#ipv6 add fe80::1 link-local
R2(config-if)#no sh
R2(config-if)#end
R1(config)#ipv6 route ::/0 2001:db8:acad:2::1
```

Затем произведем проверку сетевой связности и маршрутизации, отправив ICMP-запрос на интерфейс Et0/1 маршрутизатора R2:

!!пикча1

Проверим конфигурацию IPv6-адреса на хосте PC-A. Т.к. эмулятор Eve-NG у меня развернут в Яндекс.Облаке и есть трудности с запуском quemu-образов, в качестве хоста я использовал маршрутизатор:

```
Router(config)# ipv6 unicast-routing
Router(config)#interface Et0/1
Router(config-if)# ipv6 address autoconfig
Router(config-if)# ipv6 enable
```

!!пикча2

Как можно видеть адрес присвоен из сети 2001:db8:1::/64. Часть адреса с идентификатором хоста сгенерирована на основе mac-адреса устройства (EUI-64).

## Перейдем к настройке DHCP(ipv6)-сервера

Выполним настройку DHCP сервера на R1 без сохранения состояния, чтобы PC-A мог получать информацию о DNS-сервере и домене:

```
R1(config)#ipv6 dhcp pool R1-STATELESS
R1(config-dhcpv6)#dns-server 2001:db8:acad::1
R1(config-dhcpv6)#domain-name STATELESS.com
R1(config-dhcpv6)#exit
R1(config)#interface g0/0/1
R1(config-if)#ipv6 nd other-config-flag
R1(config-if)#ipv6 dhcp server R1-STATELESS  
```
На PC-A выполним запрос к DNS серверу

!! пикча

А так же проверим доступность интерфейса Et0/0 маршрутизатора R2

!! пикча

Затем произведем настройку DHCP сервера с сохранением состояния на R1:

Создадим пулл R2-STATEFULL для сети 2001:db8:acad:3:aaa::/80

```
R1(config)# ipv6 dhcp pool R2-STATEFUL
R1(config-dhcp)# address prefix 2001:db8:acad:3:aaa::/80
R1(config-dhcp)# dns-server 2001:db8:acad::254
R1(config-dhcp)# domain-name STATEFUL.com   
```

И назначим его на интерфейс Et0/0

```
R1(config)# interface Et0/0
R1(config-if)# ipv6 dhcp server R2-STATEFUL   
```

!!пикча
!!пикча

Произведем настройку ретрансляции на R2:

```
R2(config) # interface Et0/1
R2(config-if)# ipv6 nd managed-config-flag
R2(config-if)# ipv6 dhcp relay destination 2001:db8:acad:2::1 Et0/0
```