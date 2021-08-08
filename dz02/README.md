# Лабораторная работа. Просмотр таблицы MAC-адресов коммутатора
## Топология
![](https://github.com/pogodin2004/otusNetwork/blob/main/dz02/images/topology.png)
## Таблица адресации

| Устройство    | Интерфейс          | IP-адрес           | Маска подсети |
| ------------- |:------------------:| ------------------:|:-------------:|
| S1            | VLAN 1             | 192.168.1.11       | 255.255.255.0 |
| S2            | VLAN 1             | 192.168.1.12       | 255.255.255.0 |
| PC A          | NIC                | 192.168.1.1        | 255.255.255.0 |
| PC B          | NIC                | 192.168.1.2        | 255.255.255.0 |

## Цели
### Часть 1. Создание и настройка сети
### Часть 2. Изучение таблицы MAC-адресов коммутатора

## Общие сведения/сценарий

   Коммутатор локальной сети на уровне 2 предназначен для доставки кадров Ethernet всем узловым устройствам в локальной сети (LAN). Он записывает МАС-адреса узлов, отображаемые в сети, и сопоставляет их с собственными портами коммутатора Ethernet. Этот процесс называется созданием таблицы МАС-адресов. Получив кадр от ПК, коммутатор изучает МАС-адреса источника и назначения кадра. MAC-адрес источника регистрируется и сопоставляется с портом коммутатора, от которого он был получен. Затем по таблице MAC-адресов определяется МАС-адрес назначения. Если MAC-адрес назначения известен, кадр пересылается через соответствующий порт коммутатора, связанный с этим MAC-адресом. Если MAC-адрес неизвестен, то кадр отправляется по широковещательной рассылке через все порты коммутатора, кроме того, через который он был получен. Важно видеть и понимать работу коммутатора и то, как он осуществляет передачу данных по сети. Понимание функционала коммутатора особенно важно для сетевых администраторов, задача которых заключается в обеспечении безопасной и стабильной работы сети.

   Коммутаторы используются для соединения компьютеров в локальных сетях (LAN) и передачи данных между ними. Коммутаторы отправляют кадры Ethernet на узловые устройства, которые идентифицируются по МАС-адресам сетевых плат.
В части 1 вам нужно построить топологию, состоящую из двух коммутаторов, соединенных транком. В части 2 вам предстоит отправить эхо-запросы различным устройствам и посмотреть, как два коммутатора строят свои таблицы МАС-адресов.

   **Примечание.** В лабораторной работе используются коммутаторы Cisco Catalyst 2960s с операционной системой Cisco IOS 15.2(2) (образ lanbasek9). Допускается использование других моделей коммутаторов и других версий Cisco IOS. В зависимости от модели устройства и версии Cisco IOS доступные команды и результаты их выполнения могут отличаться от тех, которые показаны в лабораторных работах.

   **Примечание**: Убедитесь, что все настройки коммутатора удалены и загрузочная конфигурация отсутствует. Если вы не уверены в этом, обратитесь к инструктору.


## Необходимые ресурсы
  * 2 коммутатора (Cisco 2960 с операционной системой Cisco IOS 15.2(2) (образ lanbasek9) или аналогичная модель)
  * 2 ПК (Windows и программа эмуляции терминала, такая как Tera Term)
  * Консольные кабели для настройки устройств Cisco IOS через консольные порты.
  * Кабели Ethernet, расположенные в соответствии с топологией

   **Примечание.** Интерфейсы Fast Ethernet на коммутаторах Cisco 2960 определяют тип подключения автоматически, поэтому между коммутаторами S1 и S2 можно использовать прямой кабель Ethernet. При использовании коммутатора Cisco другой модели может потребоваться перекрестный кабель Ethernet.


## Часть 1. Создание сети и проверка настроек коммутатора по умолчанию
В первой части лабораторной работы вам предстоит настроить топологию сети и проверить настройку коммутатора по умолчанию.


## Инструкции

## Часть 1. Создание и настройка сети

### *Шаг 1. Подключите сеть в соответствии с топологией.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz02/images/cpt_topology.png)

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

### *Шаг 4. Настройте базовые параметры каждого коммутатора.

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

### *Шаг 1. Запишите МАС-адреса сетевых устройств.

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

### *Шаг 2. Просмотрите таблицу МАС-адресов коммутатора.

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

### *Шаг 3. Очистите таблицу МАС-адресов коммутатора S2 и снова отобразите таблицу МАС-адресов.

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

### *Шаг 4. С компьютера PC-B отправьте эхо-запросы устройствам в сети и просмотрите таблицу МАС-адресов коммутатора.

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
Это снижает пропускную способность сети и является проблемой безопасности, т.к. один компьютер может выдавать себя за другой для перехвата пакетов. Это называется "спуфингом".
```