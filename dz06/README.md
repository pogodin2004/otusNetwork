# Лабораторная работа - Внедрение маршрутизации между виртуальными локальными сетями
## Топология
![](https://github.com/pogodin2004/otusNetwork/blob/main/dz06/images/topology.png)
## Таблица адресации

| Устройство    | Интерфейс          | IP-адрес      | Маска подсети | Шлюз по умолчанию |
| ------------- |:------------------:|:-------------:|:-------------:|:-----------------:|
| R1            | G0/0/1.10          | 192.168.10.1  | 255.255.255.0 | -                 |
|               | G0/0/1.20          | 192.168.20.1  | 255.255.255.0 | -                 |
|               | G0/0/1.30          | 192.168.30.1  | 255.255.255.0 | -                 |
|               | G0/0/1.100         | -             | -             | -                 |
| S1            | VLAN 10            | 192.168.10.11 | 255.255.255.0 | 192.168.10.1      |
| S2            | VLAN 10            | 192.168.10.12 | 255.255.255.0 | 192.168.10.1      |
| PC A          | NIC                | 192.168.20.3  | 255.255.255.0 | 192.168.20.1      |
| PC B          | NIC                | 192.168.30.3  | 255.255.255.0 | 192.168.30.1      |

## Таблица адресации

| VLAN | Имя         | Назначенный интерфейс         |
| ---- | ----------- | ----------------------------- |
| 10   | Управление  | S1: VLAN 10                   |
|      |             | S2: VLAN 10                   |
| 20   | Sales       | S1: F0/6                      |
| 30   | Operations  | S2: F0/18                     |
| 999  | Parking_Lot | S1: F0/2-4, F0/7-24, G0/1-2   |
|      |             | S2: F0/2-17, F0/19-24, G0/1-2 |



## Задачи
### Часть 1. Создание сети и настройка основных параметров устройства
### Часть 2. Создание сетей VLAN и назначение портов коммутатора
### Часть 3. Настройка транка 802.1Q между коммутаторами.
### Часть 4. Настройка маршрутизации между сетями VLAN
### Часть 5. Проверка, что маршрутизация между VLAN работает

## Общие сведения/сценарий

   В целях повышения производительности сети большие широковещательные домены 2-го уровня делят на домены меньшего размера. Для этого современные коммутаторы используют виртуальные локальные сети (VLAN). VLAN также можно использовать в качестве меры безопасности, отделяя конфиденциальный трафик данных от остальной части сети. Сети VLAN облегчают процесс проектирования сети, обеспечивающей помощь в достижении целей организации. Для связи между VLAN требуется устройство, работающее на уровне 3 модели OSI. Добавление маршрутизации между VLAN позволяет организации разделять и разделять широковещательные домены, одновременно позволяя им обмениваться данными друг с другом.

   Транковые каналы сети VLAN используются для распространения сетей VLAN по различным устройствам. Транковые каналы разрешают передачу трафика из множества сетей VLAN через один канал, не нанося вред идентификации и сегментации сети VLAN. Особый вид маршрутизации между VLAN, называемый «Router-on-a-Stick», использует магистраль от маршрутизатора к коммутатору, чтобы все VLAN могли переходить к маршрутизатору.
   
   В этой лабораторной работе вы создадите VLAN на обоих коммутаторах в топологии, назначите VLAN для коммутации портов доступа, убедитесь, что VLAN работают должным образом, создадите транки VLAN между двумя коммутаторами и между S1 и R1, и настройте маршрутизацию между VLAN на R1 для разрешения связи между хостами в разных VLAN независимо от подсети, в которой находится хост.

   **Примечание:** Маршрутизаторы, используемые в практических лабораторных работах CCNA, - это Cisco 4221 с Cisco IOS XE Release 16.9.4 (образ universalk9). В лабораторных работах используются коммутаторы Cisco Catalyst 2960 с Cisco IOS версии 15.2(2) (образ lanbasek9). Можно использовать другие маршрутизаторы, коммутаторы и версии Cisco IOS. В зависимости от модели устройства и версии Cisco IOS доступные команды и результаты их выполнения могут отличаться от тех, которые показаны в лабораторных работах. Правильные идентификаторы интерфейса см. в сводной таблице по интерфейсам маршрутизаторов в конце лабораторной работы.

   **Примечание:** Убедитесь, что у всех маршрутизаторов и коммутаторов была удалена начальная конфигурация. Если вы не уверены в этом, обратитесь к инструктору.


## Необходимые ресурсы
  * 1 Маршрутизатор (Cisco 4221 с универсальным образом Cisco IOS XE версии 16.9.4 или аналогичным)
  * 2 коммутатора (Cisco 2960 с операционной системой Cisco IOS 15.2(2) (образ lanbasek9) или аналогичная модель)
  * 2 ПК (ОС Windows с программой эмуляции терминалов, такой как Tera Term)
  * Консольные кабели для настройки устройств Cisco IOS через консольные порты.
  * Кабели Ethernet, расположенные в соответствии с топологией

## Инструкции

## Часть 1. Создание сети и настройка основных параметров устройства

   В первой части лабораторной работы вам предстоит создать топологию сети и настроить базовые параметры для узлов ПК и коммутаторов.

### Шаг 1. Создайте сеть согласно топологии.

   Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz06/images/topology.png)

