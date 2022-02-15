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

S1:
![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab02/S1_icmp_test.png?raw=true)

S2:
![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab02/S2_icmp_test.png?raw=true)

S3:
![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab02/S3_icmp_test.png?raw=true)

### Часть 2. Опеределение корневого моста

2.1. Отключим все порты на коомутаторах


```
conf t
interface range Et0/0-4, Et1/1-4
shutdown
end
```


2.2 Настроим подключенные интерфейсы в качестве транковых

S1:

```
conf t
interface range Et0/2-3, Et 1/1-2
switchport trunk encapsulation dot1q
switchport mode trunk
end
```

S2:

```
conf t
interface range Et0/0, Et0/2, Et 1/1-2
switchport trunk encapsulation dot1q
switchport mode trunk
end
```

S3:

```
conf t
interface range Et0/0, Et0/3, Et1/0, Et 1/3
switchport trunk encapsulation dot1q
switchport mode trunk
end
```

2.3. Затем для демонстрации работы протокола STP отключим все порты на коммутаторах, настроим транковые порты и включим следующие порты:

* S1: Et1/2, Et1/3;
* S2: Et0/0, Et1/2;
* S3: Et0/0, Et1/3.

Spanning-tree на S1:

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab02/S1_ST.png?raw=true)

Spanning-tree на S2:

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab02/S2_ST.png?raw=true)

Spanning-tree на S3:

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab02/S3_ST.png?raw=true)

Логическая схема работы протокола STP:

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab02/ST_1iteration_Scheme.png?raw=true)


С учетом выходных данных, поступающих с коммутаторов, ответьте на следующие вопросы:

Какой коммутатор является корневым мостом? Корневый мостом является коммутатор S1

Почему этот коммутатор был выбран протоколом spanning-tree в качестве корневого моста? Коммутатор S1 был выбран в качестве корневого, т.к. несмотря на равный приоритет с другими коммутаторами, имеет самое низкое значение mac-адреса.

Какие порты на коммутаторе являются корневыми портами? У коммутатора S2 им является порт Et1/2, у S3 - Et1/3.

Какие порты на коммутаторе являются назначенными портами? Для корневого коммутатора S1 все активные порты, учавствующие в STP являются Designated. Для коммутатора S2 - порт Ethernet 0/0.

Какой порт отображается в качестве альтернативного и в настоящее время заблокирован? В качестве альтернативного отображается порт Ethernet 0/0 коммутатора S3 по той причине, что стоимость линков от него до корневого коммутатора S1 является наивысшей среди активных портов.

### Часть3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов

3.1 Определим коммутатор с заблокированным портом.

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab02/S3_ST.png?raw=true)

3.2. Затем уменьшим стоимость root-порта Et1/3 до 18:

```
conf t
interface Ethernet1/3
spanning-tree cost 18
end
```

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab02/S3_ST_cost_change.png?raw=true)

3.3. Отменим изменения стоимости порта

```
conf t
interface Ethernet1/3
no spanning-tree cost 18
end
```

После чего STP возвращается к исходному состоянию.

### 4.Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов

4.1.Активируем избыточные связи на всех коммутаторах

S1:

```
conf t
interface range Et0/2-3
no shutdown
end
```

S2:

```
conf t
interface range Et1/1, Et0/2
no shutdown
end
```

S3:


```
conf t
interface range Et1/1, Et0/3
no shutdown
end
```


Мы можем наблюдать, что ближайшие к корневому коммутатору порты выбраны корневыми, а порты с наибольшей стоимостью линков до корневого коммутатора переведены в состояние "Alt".

S1:

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab02/S1_ST_all_ports.png?raw=true)

S2:

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab02/S2_ST_all_ports.png?raw=true)

S3:

![](https://github.com/IBashlakov/Otus_Network_Engineer_2022/blob/main/lab02/S3_ST_all_ports.png?raw=true)

Какой порт выбран протоколом STP в качестве порта корневого моста на каждом коммутаторе некорневого моста?

В качестве портов корневого моста выбраны порты коммутатора S2: Et0/2, S3 E0/3, как ближайшие к корневому коммутатору.

Почему протокол STP выбрал эти порты в качестве портов корневого моста на этих коммутаторах?

Данные порты являются ближайшими к корневому коммутатору и имеют наименьшую стоимость.

Вопросы для повторения:

Какое значение протокол STP использует первым после выбора корневого моста, чтобы определить выбор порта? Сравниваются значения стоимости портов.
Если первое значение на двух портах одинаково, какое следующее значение будет использовать протокол STP при выборе порта? Сравниваются значения приоритета портов.
Если оба значения на двух портах равны, каким будет следующее значение, которое использует протокол STP при выборе порта? Сравниваются значения номеров портов
Во всех случаях меньшие значения имеют больший приоритет.