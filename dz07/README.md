# Лабораторная работа. Развертывание коммутируемой сети с резервными каналами
## Топология
![](https://github.com/pogodin2004/otusNetwork/blob/main/dz07/images/topology.png)
## Таблица адресации

| Устройство | Интерфейс | IP-адрес    | Маска подсети | 
| ---------- |:---------:|:-----------:|:-------------:|
| S1         | VLAN 1    | 192.168.1.1 | 255.255.255.0 |
| S2         | VLAN 1    | 192.168.1.2 | 255.255.255.0 |
| S3         | VLAN 1    | 192.168.1.3 | 255.255.255.0 |

## Цели
### Часть 1. Создание сети и настройка основных параметров устройства
### Часть 2. Выбор корневого моста
### Часть 3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов
### Часть 4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов

## Общие сведения/сценарий

   Избыточность позволяет увеличить доступность устройств в топологии сети за счёт устранения единой точки отказа. Избыточность в коммутируемой сети обеспечивается посредством использования нескольких коммутаторов или нескольких каналов между коммутаторами. Когда в проекте сети используется физическая избыточность, возможно возникновение петель и дублирование кадров.

   Протокол spanning-tree (STP) был разработан как механизм предотвращения возникновения петель на 2-м уровне для избыточных каналов коммутируемой сети. Протокол STP обеспечивает наличие только одного логического пути между всеми узлами назначения в сети путем намеренного блокирования резервных путей, которые могли бы вызвать петлю.

   В этой лабораторной работе команда **show spanning-tree** используется для наблюдения за процессом выбора протоколом STP корневого моста. Также вы будете наблюдать за процессом выбора портов с учетом стоимости и приоритета.

   **Примечание.** Используются коммутаторы Cisco Catalyst 2960s с Cisco IOS версии 15.0(2) (образ lanbasek9). Допускается использование других моделей коммутаторов и других версий Cisco IOS. В зависимости от модели устройства и версии Cisco IOS доступные команды и результаты их выполнения могут отличаться от тех, которые показаны в лабораторных работах. 

   **Примечание.** Убедитесь, что все настройки коммутатора удалены и загрузочная конфигурация отсутствует. Если вы не уверены, обратитесь к инструктору.


## Необходимые ресурсы
  * 3 коммутатора (Cisco 2960 с операционной системой Cisco IOS 15.0(2) (образ lanbasek9) или аналогичная модель)
  * Консольные кабели для настройки устройств Cisco IOS через консольные порты
  * Кабели Ethernet, расположенные в соответствии с топологией

## Часть 1. Создание сети и настройка основных параметров устройства

  В части 1 вам предстоит настроить топологию сети и основные параметры коммутаторов.

### Шаг 1. Создайте сеть согласно топологии.

   Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz07/images/my_topology.png)

### Шаг 2: Выполните инициализацию и перезагрузку коммутаторов.

```
Switch#erase startup-config 
Erasing the nvram filesystem will remove all configuration files! Continue? [confirm]y[OK]
Erase of nvram: complete
%SYS-7-NV_BLOCK_INIT: Initialized the geometry of nvram
Switch#
Switch#reload
Proceed with reload? [confirm]yC2960 Boot Loader (C2960-HBOOT-M) Version 12.2(25r)FX, RELEASE SOFTWARE (fc4)
Cisco WS-C2960-24TT (RC32300) processor (revision C0) with 21039K bytes of memory.
2960-24TT starting...
```

### Шаг 3: Настройте базовые параметры каждого коммутатора.

   a. Отключите поиск DNS.

   b. Присвойте имена устройствам в соответствии с топологией.

   c. Назначьте class в качестве зашифрованного пароля доступа к привилегированному режиму.

   d. Назначьте cisco в качестве паролей консоли и VTY и активируйте вход для консоли и VTY каналов.

   e. Настройте logging synchronous для консольного канала.

   f. Настройте баннерное сообщение дня (MOTD) для предупреждения пользователей о запрете несанкционированного доступа.

   g. Задайте IP-адрес, указанный в таблице адресации для VLAN 1 на всех коммутаторах.

   h. Скопируйте текущую конфигурацию в файл загрузочной конфигурации.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz07/images/s1_startup_config.png)

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz07/images/s2_startup_config.png)

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz07/images/s3_startup_config.png)