### Шаг 2. Шаг 2. Настройте базовые параметры для маршрутизатора.

   a. Подключитесь к маршрутизатору с помощью консоли и активируйте привилегированный режим EXEC.
 
   b. Войдите в режим конфигурации.

   c. Назначьте маршрутизатору имя устройства.

   d. Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

   e. Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.
 
   f. Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

   g. Установите cisco в качестве пароля виртуального терминала и активируйте вход.

   h. Зашифруйте открытые пароли.

   i. Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

   j. Сохраните текущую конфигурацию в файл загрузочной конфигурации.

   k. Настройте на маршрутизаторе время.

```
Router>enable 
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#hostname R1
R1(config)#no ip domain-lookup 
R1(config)#enable secret class
R1(config)#line co 0
R1(config-line)#pas
R1(config-line)#password cisco
R1(config-line)#login
R1(config-line)#exit
R1(config)#line vty 0 4
R1(config-line)#pass cisco
R1(config-line)#login
R1(config-line)#exit
R1(config)#service password-encryption 
R1(config)#banner motd #Please enter the password!!!#
R1(config)#exit
%SYS-5-CONFIG_I: Configured from console by console
R1#wr mem
Building configuration...
[OK]
R1#clock set 19:30:00 17 september 2021
R1#show clock 
19:30:17.154 UTC Fri Sep 17 2021
R1#
```
### Шаг 3. Настройте базовые параметры каждого коммутатора.

   a. Присвойте коммутатору имя устройства.

   b. Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

   c. Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

   d. Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

   e. Установите cisco в качестве пароля виртуального терминала и активируйте вход.

   f. Зашифруйте открытые пароли.

   g. Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

   h. Настройте на коммутаторах время.

   i. Сохранение текущей конфигурации в качестве начальной.

```
Switch>
Switch>en
Switch>enable 
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname S1
S1(config)#no ip domain-lookup
S1(config)#enable secret class
S1(config)#line con 0
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#line vty 0 4
S1(config-line)#password cisco
S1(config-line)#exit
S1(config)#service password-encryption
S1(config)#banner motd #Please enter password#
S1(config)#exit
S1#
%SYS-5-CONFIG_I: Configured from console by console

S1#wr mem
Building configuration...
[OK]
S1#clock set 17:39:00 17 september 2021
S1#exit
```

```
Switch>enable
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname S2
S2(config)#enable password class
S2(config)#no ip domain-lookup
S2(config)#line con 0
S2(config-line)#password cisco
S2(config-line)#login
S2(config-line)#exit
S2(config)#line vty 0 4
S2(config-line)#password cisco
S2(config-line)#login
S2(config-line)#exit
S2(config)#service password-en
S2(config)#service password-encryption 
S2(config)#banner motd #Please enter the password#
S2(config)#exit
%SYS-5-CONFIG_I: Configured from console by console
S2#clock set 17:43:00 17 september 2021
S2#wr mem
Building configuration...
[OK]
S2#
```

