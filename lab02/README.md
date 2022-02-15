# Lab 02

# Развертывание коммутируемой сети с резервными каналами

**Цель лабораторной работы**: изучение работы протокола STP и факторов, влияющих на него работу.

Выполненная работа:

Лабораторная работа выполнялась в среде EVE-NG (v. 2.03-112) для эмуляции работы коммунтаторов использовались образы L2-ADVENTERPRICEK9-M-15.2-20150703.bin

### Часть 1. Создание сети и базовая настройка сети

1.1. Создадим лабораторный стенд согласно схеме топологии сети.

Физическая схема подключения:

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab02/Physical%20scheme.png?raw=true)

1.2. Произведем инициализацию и перезагрузку коммутаторов.
1.3. Произведем базовую конфигруцию коммутаторов и настройку SVI-интерфейсов согласно таблице:

| Устройство |	Интерфейс | IP-адреc | Маска подсети |
|------------|------------|----------|---------------|
| S1	| VLAN 1	| 192.168.1.1 |	255.255.255.0 |
| S2	| VLAN 1	| 192.168.1.2	 | 255.255.255.0 |
| S3	| VLAN 1	| 192.168.1.3	 | 255.255.255.0 |

1.3. Проверим корректность настройки стенда с помощью icmp-запросов к каждому устройству.



Затем для демонстрации работы протокола STP отключим все порты на коммутаторах, настроим транковые порты и включим следующие порты:

* S1: Et1/2, Et1/3;
* S2: Et0/0, Et1/2;
* S3: Et0/0, Et1/3.

Spanning-tree на S1:

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab02/S1_ST.png?raw=true)

Он был выбран root-коммутатором, т.к. несмотря на равный приоритет с другими коммутаторами, имеет самое низкое значение mac-адреса.

Spanning-tree на S2:

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab02/S2_ST.png?raw=true)

Spanning-tree на S3:

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab02/S3_ST.png?raw=true)

Логическая схема работы протокола STP:

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab02/ST_1iteration_Scheme.png?raw=true)

Порт Et0/0 на коммутаторе S3 был переведен в состояние "Alt", т.к. имеет наивышую стоимость пути до корневого коммутатора.

Затем уменьшим стоимость порта Et1/3 до 18:

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab02/S3_ST_cost_change.png?raw=true)

Одновременно с этим порт Et0/2 коммутатора S2 перешел в состояние "Alt".
Затем отменим изменения стоимости порта и включим все интерфейсы на всех коммутаторах:

S1:

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab02/S1_ST_all_ports.png?raw=true)

S2:

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab02/S2_ST_all_ports.png?raw=true)

S3:
![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab02/S3_ST_all_ports.png?raw=true)

Мы можем наблюдать, что ближайшие к корневому коммутатору порты выбраны корневыми, а порты с наибольшей стоимостью линков до корневого коммутатора переведены в состояние "Alt".
