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
```
```
   Интерфейсу Gigabit Ethernet 0/0/0 назначены две групповые рассылки FF02::1 - для всех узлов в локальной сети (канале) 
   и FF02::1:FF00:1 - групповой адрес запрашиваемого узла для мультикаст-рассылки.
```


