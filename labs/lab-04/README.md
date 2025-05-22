# Лабораторная работа №4. Настройка IPv6-адресов на сетевых устройствах.

## Задачи
    Часть 1. Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора
    Часть 2. Ручная настройка IPv6-адресов
    Часть 3. Проверка сквозного соединения

## Часть 1. Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора

![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-04/img/0_topology.png?raw=true)

После подключения сети, инициализации и перезагрузки маршрутизатора и коммутатора выполните следующие действия:

### Шаг 1. Настройте маршрутизатор.

Назначьте имя хоста и настройте основные параметры устройства.

```
<------ <Базовая настройка> ------>
Switch>
Switch>en
Switch#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Switch(config)#hostname S1
S1(config)#service password-encryption 
S1(config)#enable secret class
S1(config)#banner motd #Get out!!!#
S1(config)#no ip domain-lookup
S1(config)#line con 0
S1(config-line)#password cisco
S1(config-line)#logging synchronous 
S1(config-line)#login
S1(config-line)#exit
S1(config)#line vty 0 4
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#
S1(config)#ip domain-name sw1
S1(config)#crypto key generate rsa
The name for the keys will be: S1.sw1
Choose the size of the key modulus in the range of 360 to 2048 for your
  General Purpose Keys. Choosing a key modulus greater than 512 may take
  a few minutes.

How many bits in the modulus [512]: 2048
% Generating 2048 bit RSA keys, keys will be non-exportable...[OK]


<------ <Настройка ssh> ------>
S1(config)#line vty 0 4
*Mar 1 0:17:56.272: %SSH-5-ENABLED: SSH 1.99 has been enabled
S1(config-line)#transport input ssh 
S1(config-line)#login local
S1(config-line)#exit
S1(config)#ip ssh version 2
S1(config)#ip ssh authentication-retries 3
S1(config)#ip ssh time-out 120


<------ <Настройка SDM> ------>
S1(config)#sdm prefer dual-ipv4-and-ipv6 default 
Changes to the running SDM preferences have been stored, but cannot take effect until the next reload.
Use 'show sdm prefer' to see what SDM preference is currently active.
S1(config)#do write memory
Building configuration...
[OK]
S1(config)#do reload

<------ <omitted> ------>

Press RETURN to get started!

Get out!!!

User Access Verification

Password: 

S1>en
Password: 
S1#show sdm prefer 
 The current template is "dual-ipv4-and-ipv6 default" template.
 
<------ <omitted> ------>

S1#
```

### Шаг 2. Настройте коммутатор.

Назначьте имя хоста и настройте основные параметры устройства.

```
<------ <Базовая настройка> ------>
Router>en
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#hostname R1
R1(config)#service passw
R1(config)#service password-encryption 
R1(config)#enable secret class
R1(config)#banner motd #Get out!!!#
R1(config)#no ip domain-lookup
R1(config)#line con 0
R1(config-line)#password cisco
R1(config-line)#logging synchronous
R1(config-line)#login
R1(config-line)#exit
R1(config)#ip domain-name Rt1
R1(config)#crypto key generate rsa
The name for the keys will be: R1.Rt1
Choose the size of the key modulus in the range of 360 to 2048 for your
  General Purpose Keys. Choosing a key modulus greater than 512 may take
  a few minutes.

How many bits in the modulus [512]: 2048
% Generating 2048 bit RSA keys, keys will be non-exportable...[OK]


<------ <Настройка ssh> ------>
R1(config)#line vty 0 4
*Mar 1 0:52:42.204: %SSH-5-ENABLED: SSH 1.99 has been enabled

R1(config-line)#transport input ssh
R1(config-line)#login local
R1(config-line)#exit
R1(config)#ip ssh version 2
R1(config)#ip ssh authentication-retries 3
R1(config)#ip ssh time-out 120
R1(config)#
```


## Часть 2. Ручная настройка IPv6-адресов

### Шаг 1. Назначьте IPv6-адреса интерфейсам Ethernet на R1.

a.	Назначьте глобальные индивидуальные IPv6-адреса, указанные в таблице адресации обоим интерфейсам Ethernet на R1.

```
Press RETURN to get started.

Get out!!!

User Access Verification

Password: 

R1>en
Password: 
R1#conf t
R1(config)#interface GigabitEthernet 0/0/0
R1(config-if)#ipv6 address 2001:db8:acad:a::1/64
R1(config-if)#no shutdown 
R1(config-if)#exit
R1(config)#interface GigabitEthernet 0/0/1
R1(config-if)#ipv6 address 2001:db8:acad:1::1/64
R1(config-if)#no shutdown
R1(config-if)#end
```

b.	Введите команду **show ipv6 interface brief**, чтобы проверить, назначен ли каждому интерфейсу корректный индивидуальный IPv6-адрес.

> Примечание.
>
> Отображаемый локальный адрес канала основан на адресации EUI-64, которая автоматически использует MAC-адрес интерфейса для создания 128-битного локального IPv6-адреса канала.

