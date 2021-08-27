# Лабораторная работа. Настройка IPv6-адресов на сетевых устройствах
## Топология
![](https://github.com/pogodin2004/otusNetwork/blob/main/dz04/images/topology.png)
## Таблица адресации

| Устройство    | Интерфейс          | IP-адрес           | Длина префикса | Шлюз по умолчанию |
| ------------- |:------------------:| ------------------:|:--------------:|:-----------------:|
| R1            | G0/0/0             | 2001:db8:acad: a::1 | 64             | -                 |
|               | G0/0/1             | 2001:db8:acad:1::1 | 64             | -                 |
| S1            | VLAN 1             | 2001:db8:acad:1::b | 64             | -                 |
| PC A          | NIC                | 2001:db8:acad:1::3 | 64             |fe80::2            |
| PC B          | NIC                | 2001:db8:acad: a::3 | 64             |fe80::1            |

## Задачи
### Часть 1. Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора
### Часть 2. Ручная настройка IPv6-адресов
### Часть 3. Проверка сквозного соединения


## Общие сведения/сценарий

   В этой лабораторной работе  вы будете настраивать хосты и интерфейсы устройств с IPv6-адресами.  Для просмотра индивидуальных и групповых IPv6-адресов вы будете использовать команду show. Вы также будете проверять сквозное соединение с помощью команд **ping and traceroute**.

   **Примечание**: Маршрутизаторы, используемые в практических лабораторных работах CCNA, - это Cisco 4221 с Cisco IOS XE Release 16.9.4 (образ universalk9). В лабораторных работах используются коммутаторы Cisco Catalyst 2960 с Cisco IOS версии 15.2(2) (образ lanbasek9). Можно использовать другие маршрутизаторы, коммутаторы и версии Cisco IOS. В зависимости от модели устройства и версии Cisco IOS доступные команды и результаты их выполнения могут отличаться от тех, которые показаны в лабораторных работах. Правильные идентификаторы интерфейса см. в сводной таблице по интерфейсам маршрутизаторов в конце лабораторной работы.

   **Примечание.** Убедитесь, что у всех маршрутизаторов и коммутаторов была удалена начальная конфигурация. Если вы не уверены, обратитесь к инструктору.

   **Примечание**: Шаблон по умолчанию менеджера базы данных 2960 Switch Database Manager (SDM) не поддерживает IPv6. Перед назначением IPv6-адреса SVI VLAN 1 может понадобиться выполнение команды sdm prefer dual-ipv4-and-ipv6 default для включения IPv6-адресации.

    **Примечание**: Шаблон default bias, который по умолчанию используется диспетчером SDM (диспетчер базы данных коммутатора), не предоставляет возможностей адресации IPv6. Убедитесь, что SDM использует шаблон dual-ipv4-and-ipv6 или lanbase-routing. Новый шаблон будет использоваться после перезагрузки. 

```
S1# show sdm prefer
```
```
The current template is "dual-ipv4-and-ipv6 default" template.
 The selected template optimizes the resources in
 the switch to support this level of features for
 0 routed interfaces and 1024 VLANs.

  number of unicast mac addresses:                  4K
  number of IPv4 IGMP groups + multicast routes:    0.25K
  number of IPv4 unicast routes:                    0
  number of IPv6 multicast groups:                  0.375k
  number of directly-connected IPv6 addresses:      0
  number of indirect IPv6 unicast routes:           0
  number of IPv4 policy based routing aces:         0
  number of IPv4/MAC qos aces:                      0.125K
  number of IPv4/MAC security aces:                 0.375K
  number of IPv6 policy based routing aces:         0
  number of IPv6 qos aces:                          0.625k
  number of IPv6 security aces:                     0.125K
```

    Чтобы установить шаблон dual-ipv4-and-ipv6 в качестве шаблона SDM по умолчанию, выполните следующие действия:

```
S1# configure terminal
S1(config)# sdm prefer dual-ipv4-and-ipv6 default
S1(config)# end
S1# reload
```

## Необходимые ресурсы
  * 1 Маршрутизатор (Cisco 4221 с универсальным образом Cisco IOS XE версии 16.9.4 или аналогичным)
  * 1 коммутатор (Cisco 2960 с ПО Cisco IOS версии 15.2(2) с образом lanbasek9 или аналогичная модель)
  * 2 ПК (Windows и программа эмуляции терминала, такая как Tera Term)
  * Консольные кабели для настройки устройств Cisco IOS через консольные порты.
  * Кабели Ethernet, расположенные в соответствии с топологией

   **Примечание**: Интерфейсы Fast Ethernet на коммутаторах Cisco 2960 определяют тип подключения автоматически, поэтому между коммутаторами S1 и S2 можно использовать прямой кабель Ethernet. При использовании коммутатора Cisco другой модели может потребоваться перекрестный кабель Ethernet.

## Инструкции

## Часть 1. Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора

    После подключения сети, инициализации и перезагрузки маршрутизатора и коммутатора выполните следующие действия:

### Шаг 1. Настройте маршрутизатор

    Назначьте имя хоста и настройте основные параметры устройства.

```
Router>enable 
Router#configure terminal
Router(config)#hostname R1
R1(config)#service password-encryption 
R1(config)#enable secret class
R1(config)#end
R1#write memory 
R1#
```

### Шаг 2. Шаг 2. Настройте коммутатор.

    Назначьте имя хоста и настройте основные параметры устройства.

```
Switch>enable 
Switch#configure terminal 
Switch(config)#hostname S1
S1(config)#service password-encryption 
S1(config)#enable secret class
S1(config)#end
S1#write memory 
S1#
```

## Часть 1. Часть 2. Ручная настройка IPv6-адресов

### Шаг 1. Настройте маршрутизатор

   Назначьте имя хоста и настройте основные параметры устройства.

   a. Назначьте глобальные индивидуальные IPv6-адреса, указанные в таблице адресации обоим интерфейсам Ethernet на R1.

```
R1(config)#interface g0/0/0
R1(config-if)#ipv6 address 2001:db8:acad:a::1/64
R1(config-if)#no shutdown 

R1(config)#int g0/0/1
R1(config-if)#ipv6 address 2001:db8:acad:1::1/64
R1(config-if)#no shutdown 
```

   b. Введите команду **show ipv6 interface brief**, чтобы проверить, назначен ли каждому интерфейсу корректный индивидуальный IPv6-адрес.

```
R1#show ipv6 int br
GigabitEthernet0/0/0       [up/up]
    FE80::206:2AFF:FE22:D301
    2001:DB8:ACAD:A::1
GigabitEthernet0/0/1       [up/up]
    FE80::206:2AFF:FE22:D302
    2001:DB8:ACAD:1::1
GigabitEthernet0/0/2       [administratively down/down]
    unassigned
Vlan1                      [administratively down/down]
    unassigned
```

   **Примечание.** Отображаемый локальный адрес канала основан на адресации EUI-64, которая автоматически использует MAC-адрес интерфейса для создания 128-битного локального IPv6-адреса канала.

   c. Чтобы обеспечить соответствие локальных адресов канала индивидуальному адресу, вручную введите локальные адреса канала на каждом интерфейсе Ethernet на R1.

```
R1(config)#int gi0/0/0
R1(config-if)#ipv6 address  fe80::1 link-local 
R1(config-if)#exit
R1(config)#int gi0/0/1
R1(config-if)#ipv6 ad fe80::1 link-local 
```

   **Примечание.** Каждый интерфейс маршрутизатора относится к отдельной сети. Пакеты с локальным адресом канала никогда не выходят за пределы локальной сети, а значит, для обоих интерфейсов можно указывать один и тот же локальный адрес канала.

   d. Используйте выбранную команду, чтобы убедиться, что локальный адрес связи изменен на fe80::1.  

```
R1#show ipv6 int br
GigabitEthernet0/0/0       [up/up]
    FE80::1
    2001:DB8:ACAD:A::1
GigabitEthernet0/0/1       [up/up]
    FE80::1
    2001:DB8:ACAD:1::1
GigabitEthernet0/0/2       [administratively down/down]
    unassigned
Vlan1                      [administratively down/down]
    unassigned
```

   Какие группы многоадресной рассылки назначены интерфейсу G0/0?

```
R1#sh ipv6 int gi0/0/0
GigabitEthernet0/0/0 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::1
  No Virtual link-local address(es):
  Global unicast address(es):
    2001:DB8:ACAD:A::1, subnet is 2001:DB8:ACAD:A::/64
  Joined group address(es):
    FF02::1
    FF02::1:FF00:1
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds

  Интерфейсу Gigabit Ethernet 0/0/0 назначены две групповые рассылки FF02::1 - для всех узлов в локальной сети (канале) 
и FF02::1:FF00:1 - групповой адрес запрашиваемого узла для мультикаст-рассылки.

```


###############################################################################################

### *Шаг 2. Настройте узлы ПК.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz02/images/pc_a_ip.png)

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz02/images/pc_b_ip.png)

