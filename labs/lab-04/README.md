# Лабораторная работа №4. Настройка IPv6-адресов на сетевых устройствах.

## Задачи
    Часть 1. Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора
    Часть 2. Ручная настройка IPv6-адресов
    Часть 3. Проверка сквозного соединения

## Часть 1. Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора

> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-04/img/0_topology.png?raw=true)

После подключения сети, инициализации и перезагрузки маршрутизатора и коммутатора выполните следующие действия:

### Шаг 1. Настройте маршрутизатор.

Назначьте имя хоста и настройте основные параметры устройства.

> ```
> Router>en
> Router#conf t
> Enter configuration commands, one per line.  End with CNTL/Z.
> Router(config)#hostname R1
> R1(config)#service passw
> R1(config)#service password-encryption 
> R1(config)#enable secret class
> R1(config)#banner motd #Get out!!!#
> R1(config)#no ip domain-lookup
> R1(config)#line con 0
> R1(config-line)#password cisco
> R1(config-line)#logging synchronous
> R1(config-line)#login
> R1(config-line)#exit
> R1(config)#line vty 0 4
> R1(config-line)#password cisco
> R1(config-line)#login
> R1(config-line)#exit
> R1(config)#
> ```

### Шаг 2. Настройте коммутатор.

Назначьте имя хоста и настройте основные параметры устройства.

> Базовая настройка
>
> ```
> Switch>
> Switch>en
> Switch#conf t
> Enter configuration commands, one per line.  End with CNTL/Z.
> Switch(config)#hostname S1
> S1(config)#service password-encryption 
> S1(config)#enable secret class
> S1(config)#banner motd #Get out!!!#
> S1(config)#no ip domain-lookup
> S1(config)#line con 0
> S1(config-line)#password cisco
> S1(config-line)#logging synchronous 
> S1(config-line)#login
> S1(config-line)#exit
> S1(config)#line vty 0 4
> S1(config-line)#password cisco
> S1(config-line)#login
> S1(config-line)#exit
> S1(config)#

> Настройка SDM для включения ipv6
>
> S1(config)#sdm prefer dual-ipv4-and-ipv6 default 
> Changes to the running SDM preferences have been stored, but cannot take effect until the next reload.
> Use 'show sdm prefer' to see what SDM preference is currently active.
> S1(config)#do write memory
> Building configuration...
> [OK]
> S1(config)#do reload
> 
> <------ <omitted> ------>
> 
> Press RETURN to get started!
> 
> Get out!!!
> 
> User Access Verification
> 
> Password: 
> 
> S1>en
> Password: 
> S1#show sdm prefer 
>  The current template is "dual-ipv4-and-ipv6 default" template.
>  
> <------ <omitted> ------>
> 
> ```

## Часть 2. Ручная настройка IPv6-адресов

### Шаг 1. Назначьте IPv6-адреса интерфейсам Ethernet на R1.

Назначьте глобальные индивидуальные IPv6-адреса, указанные в таблице адресации обоим интерфейсам Ethernet на R1.

> ```
> Press RETURN to get started.
> 
> Get out!!!
> 
> User Access Verification
> 
> Password: 
> 
> R1>en
> Password: 
> R1#conf t
> R1(config)#interface GigabitEthernet 0/0/0
> R1(config-if)#ipv6 address 2001:db8:acad:a::1/64
> R1(config-if)#no shutdown 
> R1(config-if)#exit
> R1(config)#interface GigabitEthernet 0/0/1
> R1(config-if)#ipv6 address 2001:db8:acad:1::1/64
> R1(config-if)#no shutdown
> R1(config-if)#end
> ```

Введите команду **show ipv6 interface brief**, чтобы проверить, назначен ли каждому интерфейсу корректный индивидуальный IPv6-адрес.

> Примечание.
>
> Отображаемый локальный адрес канала основан на адресации EUI-64, которая автоматически использует MAC-адрес интерфейса для создания 128-битного локального IPv6-адреса канала.

> ```
> R1#
> R1#show ipv6 inter
> R1#show ipv6 interface br
> GigabitEthernet0/0/0       [up/up]
>     2001:DB8:ACAD:A::1
> GigabitEthernet0/0/1       [up/up]
>     2001:DB8:ACAD:1::1
> GigabitEthernet0/0/2       [administratively down/down]
>     unassigned
> Vlan1                      [administratively down/down]
>     unassigned
> R1#
> ```

Чтобы обеспечить соответствие локальных адресов канала индивидуальному адресу, вручную введите локальные адреса канала на каждом интерфейсе Ethernet на R1.

