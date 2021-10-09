# Лабораторная работа - Конфигурация безопасности коммутатора
## Топология
![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/topology.png)
## Таблица адресации
| Устройство | Интерфейс   | IP-адрес       | Маска подсети |
| ---------- |:-----------:|:--------------:|:-------------:|
| R1         | G0/0/1      | 192.168.10.1   | 255.255.255.0 |
|            | Loopback 0  | 10.10.1.1      | 255.255.255.0 |
| S1         | VLAN 200    | 192.168.10.201 | 255.255.255.0 |
| S2         | VLAN 1      | 192.168.10.202 | 255.255.255.0 |
| PC-A       | NIC         | DHCP           | 255.255.255.0 |
| PC-B       | NIC         | DHCP           | 255.255.255.0 |

## Цели
### Часть 1. Настройка основного сетевого устройства

   * Создайте сеть.

   * Настройте маршрутизатор R1.

   * Настройка и проверка основных параметров коммутатора

### Часть 2. Настройка сетей VLAN

   * Сконфигруриуйте VLAN 10.

   * Сконфигруриуйте SVI для VLAN 10.

   * Настройте VLAN 333 с именем Native на S1 и S2.

   * Настройте VLAN 999 с именем ParkingLot на S1 и S2.

### Часть 3. Настройки безопасности коммутатора.

   * Реализация магистральных соединений 802.1Q.

   * Настройка портов доступа

   * Безопасность неиспользуемых портов коммутатора

   * Документирование и реализация функций безопасности порта.

   * Реализовать безопасность DHCP snooping .

   * Реализация PortFast и BPDU Guard

   * Проверка сквозной связанности.

## Общие сведения/сценарий

   Это комплексная лабораторная работа, нацеленная на повторение ранее изученных функций безопасности уровня 2.

   **Примечание:** Маршрутизаторы, используемые в практических лабораторных работах CCNA, - это Cisco 4221 с Cisco IOS XE Release 16.9.3 (образ universalk9). В лабораторных работах используются коммутаторы Cisco Catalyst 2960 с Cisco IOS версии 15.0(2) (образ lanbasek9). Можно использовать другие маршрутизаторы, коммутаторы и версии Cisco IOS. В зависимости от модели устройства и версии Cisco IOS доступные команды и результаты их выполнения могут отличаться от тех, которые показаны в лабораторных работах. Правильные идентификаторы интерфейса см. в сводной таблице по интерфейсам маршрутизаторов в конце лабораторной работы.

   **Примечание:** Убедитесь, что все настройки коммутатора удалены и загрузочная конфигурация отсутствует. Если вы не уверены, обратитесь к инструктору.

## Необходимые ресурсы
   * 2 маршрутизатора (Cisco 4221 с универсальным образом Cisco IOS XE версии 16.9.4 или аналогичным)
   * 2 коммутатора (Cisco 2960 с операционной системой Cisco IOS 15.2(2) (образ lanbasek9) или аналогичная модель)
   * 2 ПК (ОС Windows с программой эмуляции терминалов, такой как Tera Term)
   * Консольные кабели для настройки устройств Cisco IOS через консольные порты.
   * Кабели Ethernet, расположенные в соответствии с топологией

## Инфтрукции

## Часть 1. Настройка основного сетевого устройства
   
### Шаг 1. Создайте сеть.

   a. Создайте сеть согласно топологии.

   b. Инициализация устройств

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/my_topology.png)

### Шаг 2. Настройте маршрутизатор R1.

   a. Загрузите следующий конфигурационный скрипт на R1.

```
enable
configure terminal
hostname R1
no ip domain lookup
ip dhcp excluded-address 192.168.10.1 192.168.10.9
ip dhcp excluded-address 192.168.10.201 192.168.10.202
!
ip dhcp pool Students
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 domain-name CCNA2.Lab-11.6.1
!
interface Loopback0
 ip address 10.10.1.1 255.255.255.0
!
interface GigabitEthernet0/0/1
 description Link to S1
 ip dhcp relay information trusted
 ip address 192.168.10.1 255.255.255.0
 no shutdown
!
line con 0
 logging synchronous
 exec-timeout 0 0
```

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/r1_first_config.png)

   b. Проверьте текущую конфигурацию на R1, используя следующую команду:

```
R1# show ip interface brief
```

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/r1_int_br.png)

   c. Убедитесь, что IP-адресация и интерфейсы находятся в состоянии up / up (при необходимости устраните неполадки).

### Шаг 3. Настройка и проверка основных параметров коммутатора

   a. Настройте имя хоста для коммутаторов S1 и S2.

   b. Запретите нежелательный поиск в DNS.

   c. Настройте описания интерфейса для портов, которые используются в S1 и S2.

   d. Установите для шлюза по умолчанию для VLAN управления значение 192.168.10.1 на обоих коммутаторах.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/s1_first_config.png)


![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/s2_first_config.png)


## Часть 2. Настройка сетей VLAN на коммутаторах.

### Шаг 1. Сконфигруриуйте VLAN 10.

Добавьте VLAN 10 на S1 и S2 и назовите VLAN - Management.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/s1_vlan_10.png)


![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/s2_vlan_10.png)
   

### Шаг 2. Сконфигруриуйте SVI для VLAN 10.

   Настройте IP-адрес в соответствии с таблицей адресации для SVI для VLAN 10 на S1 и S2. Включите интерфейсы SVI и предоставьте описание для интерфейса.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/s1_int_vlan_10.png)

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/s2_int_vlan_10.png)

### Шаг 3. Настройте VLAN 333 с именем Native на S1 и S2.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/s1_vlan_333.png)

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/s2_vlan_333.png)

### Шаг 4. Настройте VLAN 999 с именем ParkingLot на S1 и S2.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/s1_vlan_999.png)

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/s2_vlan_999.png)

## Часть 3. Настройки безопасности коммутатора.

### Шаг 1. Релизация магистральных соединений 802.1Q.

   a. Настройте все магистральные порты Fa0/1 на обоих коммутаторах для использования VLAN 333 в качестве native VLAN.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/s1_fa0-1_trunk.png)

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/s2_fa0-1_trunk.png)

   b. Убедитесь, что режим транкинга успешно настроен на всех коммутаторах.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/s1_sh_trunk.png)

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/s2_sh_trunk.png)

   c. Отключить согласование DTP F0/1 на S1 и S2. 

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/s1_noneg.png)

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/s2_noneg.png)

   d. Проверьте с помощью команды show interfaces

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/s1_sh_noneg.png)

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/s2_sh_noneg.png)

### Шаг 2. Настройка портов доступа

   a. На S1 настройте F0/5 и F0/6 в качестве портов доступа и свяжите их с VLAN 10.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/s1_access_ports.png)

   b. На S2 настройте порт доступа Fa0/18 и свяжите его с VLAN 10.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/s2_access_port.png)

### Шаг 3. Безопасность неиспользуемых портов коммутатора

   a. На S1 и S2 переместите неиспользуемые порты из VLAN 1 в VLAN 999 и отключите неиспользуемые порты.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/s1_shut.png)

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/s2_shut_1.png)

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/s2_shut_2.png)

   b. Убедитесь, что неиспользуемые порты отключены и связаны с VLAN 999

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/s1_int_status.png)

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/s2_int_status.png)

### Шаг 4. Документирование и реализация функций безопасности порта.

   Интерфейсы F0/6 на S1 и F0/18 на S2 настроены как порты доступа. На этом шаге вы также настроите безопасность портов на этих двух портах доступа.

   a. На S1, введите команду show port-security interface f0/6  для отображения настроек по умолчанию безопасности порта для интерфейса F0/6. Запишите свои ответы ниже.

```
Защита портов: Disabled
Максимальное количество записей MAC-адресов: 1
Режим проверки на нарушение безопасности: Shutdown
Aging Time: 0 mins
Aging Type: Absolute
Secure Static Address Aging: Disabled
Sticky MAC Address: 0
```
   b. На S1 включите защиту порта на F0 / 6 со следующими настройками:

   * Максимальное количество записей MAC-адресов: 3

   * Режим безопасности: restrict

   * Aging time: 60 мин.

   * Aging type: неактивный

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz09/images/s1_fa0-6_port-sec.png)

   c. Verify port security on S1 F0/6.