### *Шаг 3. Выполните инициализацию и перезагрузку коммутаторов.

```
erase startup-config
...
reload
...
```

### Шаг 4. Настройте базовые параметры каждого коммутатора.

   a. Настройте имена устройств в соответствии с топологией.

```
Switch>enable 
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname S1
S1(config)#
```
```
Switch>enable 
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname S2
S2(config)#
```

   b. Настройте IP-адреса, как указано в таблице адресации.

```
S1(config)#int vla 1
S1(config-if)#ip ad
S1(config-if)#ip address 192.168.1.11 255.255.255.0
S1(config-if)#no shut
```
```
S2(config)#int vla 1
S2(config-if)#ip ad
S2(config-if)#ip address 192.168.1.12 255.255.255.0
S2(config-if)#no shut
```

   c. Назначьте cisco в качестве паролей консоли и VTY.
```
S1#conf t
S1(config)#line con 0
S1(config-line)#pass cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#line vty 0 15
S1(config-line)#pass cisco
S1(config-line)#login
S1(config-line)#exit
```
```
S2#conf t
S2(config)#line con 0
S2(config-line)#pass cisco
S2(config-line)#login
S2(config-line)#exit
S2(config)#line vty 0 15
S2(config-line)#pass cisco
S2(config-line)#login
S2(config-line)#exit
```

   d. Назначьте class в качестве пароля доступа к привилегированному режиму EXEC.