> ```
> R1(config)#interface GigabitEthernet 0/0/1
> R1(config-if)#ipv6 address fe80::1 link-local
> R1(config-if)#exit
> R1(config)#interface GigabitEthernet 0/0/0
> R1(config-if)#ipv6 address fe80::1 link-local
> R1(config-if)#end
> R1#
> %SYS-5-CONFIG_I: Configured from console by console
> ```

> Примечание.
>
> Каждый интерфейс маршрутизатора относится к отдельной сети. Пакеты с локальным адресом канала никогда не выходят за пределы локальной сети, а значит, для обоих интерфейсов можно указывать один и тот же локальный адрес канала.

Используйте выбранную команду, чтобы убедиться, что локальный адрес связи изменен на fe80::1.  

> ```
> R1#show ipv6 interface br
> GigabitEthernet0/0/0       [up/up]
>     FE80::1
>     2001:DB8:ACAD:A::1
> GigabitEthernet0/0/1       [up/up]
>     FE80::1
>     2001:DB8:ACAD:1::1
> GigabitEthernet0/0/2       [administratively down/down]
>     unassigned
> Vlan1                      [administratively down/down]
>     unassigned
> R1#
> ```

> **Вопрос:** *Какие группы многоадресной рассылки назначены интерфейсу G0/0?*

> **Ответ:**
> ```
> R1#show ipv6 interface GigabitEthernet 0/0/0
> GigabitEthernet0/0/0 is up, line protocol is up
>   IPv6 is enabled, link-local address is FE80::1
>   No Virtual link-local address(es):
>   Global unicast address(es):
>     2001:DB8:ACAD:A::1, subnet is 2001:DB8:ACAD:A::/64
>   Joined group address(es):
>     FF02::1
>     FF02::1:FF00:1
>   MTU is 1500 bytes
>   ICMP error messages limited to one every 100 milliseconds
>   ICMP redirects are enabled
>   ICMP unreachables are sent
>   ND DAD is enabled, number of DAD attempts: 1
>   ND reachable time is 30000 milliseconds
> ```
>
> Здесь мы видим, что интерфейсу назначены multicast-группы **FF02::1** (All Nodes Multicast Group) и **FF02::1:FF00:1** (Solicited Node Multicast Group).
>
> Первая группа это аналог broadcast в IPv4, то есть пакеты, отправленные на этот адрес, будут получены всеми хостами в пределах одного канала (link-local).
>
> Вторая группа используется для разрешения IPv6-адресов в MAC-адреса, чем занимается Neighbor Discovery Protocol (NDP), это некий аналог ARP-протокола в IPv4, только он отправляет не широковещательный запрос,
> а запрос на Solicited Node Multicast Group. Устройство, чей адрес разрешается, получив этот запрос, направляет ответ напрямую инициатору.


### Шаг 2. Активируйте IPv6-маршрутизацию на R1.

В командной строке на PC-B введите команду ipconfig, чтобы получить данные IPv6-адреса, назначенного интерфейсу ПК.

> ```
> Cisco Packet Tracer PC Command Line 1.0
> 
> C:\>ipconfig
> 
> FastEthernet0 Connection:(default port)
> 
>    Connection-specific DNS Suffix..: 
>    Link-local IPv6 Address.........: FE80::201:97FF:FE29:EA25
>    IPv6 Address....................: ::
>    IPv4 Address....................: 0.0.0.0
>    Subnet Mask.....................: 0.0.0.0
>    Default Gateway.................: ::
>                                      0.0.0.0
> ```

> **Вопрос:** *Назначен ли индивидуальный IPv6-адрес сетевой интерфейсной карте (NIC) на PC-B?*

> **Ответ:** *Нет, не назначен. Назначен только link-local адрес, который генерируется автоматически механизмом EUI-64.*


Активируйте IPv6-маршрутизацию на R1 с помощью команды IPv6 unicast-routing.

> ```
> R1#conf t
> Enter configuration commands, one per line.  End with CNTL/Z.
> R1(config)#ipv6 un
> R1(config)#ipv6 unicast-routing
> ```

> Примечание. 
> 
> Это позволит компьютерам получать IP-адреса и данные шлюза по умолчанию с помощью функции SLAAC (Stateless Address Autoconfiguration (Автоконфигурация без сохранения состояния адреса)).

Теперь, когда R1 входит в группу многоадресной рассылки всех маршрутизаторов, еще раз введите команду ipconfig на PC-B. Проверьте данные IPv6-адреса.

