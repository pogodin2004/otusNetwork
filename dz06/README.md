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
| 999  | Parking_Lot | С1: F0/2-4, F0/7-24, G0/1-2   |
| 999  | Parking_Lot | С2: F0/2-17, F0/19-24, G0/1-2 |



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

   b. Убедитесь, что VLAN назначены на правильные интерфейсы.


### Шаг 3. Создайте имя пользователя в локальной базе учетных записей.

```
R1(config)#username admin password Adm1nP@55
R1(config)#
```
### Шаг 4. Активируйте протокол SSH на линиях VTY.

   a. Активируйте протоколы Telnet и SSH на входящих линиях VTY с помощью команды transport input.

```
R1(config-line)#transport input telnet
R1(config-line)#transport input ssh
```

   b. Измените способ входа в систему таким образом, чтобы использовалась проверка пользователей по локальной базе учетных записей.

```
R1(config-line)#login local
R1(config-line)#
```

### Шаг 5. Сохраните текущую конфигурацию в файл загрузочной конфигурации.

```
R1#copy running-config startup-config 
Destination filename [startup-config]? 
Building configuration...
[OK]
R1#
```

### Шаг 6. Установите соединение с маршрутизатором по протоколу SSH.

   a. Запустите Tera Term с PC-A.
   b. Установите SSH-подключение к R1. Use the username admin and password Adm1nP@55. У вас должно получиться установить SSH-подключение к R1.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz05/images/pc_ssh_to_R1.png)

## Часть 3. Настройка коммутатора для доступа по протоколу SSH

   В части 3 вам предстоит настроить коммутатор для приема подключений по протоколу SSH, а затем установить SSH-подключение с помощью программы Tera Term.

### Шаг 1. Настройте основные параметры коммутатора.

   a. Подключитесь к коммутатору с помощью консольного подключения и активируйте привилегированный режим EXEC.

   b. Войдите в режим конфигурации.

   c. Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

   d. Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

   e. Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

   f. Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

   g. Зашифруйте открытые пароли.

   h. Создайте баннер, который предупреждает о запрете несанкционированного доступа.

   i. Настройте и активируйте на коммутаторе интерфейс VLAN 1, используя информацию, приведенную в таблице адресации.

   j. Сохраните текущую конфигурацию в файл загрузочной конфигурации.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz05/images/sw_access.png)

### Шаг 2. Настройте коммутатор для соединения по протоколу SSH.

   Для настройки протокола SSH на коммутаторе используйте те же команды, которые применялись для аналогичной настройки маршрутизатора в части 2.

   a. Настройте имя устройства, как указано в таблице адресации.

   b. Задайте домен для устройства.

   c. Создайте ключ шифрования с указанием его длины.

   d. Создайте имя пользователя в локальной базе учетных записей.

   e. Активируйте протоколы Telnet и SSH на линиях VTY.

   f. Измените способ входа в систему таким образом, чтобы использовалась проверка пользователей по локальной базе учетных записей.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz05/images/sw_ssh.png)

### Шаг 3. Установите соединение с коммутатором по протоколу SSH.

   Запустите программу Tera Term на PC-A, затем установите подключение по протоколу SSH к интерфейсу SVI коммутатора S1.

   Вопрос:
   Удалось ли вам установить SSH-соединение с коммутатором?

```
Да
```

## Часть 4. Настройка протокола SSH с использованием интерфейса командной строки (CLI) коммутатора

   Клиент SSH встроен в операционную систему Cisco IOS и может запускаться из интерфейса командной строки. В части 4 вам предстоит установить соединение с маршрутизатором по протоколу SSH, используя интерфейс командной строки коммутатора.

### Шаг 1. Посмотрите доступные параметры для клиента SSH в Cisco IOS.

Используйте вопросительный знак (?), чтобы отобразить варианты параметров для команды ssh.

```
S1#ssh ?
  -l  Log in using this user name
  -v  Specify SSH Protocol Version
S1#ssh 
```

### Шаг 2. Установите с коммутатора S1 соединение с маршрутизатором R1 по протоколу SSH.

   a. Чтобы подключиться к маршрутизатору R1 по протоколу SSH, введите команду –l admin. Это позволит вам войти в систему под именем **admin.** При появлении приглашения введите в качестве пароля **Adm1nP@55**

```
S1#ssh -l admin 192.168.1.1

Password: 

unauthorized access prohibited

R1>
```

   b. Чтобы вернуться к коммутатору S1, не закрывая сеанс SSH с маршрутизатором R1, нажмите комбинацию клавиш Ctrl+Shift+6. Отпустите клавиши Ctrl+Shift+6 и нажмите x. Отображается приглашение привилегированного режима EXEC коммутатора.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz05/images/manipulations.png)

   c. Чтобы вернуться к сеансу SSH на R1, нажмите клавишу Enter в пустой строке интерфейса командной строки. Чтобы увидеть окно командной строки маршрутизатора, нажмите клавишу Enter еще раз.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz05/images/manipulations1.png)

   d. Чтобы завершить сеанс SSH на маршрутизаторе R1, введите в командной строке маршрутизатора команду exit.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz05/images/manipulations2.png)


   Какие версии протокола SSH поддерживаются при использовании интерфейса командной строки?

```
S1#sh ip ssh
SSH Enabled - version 1.99
Authentication timeout: 120 secs; Authentication retries: 3
```

## Вопрос для повторения

   Как предоставить доступ к сетевому устройству нескольким пользователям, у каждого из которых есть собственное имя пользователя?

```
   Если пользователи уже созданы, то подключение осущесталяется от его имени
   ssh -l <user> <ip-address>.
   Если пользователь не создан, то вводим команду
   username <username> secret <password>
   потом в line vty ... вводим login local
   и подключение осуществляем от имени выбранного пользователя.
```

