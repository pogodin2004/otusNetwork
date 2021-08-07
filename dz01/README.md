# Лабораторная работа. Базовая настройка коммутатора 
## Топология
![](https://github.com/pogodin2004/otusNetwork/blob/main/dz01/topology.png)
## Таблица адресации

| Устройство    | Интерфейс          | IP-адрес / префикс |
| ------------- |:------------------:| ------------------:|
| S1            | VLAN 1             |     192.168.1.2/24 |
| PC A          | NIC                |    192.168.1.10/24 |

## Задачи
### Часть 1. Проверка конфигурации коммутатора по умолчанию
### Часть 2. Создание сети и настройка основных параметров устройства
  * Настройте базовые параметры коммутатора.
  * Настройте IP-адрес для ПК.
### Часть 3. Проверка сетевых подключений
  * Отобразите конфигурацию устройства.
  * Протестируйте сквозное соединение, отправив эхо-запрос.
  * Протестируйте возможности удаленного управления с помощью Telnet.

## Общие сведения/сценарий
На коммутаторах Cisco можно настроить особый IP-адрес, который называют виртуальным интерфейсом коммутатора (SVI). SVI или адрес управления можно использовать для удаленного доступа к коммутатору в целях отображения или настройки параметров. Если для SVI сети VLAN 1 назначен IP-адрес, то по умолчанию все порты в сети VLAN 1 имеют доступ к IP-адресу управления SVI. 

   В ходе данной лабораторной работы вам предстоит построить простую топологию, используя Ethernet-кабель локальной сети, и получить доступ к коммутатору Cisco, используя консольное подключение и методы удаленного доступа. Перед настройкой базовых параметров коммутатора нужно проверить настройки коммутатора по умолчанию. В число таких основных параметров коммутации входят имя устройства, описание интерфейса, локальные пароли, объявление дня (MOTD), IP-адрес и статический MAC-адрес. Необходимо также показать использование IP-адреса управления для удаленного управления коммутатором. Топология включает один коммутатор и один узел с использованием только портов Ethernet и консольных портов.

   **Примечание.** В лабораторной работе используются коммутаторы Cisco Catalyst 2960s с операционной системой Cisco IOS 15.2(2) (образ lanbasek9). Допускается использование других моделей коммутаторов и других версий Cisco IOS. В зависимости от модели устройства и версии Cisco IOS доступные команды и результаты их выполнения могут отличаться от тех, которые показаны в лабораторных работах. 

   **Примечание:** Убедитесь, что все настройки коммутатора удалены и загрузочная конфигурация отсутствует. Если вы не уверены, обратитесь к инструктору. Процедура инициализации и перезагрузки коммутатора описана в приложении А.

## Необходимые ресурсы
  * 1 коммутатор (Cisco 2960 с ПО Cisco IOS версии 15.2(2) с образом lanbasek9 или аналогичная модель)
  * 1 ПК (под управлением Windows с программой эмуляции терминала, например, Tera Term)
  * 1 консольный кабель для настройки устройства на базе Cisco IOS через консольный порт.
  * 1 кабель Ethernet, как показано в топологии.

## Часть 1. Создание сети и проверка настроек коммутатора по умолчанию
В первой части лабораторной работы вам предстоит настроить топологию сети и проверить настройку коммутатора по умолчанию.

#### Шаг 1. Создайте сеть согласно топологии.

   a. Подсоедините консольный кабель, как показано в топологии. На данном этапе не подключайте кабель Ethernet компьютера PC-A.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz01/console_connection.png)

   Примечание. При использовании Netlab отключите интерфейс F0/6 на коммутаторе S1. Это имеет такой же эффект, как отсоединение компьютера PC-A от коммутатора S1.\

   b. Установите консольное подключение к коммутатору с компьютера PC-A с помощью Tera Term или другой программы эмуляции терминала.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz01/terminal.png)

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz01/terminal_connection.png)

   Вопрос: Почему нужно использовать консольное подключение для первоначальной настройки коммутатора? Почему нельзя подключиться к коммутатору через Telnet или SSH?