```
S1#conf terminal 
S1(config)#ser pass
S1(config)#enable secret class
```
```
S2#conf terminal 
S2(config)#ser pass
S2(config)#enable secret class
```

## Часть 2. Изучение таблицы МАС-адресов коммутатора

   Как только между сетевыми устройствами начинается передача данных, коммутатор выясняет МАС-адреса и строит таблицу.

### Шаг 1. Запишите МАС-адреса сетевых устройств.

   a. Откройте командную строку на PC-A и PC-B и введите команду ipconfig /all.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz02/images/pc_a_ipconfig.png)

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz02/images/pc_b_ipconfig.png)

   Назовите физические адреса адаптера Ethernet.

   MAC-адрес компьютера PC-A:

```Physical Address................: 0002.4ACE.BB78```

   MAC-адрес компьютера PC-B:

```Physical Address................: 0006.2A11.3604```

   b. Подключитесь к коммутаторам S1 и S2 через консоль и введите команду **show interface F0/1** на каждом 

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz02/images/s1_fa0-1.png)

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz02/images/s2_fa0-1.png)

   Назовите адреса оборудования во второй строке выходных данных команды (или зашитый адрес — bia).

   МАС-адрес коммутатора S1 Fast Ethernet 0/1:

```
Hardware is Lance, address is 0003.e4e6.9e01 (bia 0003.e4e6.9e01)
```

   МАС-адрес коммутатора S2 Fast Ethernet 0/1:

```
Hardware is Lance, address is 0001.c7d2.7501 (bia 0001.c7d2.7501)
```

### Шаг 2. Просмотрите таблицу МАС-адресов коммутатора.

   Подключитесь к коммутатору S2 через консоль и просмотрите таблицу МАС-адресов до и после тестирования сетевой связи с помощью эхо-запросов.

   a. Подключитесь к коммутатору S2 через консоль и войдите в привилегированный режим EXEC.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz02/images/telnet_s2.png)

   b. В привилегированном режиме EXEC введите команду **show mac address-table** и нажмите клавишу ввода.

   *S2# show mac address-table*

```
S2#show mac-address-table 
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----

   1    0003.e4e6.9e01    DYNAMIC     Fa0/1
   1    0006.2a11.3604    DYNAMIC     Fa0/18
S2#
```

   Даже если сетевая коммуникация в сети не происходила (т. е. если команда ping не отправлялась), коммутатор может узнать МАС-адреса при подключении к ПК и другим коммутаторам.

   Записаны ли в таблице МАС-адресов какие-либо МАС-адреса?

```да, записаны два mac-адреса```

   Какие МАС-адреса записаны в таблице? С какими портами коммутатора они сопоставлены и каким устройствам принадлежат? Игнорируйте МАС-адреса, сопоставленные с центральным процессором.

```
0003.e4e6.9e01 на порту Fa0/1 - это коммутатор S1
0006.2a11.3604 на порту Fa0/18 - это PC-B
```

   Если вы не записали МАС-адреса сетевых устройств в шаге 1, как можно определить, каким устройствам принадлежат МАС-адреса, используя только выходные данные команды **show mac address-table**? Работает ли это решение в любой ситуации?

```
можно посмотреть какое устройтво подключено к тому или иному порту или воспользоваться сервисами, 
которые определяют вендора и устройство по mac-адресу
```

### Шаг 3. Очистите таблицу МАС-адресов коммутатора S2 и снова отобразите таблицу МАС-адресов.

   a. В привилегированном режиме EXEC введите команду clear mac address-table dynamic и нажмите клавишу Enter.

   *S2# clear mac address-table dynamic*

   b. Снова быстро введите команду show mac address-table.

```
S2#clear mac-address-table 
S2#show mac-address-table 
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----

   1    0006.2a11.3604    DYNAMIC     Fa0/18
```
```
таблица должна очиститься
```
   Указаны ли в таблице МАС-адресов адреса для VLAN 1? Указаны ли другие МАС-адреса?