```
R1#
R1#show ipv6 inter
R1#show ipv6 interface br
GigabitEthernet0/0/0       [up/up]
    2001:DB8:ACAD:A::1
GigabitEthernet0/0/1       [up/up]
    2001:DB8:ACAD:1::1
GigabitEthernet0/0/2       [administratively down/down]
    unassigned
Vlan1                      [administratively down/down]
    unassigned
R1#
```

c.	Чтобы обеспечить соответствие локальных адресов канала индивидуальному адресу, вручную введите локальные адреса канала на каждом интерфейсе Ethernet на R1.

> Примечание.
>
> Каждый интерфейс маршрутизатора относится к отдельной сети. Пакеты с локальным адресом канала никогда не выходят за пределы локальной сети, а значит, для обоих интерфейсов можно указывать один и тот же локальный адрес канала.

```
R1(config)#interface GigabitEthernet 0/0/1
R1(config-if)#ipv6 address fe80::1 link-local
R1(config-if)#exit
R1(config)#interface GigabitEthernet 0/0/0
R1(config-if)#ipv6 address fe80::1 link-local
R1(config-if)#end
R1#
%SYS-5-CONFIG_I: Configured from console by console
```

d.	Используйте выбранную команду, чтобы убедиться, что локальный адрес связи изменен на fe80::1.  

```
R1#show ipv6 interface br
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
R1#
```

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
>     FF02::1**
>     FF02::1:FF00:1
>   MTU is 1500 bytes
>   ICMP error messages limited to one every 100 milliseconds
>   ICMP redirects are enabled
>   ICMP unreachables are sent
>   ND DAD is enabled, number of DAD attempts: 1
>   ND reachable time is 30000 milliseconds
> ```

### Шаг 2. Активируйте IPv6-маршрутизацию на R1.

a.	В командной строке на PC-B введите команду ipconfig, чтобы получить данные IPv6-адреса, назначенного интерфейсу ПК.


> **Вопрос:** *Назначен ли индивидуальный IPv6-адрес сетевой интерфейсной карте (NIC) на PC-B?*

> **Ответ:** * _ *


b.	Активируйте IPv6-маршрутизацию на R1 с помощью команды IPv6 unicast-routing.
, чтобы убедиться, что новая многоадресная группа назначена интерфейсу G0/0/0. Обратите внимание, что в списке групп для интерфейса G0/0 отображается группа многоадресной рассылки всех маршрутизаторов (FF02::2).

> Примечание. 
> 
> Это позволит компьютерам получать IP-адреса и данные шлюза по умолчанию с помощью функции SLAAC (Stateless Address Autoconfiguration (Автоконфигурация без сохранения состояния адреса)).

c.	Теперь, когда R1 входит в группу многоадресной рассылки всех маршрутизаторов, еще раз введите команду ipconfig на PC-B. Проверьте данные IPv6-адреса.


> **Вопрос:** *Почему PC-B получил глобальный префикс маршрутизации и идентификатор подсети, которые вы настроили на R1?* 

> **Ответ:** * _ *


### Шаг 3. Назначьте IPv6-адреса интерфейсу управления (SVI) на S1.

a.	Назначьте адрес IPv6 для S1. Также назначьте этому интерфейсу локальный адрес канала fe80::b.

b.	Проверьте правильность назначения IPv6-адресов интерфейсу управления с помощью команды show ipv6 interface vlan1.

Закройте окно настройки.

### Шаг 4. Назначьте компьютерам статические IPv6-адреса.

a.	Откройте окно Свойства Ethernet для каждого ПК и назначьте адресацию IPv6.

b.	Убедитесь, что оба компьютера имеют правильную информацию адреса IPv6. Каждый компьютер должен иметь два глобальных адреса IPv6: один статический и один SLACC.

> Примечание.
>
> При выполнении работы в среде Cisco Packet Tracer установите статический и SLACC адреса на компьютеры последовательно, отразив результаты в отчете

## Часть 3. Проверка сквозного подключения

С PC-A отправьте эхо-запрос на FE80::1. Это локальный адрес канала, назначенный G0/1 на R1.

Отправьте эхо-запрос на интерфейс управления S1 с PC-A.

Введите команду tracert на PC-A, чтобы проверить наличие сквозного подключения к PC-B.

С PC-B отправьте эхо-запрос на PC-A.

С PC-B отправьте эхо-запрос на локальный адрес канала G0/0 на R1.

> Примечание.
>
> В случае отсутствия сквозного подключения проверьте, правильно ли указаны IPv6-адреса на всех устройствах.

## Вопросы для повторения

> **Вопрос:** *Почему обоим интерфейсам Ethernet на R1 можно назначить один и тот же локальный адрес канала — FE80::1?*

> **Ответ:** * _ *

> **Вопрос:**	*Какой идентификатор подсети в индивидуальном IPv6-адресе 2001:db8:acad::aaaa:1234/64?*

> **Ответ:** * _ *