### Шаг 4: Проверьте связь.

   Проверьте способность компьютеров обмениваться эхо-запросами.

   Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S2?

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz07/images/ping_s1-s2.png)

   Успешно ли выполняется эхо-запрос от коммутатора S1 на коммутатор S3?

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz07/images/ping_s1-s3.png)

   Успешно ли выполняется эхо-запрос от коммутатора S2 на коммутатор S3?

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz07/images/ping_s2-s3.png)

   Выполняйте отладку до тех пор, пока ответы на все вопросы не будут положительными.

## Часть 2: Определение корневого моста

   Для каждого экземпляра протокола spanning-tree (коммутируемая сеть LAN или широковещательный домен) существует коммутатор, выделенный в качестве корневого моста. Корневой мост служит точкой привязки для всех расчётов протокола spanning-tree, позволяя определить избыточные пути, которые следует заблокировать.

   Процесс выбора определяет, какой из коммутаторов станет корневым мостом. Коммутатор с наименьшим значением идентификатора моста (BID) становится корневым мостом. Идентификатор BID состоит из значения приоритета моста, расширенного идентификатора системы и MAC-адреса коммутатора. Значение приоритета может находиться в диапазоне от 0 до 65535 с шагом 4096. По умолчанию используется значение 32768.

### Шаг 1: Отключите все порты на коммутаторах.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz07/images/s1_shutdown.png)

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz07/images/s2_shutdown.png)

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz07/images/s3_shutdown.png)
   

### Шаг 2: Настройте подключенные порты в качестве транковых.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz07/images/s1_trunk_mode.png)

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz07/images/s2_trunk_mode.png)

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz07/images/s3_trunk_mode.png)


### Шаг 3: Включите порты F0/2 и F0/4 на всех коммутаторах.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz07/images/s1_int_up.png)

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz07/images/s2_int_up.png)

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz07/images/s3_int_up.png)


### Шаг 4: Отобразите данные протокола spanning-tree.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz07/images/s1_spa.png)

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz07/images/s2_spa.png)

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz07/images/s3_spa.png)

   В схему ниже запишите роль и состояние (Sts) активных портов на каждом коммутаторе в топологии.

![](https://github.com/pogodin2004/otusNetwork/blob/main/dz07/images/topology_with_stp.png)

   С учетом выходных данных, поступающих с коммутаторов, ответьте на следующие вопросы.


   Какой коммутатор является корневым мостом? 

```
Коммутатор S2 является корневым мостом.
```

   Почему этот коммутатор был выбран протоколом spanning-tree в качестве корневого моста?

```
S2 имеет самый низкий MAC-адрес.
```

   Какие порты на коммутаторе являются корневыми портами?

```
На S1 - F0/2, на S3 - F0/2.
```

   Какие порты на коммутаторе являются назначенными портами?

```
На S1 - F0/4, на S2 - F0/2 и F0/4.
```

   Какой порт отображается в качестве альтернативного и в настоящее время заблокирован?

```
На S3 - F0/4.
```

   Почему протокол spanning-tree выбрал этот порт в качестве невыделенного (заблокированного) порта?

```
Так как S3 имеет MAC-адрес выше, чем S2.
```

********************************************************************************************


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
S2(config-if)#sw tru allowed vlan 10,20,30,1000
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
R1(config-subif)#enc dot 1000 native
R1(config-subif)#exit
```
![](https://github.com/pogodin2004/otusNetwork/blob/main/dz06/images/r1_native_vlan.png)


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