```
нет, только адрес подключенного к порту 0/18 PC-b
```

   Через 10 секунд введите команду show mac address-table и нажмите клавишу ввода. Появились ли в таблице МАС-адресов новые адреса?

```
S2#show mac-address-table 
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----

   1    0003.e4e6.9e01    DYNAMIC     Fa0/1
   1    0006.2a11.3604    DYNAMIC     Fa0/18
S2#
```

```
появились все mac-адреса устройств на портах
```

### Шаг 4. С компьютера PC-B отправьте эхо-запросы устройствам в сети и просмотрите таблицу МАС-адресов коммутатора.

   a. На компьютере PC-B откройте командную строку и еще раз введите команду arp -a.

```
C:\>arp -a
No ARP Entries Found
```

   Не считая адресов многоадресной и широковещательной рассылки, сколько пар IP- и МАС-адресов устройств было получено через протокол ARP?

```
ни одного
```

   b. Из командной строки PC-B отправьте эхо-запросы на компьютер PC-A, а также коммутаторы S1 и S2.

```
C:\>ping 192.168.1.11

Pinging 192.168.1.11 with 32 bytes of data:

Request timed out.
Reply from 192.168.1.11: bytes=32 time<1ms TTL=255
Reply from 192.168.1.11: bytes=32 time<1ms TTL=255
Reply from 192.168.1.11: bytes=32 time<1ms TTL=255

Ping statistics for 192.168.1.11:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 192.168.1.1

Pinging 192.168.1.1 with 32 bytes of data:

Reply from 192.168.1.1: bytes=32 time<1ms TTL=128
Reply from 192.168.1.1: bytes=32 time<1ms TTL=128
Reply from 192.168.1.1: bytes=32 time<1ms TTL=128
Reply from 192.168.1.1: bytes=32 time<1ms TTL=128

Ping statistics for 192.168.1.1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>ping 192.168.1.2

Pinging 192.168.1.2 with 32 bytes of data:

Reply from 192.168.1.2: bytes=32 time=17ms TTL=128
Reply from 192.168.1.2: bytes=32 time=12ms TTL=128
Reply from 192.168.1.2: bytes=32 time=12ms TTL=128
Reply from 192.168.1.2: bytes=32 time=12ms TTL=128

Ping statistics for 192.168.1.2:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 12ms, Maximum = 17ms, Average = 13ms

C:\>ping 192.168.1.12

Pinging 192.168.1.12 with 32 bytes of data:

Request timed out.
Reply from 192.168.1.12: bytes=32 time<1ms TTL=255
Reply from 192.168.1.12: bytes=32 time<1ms TTL=255
Reply from 192.168.1.12: bytes=32 time<1ms TTL=255

Ping statistics for 192.168.1.12:
    Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

   От всех ли устройств получены ответы? Если нет, проверьте кабели и IP-конфигурации.

```
ответ получен от всех устройств
```
   c. Подключившись через консоль к коммутатору S2, введите команду show mac address-table.

```
S2#show mac-address-table 
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----

   1    0002.4ace.bb78    DYNAMIC     Fa0/1
   1    0003.e4e6.9e01    DYNAMIC     Fa0/1
   1    0005.5e39.17d2    DYNAMIC     Fa0/1
   1    0006.2a11.3604    DYNAMIC     Fa0/18
```

   Добавил ли коммутатор в таблицу МАС-адресов дополнительные МАС-адреса? Если да, то какие адреса и устройства?

```
добавлены mac-адреса всех устройств в сети
```

   На компьютере PC-B откройте командную строку и еще раз введите команду arp -a.

```
C:\>arp -a
  Internet Address      Physical Address      Type
  192.168.1.1           0002.4ace.bb78        dynamic
  192.168.1.11          0005.5e39.17d2        dynamic
  192.168.1.12          00d0.bad2.0660        dynamic
```

   Появились ли в ARP-кэше компьютера PC-B дополнительные записи для всех сетевых устройств, которым были отправлены эхо-запросы?

```
добавлены mac-адреса PC-A, PC-B и S2
```

### Вопрос для повторения
   В сетях Ethernet данные передаются на устройства по соответствующим МАС-адресам. Для этого коммутаторы и компьютеры динамически создают ARP-кэш и таблицы МАС-адресов. Если компьютеров в сети немного, эта процедура выглядит достаточно простой. Какие сложности могут возникнуть в крупных сетях?
```
В крупных сетях хостам необходимы большие ARP-таблицы для обработки сопоставления IP-адресов и MAC-адресов. 
Это снижает пропускную способность сети и является проблемой безопасности, 
т.к. один компьютер может выдавать себя за другой для перехвата пакетов. Это называется "спуфингом".
```
