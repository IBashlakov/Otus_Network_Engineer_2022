## Lab01

## Настройка Vlan и маршрутизации между Vlan методом "Router-on-stick"

Логическая схема сети

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab01/Logical%20scheme.png?raw=true)

Физическая схема сети

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab01/Physical%20scheme.png?raw=true)

Настройка производилась следующим образом:

Базовая настройка устройств (задание hostname, прав доступа, баннеров, паролей доступа);
Задание ip-адресов на интерфейсах хостов PC-A и PC-B;
Создание SVI-интерфейсов Vlan3 на коммутаторах и назначение ip-адресов;
Настройка Vlan на коммутаторах;
Настройка trunk интерфейсов на коммутаторах;
Настройка sub-интерфесов на маршрутизаторе и назначение им ip-адресов.

Конфигурация коммутатора S1:

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab01/S1_config.png?raw=true)

Конфигурация коммутатора S2:

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab01/S2_config.png?raw=true)

Настройка субинтерфейсов на маршрутизаторе R1:

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab01/R1_subinterfaces_config.png?raw=true)

Конфигурация маршрутизатора R1:

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab01/R1_config.png?raw=true)


Хост PC-A

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab01/PC-A%20Tests.png?raw=true)

Хост PC-B

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab01/PC-B%20Tests.png?raw=true)