### Шаг 4. Настройте узлы ПК.

   Адреса ПК можно посмотреть в таблице адресации.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz06/images/pc-a.png)
![](https://github.com/pogodin2004/otusNetwork/blob/main/dz06/images/pc-b.png)

## Часть 2. Создание сетей VLAN и назначение портов коммутатора

   Во второй части вы создадите VLAN, как указано в таблице выше, на обоих коммутаторах. Затем вы назначите VLAN соответствующему интерфейсу и проверите настройки конфигурации. Выполните следующие задачи на каждом коммутаторе.

### Шаг 1. Создайте сети VLAN на коммутаторах.

   a. Создайте и назовите необходимые VLAN на каждом коммутаторе из таблицы выше.

```
Please enter password

User Access Verification

Password: 
Password: 

S1>en
S1>enable 
Password: 
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#vlan 10
S1(config-vlan)#name Management
S1(config-vlan)#exit
S1(config)#vlan 20
S1(config-vlan)#name Sales
S1(config-vlan)#exit
S1(config)#vlan 30
S1(config-vlan)#name Operations
S1(config-vlan)#exit
S1(config)#vlan 999
S1(config-vlan)#name Parking_Lot
S1(config-vlan)#exit
S1(config)#
```

```
Please enter the password

User Access Verification

Password: 

S2>en
S2>enable 
Password: 
S2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S2(config)#vlan 10
S2(config-vlan)#name Management
S2(config-vlan)#exit
S2(config)#vlan 20
S2(config-vlan)#name Sales
S2(config-vlan)#exit
S2(config)#vlan 30
S2(config-vlan)#name Operations
S2(config-vlan)#exit
S2(config)#vlan 999
S2(config-vlan)#name Parking_lot
S2(config-vlan)#exit
S2(config)#exit
S2#
%SYS-5-CONFIG_I: Configured from console by console
wr mem
Building configuration...
[OK]
S2#
```

   b. Настройте интерфейс управления и шлюз по умолчанию на каждом коммутаторе, используя информацию об IP-адресе в таблице адресации. 

```
S1#enable 
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#int vlan 10
S1(config-if)#

%LINK-5-CHANGED: Interface Vlan10, changed state to up
S1(config-if)#ip addr 192.168.10.11 255.255.255.0
S1(config-if)#no shut
S1(config-if)#exit
S1(config)#ip default-gateway 192.168.10.1
S1(config)#^Z
S1#
%SYS-5-CONFIG_I: Configured from console by console
wr mem
Building configuration...
[OK]
S1#
```

```
S2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S2(config)#int vl
S2(config)#int vlan 10
S2(config-if)#
%LINK-5-CHANGED: Interface Vlan10, changed state to up

S2(config-if)#ip addr 192.168.10.12 255.255.255.0
S2(config-if)#no shut
S2(config-if)#exit
S2(config)#ip default-gateway 192.168.10.1
S2(config)#^Z
S2#
%SYS-5-CONFIG_I: Configured from console by console
wr mem
Building configuration...
[OK]
S2#
```
   c. Назначьте все неиспользуемые порты коммутатора VLAN Parking_Lot, настройте их для статического режима доступа и административно деактивируйте их.

   **Примечание.** Команда interface range полезна для выполнения этой задачи с минимальным количеством команд.

```
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#int range fa0/2-4, fa0/7-24,gi0/1-2
S1(config-if-range)#switchport mode access 
S1(config-if-range)#sw ac vl 999
S1(config-if-range)#shut

%LINK-5-CHANGED: Interface FastEthernet0/2, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/3, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/4, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/7, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/8, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/9, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/10, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/11, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/12, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/13, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/14, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/15, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/16, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/17, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/18, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/19, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/20, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/21, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/22, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/23, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/24, changed state to administratively down

%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to administratively down

%LINK-5-CHANGED: Interface GigabitEthernet0/2, changed state to administratively down
S1(config-if-range)#exit
S1(config)#exit
S1#
%SYS-5-CONFIG_I: Configured from console by console

S1#wr mem
Building configuration...
[OK]
S1#
```

```
S2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S2(config)#int range fa0/2-17, fa0/19-24,gi0/1-2
S2(config-if-range)#sw mod ac
S2(config-if-range)#sw ac vl 999
S2(config-if-range)#shut

%LINK-5-CHANGED: Interface FastEthernet0/2, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/3, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/4, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/5, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/6, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/7, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/8, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/9, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/10, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/11, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/12, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/13, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/14, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/15, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/16, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/17, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/19, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/20, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/21, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/22, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/23, changed state to administratively down

%LINK-5-CHANGED: Interface FastEthernet0/24, changed state to administratively down

%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to administratively down

%LINK-5-CHANGED: Interface GigabitEthernet0/2, changed state to administratively down
S2(config-if-range)#^Z
S2#
%SYS-5-CONFIG_I: Configured from console by console
wr mem
Building configuration...
[OK]
S2#
```

### Шаг 2. Назначьте сети VLAN соответствующим интерфейсам коммутатора.

   a. Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа.

```
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#int fa 0/6
S1(config-if)#sw mod ac
S1(config-if)#sw acc vl 20
S1(config-if)#^Z
S1#
%SYS-5-CONFIG_I: Configured from console by console
wr mem
Building configuration...
[OK]
S1#
```

```
S2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S2(config)#int  fa 0/18
S2(config-if)#sw mod acc
S2(config-if)#sw acc vl 30
S2(config-if)#^Z
S2#
%SYS-5-CONFIG_I: Configured from console by console
wr mem
Building configuration...
[OK]
S2#
```

   b. Убедитесь, что VLAN назначены на правильные интерфейсы.

```
S1#sh vl

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1, Fa0/5
10   Management                       active    
20   Sales                            active    Fa0/6
30   Operations                       active    
999  Parking_Lot                      active    Fa0/2, Fa0/3, Fa0/4, Fa0/7
                                                Fa0/8, Fa0/9, Fa0/10, Fa0/11
                                                Fa0/12, Fa0/13, Fa0/14, Fa0/15
                                                Fa0/16, Fa0/17, Fa0/18, Fa0/19
                                                Fa0/20, Fa0/21, Fa0/22, Fa0/23
                                                Fa0/24, Gig0/1, Gig0/2
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    
```

```
[OK]
S2#sh vl

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1
10   Management                       active    
20   Sales                            active    
30   Operations                       active    Fa0/18
999  Parking_lot                      active    Fa0/2, Fa0/3, Fa0/4, Fa0/5
                                                Fa0/6, Fa0/7, Fa0/8, Fa0/9
                                                Fa0/10, Fa0/11, Fa0/12, Fa0/13
                                                Fa0/14, Fa0/15, Fa0/16, Fa0/17
                                                Fa0/19, Fa0/20, Fa0/21, Fa0/22
                                                Fa0/23, Fa0/24, Gig0/1, Gig0/2
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    
```

*******************************************************************************************

## Часть 3. Конфигурация магистрального канала стандарта 802.1Q между коммутаторами

   В части 3 вы вручную настроите интерфейс F0/1 как транк.

### Шаг 1. Вручную настройте магистральный интерфейс F0/1 на коммутаторах S1 и S2.

   a. Настройка статического транкинга на интерфейсе F0/1 для обоих коммутаторов.

```
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#int fa 0/1
S1(config-if)#sw mo tr

S1(config-if)#
%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/1, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan10, changed state to up

S1(config-if)#
```

```
S2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S2(config)#int fa 0/1
S2(config-if)#sw mode tr

S2(config-if)#
%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/1, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan10, changed state to up
```

   b. Установите native VLAN 1000 на обоих коммутаторах.

```
S1(config)#vlan 1000
S1(config-vlan)#name new_native
S1(config-if)#sw tr nat vl 1000
S1(config-if)#^Z
%CDP-4-NATIVE_VLAN_MISMATCH: Native VLAN mismatch discovered on FastEthernet0/1 (1000), with S2 FastEthernet0/1
```

```
S2(config)#vlan 1000
S2(config-vlan)#exit
S2(config)#int fa 0/1
S2(config-if)#sw trunk nat vlan 1000
S2(config-if)#%SPANTREE-2-UNBLOCK_CONSIST_PORT: Unblocking FastEthernet0/1 on VLAN1000. Port consistency restored.

%SPANTREE-2-UNBLOCK_CONSIST_PORT: Unblocking FastEthernet0/1 on VLAN0001. Port consistency restored.

exit
S2(config)#
```
   c. Укажите, что VLAN 10, 20, 30 и 1000 могут проходить по транку.

```
S1(config)#int fa 0/1
S1(config-if)#sw tr all vl 10,20,30,1000
S1(config-if)#
```

```
S2(config)#int fa 0/1
S2(config-if)#sw tru allowed vlan 10,20,30,100
S2(config-if)#
```

   d. Проверьте транки, native VLAN и разрешенные VLAN через транк.

```
S1#sh int fa 0/1 sw
Name: Fa0/1
Switchport: Enabled
Administrative Mode: trunk
Operational Mode: trunk
Administrative Trunking Encapsulation: dot1q
Operational Trunking Encapsulation: dot1q
Negotiation of Trunking: On
Access Mode VLAN: 1 (default)
Trunking Native Mode VLAN: 1000 (new_native)
Voice VLAN: none
Administrative private-vlan host-association: none
Administrative private-vlan mapping: none
Administrative private-vlan trunk native VLAN: none
Administrative private-vlan trunk encapsulation: dot1q
Administrative private-vlan trunk normal VLANs: none
Administrative private-vlan trunk private VLANs: none
Operational private-vlan: none
Trunking VLANs Enabled: 10,20,30,1000
Pruning VLANs Enabled: 2-1001
Capture Mode Disabled
Capture VLANs Allowed: ALL
Protected: false
Unknown unicast blocked: disabled
Unknown multicast blocked: disabled
Appliance trust: none
```

```
S2#sh int fa 0/1 sw
Name: Fa0/1
Switchport: Enabled
Administrative Mode: trunk
Operational Mode: trunk
Administrative Trunking Encapsulation: dot1q
Operational Trunking Encapsulation: dot1q
Negotiation of Trunking: Off
Access Mode VLAN: 30 (Operations)
Trunking Native Mode VLAN: 1000 (VLAN1000)
Voice VLAN: none
Administrative private-vlan host-association: none
Administrative private-vlan mapping: none
Administrative private-vlan trunk native VLAN: none
Administrative private-vlan trunk encapsulation: dot1q
Administrative private-vlan trunk normal VLANs: none
Administrative private-vlan trunk private VLANs: none
Operational private-vlan: none
Trunking VLANs Enabled: 10,20,30,100
Pruning VLANs Enabled: 2-1001
Capture Mode Disabled
Capture VLANs Allowed: ALL
Protected: false
Unknown unicast blocked: disabled
Unknown multicast blocked: disabled
Appliance trust: none

```

### Шаг 2. Вручную настройте магистральный интерфейс F0/5 на коммутаторе S1.

   a. Настройте интерфейс S1 F0/5 с теми же параметрами транка, что и F0/1. Это транк до маршрутизатора.

```
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#int fa 0/5
S1(config-if)#sw tru nat vl 1000
S1(config-if)#sw tr all vl 10,20,30,1000
S1(config-if)#^Z
S1#
%SYS-5-CONFIG_I: Configured from console by console
```

   b. Сохраните текущую конфигурацию в файл загрузочной конфигурации.

```
S1#copy running-config startup-config 
Destination filename [startup-config]? y
Building configuration...
[OK]
S1#
```

   c. Проверка транкинга.

```
S1#sh int tru
Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      1000

Port        Vlans allowed on trunk
Fa0/1       10,20,30,1000

Port        Vlans allowed and active in management domain
Fa0/1       10,20,30,1000

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       10,20,30,1000

```

   **Вопрос:** Что произойдет, если G0/0/1 на R1 будет отключен?

```
Интерфейс fa 0/5 на S1 будет в состоянии down. Шлюз по умолчанию для всех устройств будет также недоступен.
```

## Часть 4. Настройка маршрутизации между сетями VLAN

### Шаг 1. Настройте маршрутизатор.

   a. При необходимости активируйте интерфейс G0/0/1 на маршрутизаторе.

```
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#int gi 0/1
R1(config-if)#no shut

R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up
```

   b. Настройте подинтерфейсы для каждой VLAN, как указано в таблице IP-адресации. Все подинтерфейсы используют инкапсуляцию 802.1Q. Убедитесь, что подинтерфейсу для native VLAN не назначен IP-адрес. Включите описание для каждого подинтерфейса.

```
R1(config)#int gi 0/1.10
R1(config-subif)#
%LINK-5-CHANGED: Interface GigabitEthernet0/1.10, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1.10, changed state to up

R1(config-subif)#des Management
R1(config-subif)#enc dot 10
R1(config-subif)#ip addr 192.168.10.1 255.255.255.0
R1(config-subif)#exit
R1(config)#int gi 0/1.20
R1(config-subif)#
%LINK-5-CHANGED: Interface GigabitEthernet0/1.20, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1.20, changed state to up

R1(config-subif)#des Sales
R1(config-subif)#enc dot 20
R1(config-subif)#ip addr 192.168.20.1 255.255.255.0
R1(config-subif)#exit
R1(config)#int gi 0/1.30
R1(config-subif)#
%LINK-5-CHANGED: Interface GigabitEthernet0/1.30, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1.30, changed state to up

R1(config-subif)#des Operations
R1(config-subif)#enc dot 30
R1(config-subif)#ip addr 192.168.30.1 255.255.255.0
R1(config-subif)#exit
R1(config)#int gi 0/1.1000
R1(config-subif)#
%LINK-5-CHANGED: Interface GigabitEthernet0/1.1000, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1.1000, changed state to up

R1(config-subif)#des new_native
R1(config-subif)#enc dot 1000
R1(config-subif)#exit
```

   c. Убедитесь, что вспомогательные интерфейсы работают

```
R1#sh ip int br
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0     unassigned      YES unset  administratively down down 
GigabitEthernet0/1     unassigned      YES unset  up                    up 
GigabitEthernet0/1.10  192.168.10.1    YES manual up                    up 
GigabitEthernet0/1.20  192.168.20.1    YES manual up                    up 
GigabitEthernet0/1.30  192.168.30.1    YES manual up                    up 
GigabitEthernet0/1.1000unassigned      YES unset  up                    up 
GigabitEthernet0/2     unassigned      YES unset  administratively down down 
Vlan1                  unassigned      YES unset  administratively down down
R1#
```

## Часть 5. Проверьте, работает ли маршрутизация между VLAN

### Шаг 1. Выполните следующие тесты с PC-A. Все должно быть успешно.

   a. Отправьте эхо-запрос с PC-A на шлюз по умолчанию.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz06/images/pc-a-gateway.png)

   b. Отправьте эхо-запрос с PC-A на PC-B.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz06/images/pc-a-pc-b.png)

   c. Отправьте команду ping с компьютера PC-A на коммутатор S2.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz06/images/pc-a-s-2.png)

### Шаг 2. Пройдите следующий тест с PC-B

   В окне командной строки на PC-B выполните команду tracert на адрес PC-A.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz06/images/pc-b-tracert.png)

   Вопрос: Какие промежуточные IP-адреса отображаются в результатах?

```
192.168.30.1 - адрес шлюза по умолчанию

192.168.20.3 - адрес PC-A

```