> Для начала включим автоматическое получение ipv6 адреса в конфигурации сетевого адаптера компьютера PC-B:
> 
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-04/img/2-C_PC-B_ipv6_auto.png?raw=true)
> 
> 
> ```
> C:\>ipconfig
> 
> FastEthernet0 Connection:(default port)
> 
>    Connection-specific DNS Suffix..: 
>    Link-local IPv6 Address.........: FE80::201:97FF:FE29:EA25
>    IPv6 Address....................: 2001:DB8:ACAD:A:201:97FF:FE29:EA25
>    IPv4 Address....................: 0.0.0.0
>    Subnet Mask.....................: 0.0.0.0
>    Default Gateway.................: FE80::1
>                                      0.0.0.0
> ```

> **Вопрос:** *Почему PC-B получил глобальный префикс маршрутизации и идентификатор подсети, которые вы настроили на R1?* 

> **Ответ:** *Потому что при включении **ipv6 unicast-routing**, маршрутизатор отправил на порт **RA-сообщение** (Router Advertisement), в котором содержится идентификатор подсети и префикс маршрутизации.*
> *Компьютер PC-B получил это сообщение, вытащил из него идентификатор подсети и с помощью механизма EUI-64 сгенерировал оставшуюся часть ipv6 адреса.*
> *Также комьпютер получил link-local адрес порта маршрутизатора **FE80::1** и установил его в качестве шлюза по умолчанию. Это всё работа SLAAC.*


### Шаг 3. Назначьте IPv6-адреса интерфейсу управления (SVI) на S1.

Назначьте адрес IPv6 для S1. Также назначьте этому интерфейсу локальный адрес канала fe80::b

> ```
> Get out!!!
> 
> User Access Verification
> 
> Password: 
> 
> S1>en
> Password: 
> S1#conf t
> S1(config)#interface vlan 1
> S1(config-if)#ipv6 address 2001:db8:acad:1::b/64
> S1(config-if)#ipv6 address fe80::b link-local 
> S1(config-if)#end
> ```


Проверьте правильность назначения IPv6-адресов интерфейсу управления с помощью команды show ipv6 interface vlan1

> ```
> S1#show ipv6 interface vlan 1
> Vlan1 is administratively down, line protocol is down
>   IPv6 is tentative, link-local address is FE80::B [TEN]
>   No Virtual link-local address(es):
>   Global unicast address(es):
>     2001:DB8:ACAD:1::B, subnet is 2001:DB8:ACAD:1::/64 [TEN]
>   Joined group address(es):
>     FF02::1
>   MTU is 1500 bytes
>   ICMP error messages limited to one every 100 milliseconds
>   ICMP redirects are enabled
>   ICMP unreachables are sent
>   Output features: Check hwidb
>   ND DAD is enabled, number of DAD attempts: 1
>   ND reachable time is 30000 milliseconds
> S1#
> ```


### Шаг 4. Назначьте компьютерам статические IPv6-адреса.

Откройте окно Свойства Ethernet для каждого ПК и назначьте адресацию IPv6.
Убедитесь, что оба компьютера имеют правильную информацию адреса IPv6. Каждый компьютер должен иметь два глобальных адреса IPv6: один статический и один SLACC.

> **Я не нашёл, как в CPT задать на одном интерфейсе два адреса... При выполнении поверх автонастройки команды  **ipv6config 2001:db8:acad:a::3/64** в настройках сетевой карты переключается на static...**

> PC-A - полученный с помощью SLACC ipv6-адрес:
> 
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-04/img/4_PC-A_ipv6_SLAAC.png?raw=true) 
> 
> PC-A - задаём статический ipv6-адрес:
> 
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-04/img/4_PC-A_ipv6_static.png?raw=true) 
> 
> ```
> C:\>ipconfig
> 
> FastEthernet0 Connection:(default port)
> 
>    Connection-specific DNS Suffix..: 
>    Link-local IPv6 Address.........: FE80::2E0:B0FF:FED5:505C
>    IPv6 Address....................: 2001:DB8:ACAD:1::3
>    IPv4 Address....................: 0.0.0.0
>    Subnet Mask.....................: 0.0.0.0
>    Default Gateway.................: FE80::1
>                                      0.0.0.0
> ```
> 
> PC-B - полученный с помощью SLACC ipv6-адрес:
> 
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-04/img/4_PC-B_ipv6_SLAAC.png?raw=true) 
> 
> PC-B - задаём статический ipv6-адрес:
> 
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-04/img/4_PC-B_ipv6_static.png?raw=true) 
> 
> ```
> C:\>ipconfig
> 
> FastEthernet0 Connection:(default port)
> 
>    Connection-specific DNS Suffix..: 
>    Link-local IPv6 Address.........: FE80::201:97FF:FE29:EA25
>    IPv6 Address....................: 2001:DB8:ACAD:A::3
>    IPv4 Address....................: 0.0.0.0
>    Subnet Mask.....................: 0.0.0.0
>    Default Gateway.................: FE80::1
>                                      0.0.0.0
> ```