```У коммутатора еще нет ip-адреса```

#### Шаг 2. Проверьте настройки коммутатора по умолчанию.

   На данном этапе вам нужно проверить такие параметры коммутатора по умолчанию, как текущие настройки коммутатора, данные IOS, свойства интерфейса, сведения о VLAN и флеш-память.

   Все команды IOS коммутатора можно выполнять из привилегированного режима. Доступ к привилегированному режиму нужно ограничить с помощью пароля, чтобы предотвратить неавторизованное использование устройства — через этот режим можно получить прямой доступ к режиму глобальной конфигурации и командам, используемым для настройки рабочих параметров. Пароли можно будет настроить чуть позже.

   К привилегированному набору команд относятся команды пользовательского режима, а также команда **configure**, при помощи которой выполняется доступ к остальным командным режимам. Введите команду **enable**, чтобы войти в привилегированный режим EXEC.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz01/enable_command.png)

   a. Предположим, что коммутатор не имеет файла конфигурации, сохраненного в энергонезависимой памяти (NVRAM). Консольное подключение к коммутатору с помощью Tera Term или другой программы эмуляции терминала предоставит доступ к командной строке пользовательского режима EXEC в виде Switch>. Введите команду **enable**, чтобы войти в привилегированный режим EXEC.

   Обратите внимание, что измененная в конфигурации строка будет отражать привилегированный режим EXEC.

   Убедитесь, что на коммутаторе находится пустой файл конфигурации по умолчанию, с помощью команды **show running-config** привилегированного режима EXEC. Если конфигурационный файл был предварительно сохранен, его нужно удалить. В зависимости от модели коммутатора и версии IOS ваша конфигурация может слегка отличаться. Тем не менее, настроенных паролей или IP-адресов в конфигурации быть не должно. Выполните очистку настроек и перезагрузите коммутатор, если ваш коммутатор имеет настройки, отличные от настроек по умолчанию.

   b. Изучите текущий файл running configuration.
```Switch#show running-config 
Building configuration...

Current configuration : 1080 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
no service password-encryption
!
hostname Switch
!
!
!
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!
interface FastEthernet0/1
!
interface FastEthernet0/2
!
interface FastEthernet0/3
!
interface FastEthernet0/4
!
interface FastEthernet0/5
!
interface FastEthernet0/6
!
interface FastEthernet0/7
!
interface FastEthernet0/8
!
interface FastEthernet0/9
!
interface FastEthernet0/10
!
interface FastEthernet0/11
!
interface FastEthernet0/12
!
interface FastEthernet0/13
!
interface FastEthernet0/14
!
interface FastEthernet0/15
!
interface FastEthernet0/16
!
interface FastEthernet0/17
!
interface FastEthernet0/18
!
interface FastEthernet0/19
!
interface FastEthernet0/20
!
interface FastEthernet0/21
!
interface FastEthernet0/22
!
interface FastEthernet0/23
!
interface FastEthernet0/24
!
interface GigabitEthernet0/1
!
interface GigabitEthernet0/2
!
interface Vlan1
 no ip address
 shutdown
!
!
!
!
line con 0
!
line vty 0 4
 login
line vty 5 15
 login
!
!
!
!
end


Switch# 
```

  Вопросы:

  * Сколько интерфейсов FastEthernet имеется на коммутаторе 2960?

   ```24 интерфейса FastEthernet```

  * Сколько интерфейсов Gigabit Ethernet имеется на коммутаторе 2960?

   ```2 интерфейса GigabitEthernet```

  * Каков диапазон значений, отображаемых в vty-линиях?

   ```от 0 да 4 и от 5 до 15```  


   c.Изучите файл загрузочной конфигурации (startup configuration), который содержится в энергонезависимом ОЗУ (NVRAM). 

