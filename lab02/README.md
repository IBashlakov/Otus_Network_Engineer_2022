# Lab 02

# Развертывание коммутируемой сети с резервными каналами

Реализуем схему подключения из трех коммутаторов Cisco 2960:

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab02/Physical%20scheme.png?raw=true)

На каждом коммутаторе произведем следующие настройки:
1. Отключим поиск по dns;
2. Зададим Hostmame;
3. Настроим баннерное сообщение и права доступа;
4. Настроим интерфейсы управления Vlan1.

Конфигурация коммутатора S1:

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab02/S1_interfaces.png?raw=true)

Конфигурация коммутатора S2:

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab02/S2_interfaces.png?raw=true)

Конфигурация коммутатора S3:

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab02/S3_interfaces.png?raw=true)

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