## Часть 3. Проверка сквозного подключения

С PC-A отправьте эхо-запрос на FE80::1. Это локальный адрес канала, назначенный G0/1 на R1.

```
C:\>ping FE80::1

Pinging FE80::1 with 32 bytes of data:

Reply from FE80::1: bytes=32 time<1ms TTL=255
Reply from FE80::1: bytes=32 time<1ms TTL=255
Reply from FE80::1: bytes=32 time<1ms TTL=255
Reply from FE80::1: bytes=32 time<1ms TTL=255

Ping statistics for FE80::1:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms

C:\>
```


Отправьте эхо-запрос на интерфейс управления S1 с PC-A.

> ```
> C:\>ping 2001:db8:acad:1::b
> 
> Pinging 2001:db8:acad:1::b with 32 bytes of data:
> 
> Reply from 2001:DB8:ACAD:1::B: bytes=32 time=2011ms TTL=255
> Reply from 2001:DB8:ACAD:1::B: bytes=32 time<1ms TTL=255
> Reply from 2001:DB8:ACAD:1::B: bytes=32 time<1ms TTL=255
> Reply from 2001:DB8:ACAD:1::B: bytes=32 time<1ms TTL=255
> 
> Ping statistics for 2001:DB8:ACAD:1::B:
>     Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
> Approximate round trip times in milli-seconds:
>     Minimum = 0ms, Maximum = 2011ms, Average = 502ms
> ```

Введите команду tracert на PC-A, чтобы проверить наличие сквозного подключения к PC-B.

> ```
> C:\>tracert 2001:db8:acad:a::3
> 
> Tracing route to 2001:db8:acad:a::3 over a maximum of 30 hops: 
> 
>   1   0 ms      0 ms      0 ms      2001:DB8:ACAD:1::1
>   2   0 ms      4 ms      0 ms      2001:DB8:ACAD:A::3
> 
> Trace complete.
> ```

С PC-B отправьте эхо-запрос на PC-A.

> ```
> C:\>ping 2001:db8:acad:1::3
> 
> Pinging 2001:db8:acad:1::3 with 32 bytes of data:
> 
> Reply from 2001:DB8:ACAD:1::3: bytes=32 time<1ms TTL=127
> Reply from 2001:DB8:ACAD:1::3: bytes=32 time<1ms TTL=127
> Reply from 2001:DB8:ACAD:1::3: bytes=32 time<1ms TTL=127
> Reply from 2001:DB8:ACAD:1::3: bytes=32 time<1ms TTL=127
> 
> Ping statistics for 2001:DB8:ACAD:1::3:
>     Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
> Approximate round trip times in milli-seconds:
>     Minimum = 0ms, Maximum = 0ms, Average = 0ms
> ```

С PC-B отправьте эхо-запрос на локальный адрес канала G0/0 на R1.

> ```
> C:\>ping fe80::1
> 
> Pinging fe80::1 with 32 bytes of data:
> 
> Reply from FE80::1: bytes=32 time<1ms TTL=255
> Reply from FE80::1: bytes=32 time<1ms TTL=255
> Reply from FE80::1: bytes=32 time<1ms TTL=255
> Reply from FE80::1: bytes=32 time<1ms TTL=255
> 
> Ping statistics for FE80::1:
>     Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
> Approximate round trip times in milli-seconds:
>     Minimum = 0ms, Maximum = 0ms, Average = 0ms
> ```

## Вопросы для повторения

> **Вопрос:** *Почему обоим интерфейсам Ethernet на R1 можно назначить один и тот же локальный адрес канала — FE80::1?*

> **Ответ:** *Потому что интерфейсы маршрутизатора находится в разных сегментах сети (каналах). Трафик, полученный на этих итерфейсах, не будет маршрутизироваться в другие каналы, поэтому*
> *устройства, подключенные к интерфейсу G0/0/0 не будут видеть локальный адрес интерфейса G0/0/1 и наоборот.*
> 
> **Примечание:**
> *Проблемы могут возникнуть в том случае, если оба интерфейса коммутатора находятся в одном сегменте сети. Например, подключены в один коммутатор. Но так вообще плохо делать =)*

> **Вопрос:**	*Какой идентификатор подсети в индивидуальном IPv6-адресе 2001:db8:acad::aaaa:1234/64?*

> **Ответ:** *Адрес 2001:db8:acad::aaaa:1234/64 имеет сетевой префикс /64, глобальный префикс занимает 48 бит, это первые 3 сегмента, значит идентификатор подсети это (64-48=16 бит) четвертый сегмент адреса.*
> *Соответственно, идентификатор подсети: 0000 или просто 0.*