```Switch# show startup-config 
startup-config is not present
Switch#
```
   Почему появляется это сообщение?
   ```Заводская конфигурация```

   d. Изучите характеристики SVI для VLAN 1.
```Switch#show ip interface vlan 1
Vlan1 is administratively down, line protocol is down
  Internet protocol processing disabled

Switch#
```

```
Vlan1 is administratively down, line protocol is down
  Hardware is CPU Interface, address is 00d0.bc1e.46dc (bia 00d0.bc1e.46dc)
  MTU 1500 bytes, BW 100000 Kbit, DLY 1000000 usec,
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation ARPA, loopback not set
  ARP type: ARPA, ARP Timeout 04:00:00
  Last input 21:40:21, output never, output hang never
  Last clearing of "show interface" counters never
  Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
  Queueing strategy: fifo
  Output queue: 0/40 (size/max)
  5 minute input rate 0 bits/sec, 0 packets/sec
  5 minute output rate 0 bits/sec, 0 packets/sec
     1682 packets input, 530955 bytes, 0 no buffer
     Received 0 broadcasts (0 IP multicast)
     0 runts, 0 giants, 0 throttles
     0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored
     563859 packets output, 0 bytes, 0 underruns
     0 output errors, 23 interface resets
     0 output buffer failures, 0 output buffers swapped out
```
   Назначен ли IP-адрес сети VLAN 1?
   ```нет```

   Какой MAC-адрес имеет SVI? Возможны различные варианты ответов.
```00d0.bc1e.46dc```

   Данный интерфейс включен?
```Vlan1 is administratively down, line protocol is down```

   e. Изучите IP-свойства интерфейса SVI сети VLAN 1.

   Какие выходные данные вы видите?

f.	Подсоедините кабель Ethernet компьютера PC-A к порту 6 на коммутаторе и изучите IP-свойства интерфейса SVI сети VLAN 1. Дождитесь согласования параметров скорости и дуплекса между коммутатором и ПК.
Примечание. При использовании Netlab включите интерфейс F0/6 на коммутаторе S1.
```
Switch# show ip int br
Interface              IP-Address      OK? Method Status                Protocol 
FastEthernet0/1        unassigned      YES manual down                  down 
FastEthernet0/2        unassigned      YES manual down                  down 
FastEthernet0/3        unassigned      YES manual down                  down 
FastEthernet0/4        unassigned      YES manual down                  down 
FastEthernet0/5        unassigned      YES manual down                  down 
FastEthernet0/6        unassigned      YES manual down                  down 
FastEthernet0/7        unassigned      YES manual down                  down 
FastEthernet0/8        unassigned      YES manual down                  down 
FastEthernet0/9        unassigned      YES manual down                  down 
FastEthernet0/10       unassigned      YES manual down                  down 
FastEthernet0/11       unassigned      YES manual down                  down 
FastEthernet0/12       unassigned      YES manual down                  down 
FastEthernet0/13       unassigned      YES manual down                  down 
FastEthernet0/14       unassigned      YES manual down                  down 
FastEthernet0/15       unassigned      YES manual down                  down 
FastEthernet0/16       unassigned      YES manual down                  down 
FastEthernet0/17       unassigned      YES manual down                  down 
FastEthernet0/18       unassigned      YES manual down                  down 
FastEthernet0/19       unassigned      YES manual down                  down 
FastEthernet0/20       unassigned      YES manual down                  down 
FastEthernet0/21       unassigned      YES manual down                  down 
FastEthernet0/22       unassigned      YES manual down                  down 
FastEthernet0/23       unassigned      YES manual down                  down 
FastEthernet0/24       unassigned      YES manual down                  down 
GigabitEthernet0/1     unassigned      YES manual down                  down 
GigabitEthernet0/2     unassigned      YES manual down                  down 
Vlan1                  unassigned      YES manual administratively down down
```






