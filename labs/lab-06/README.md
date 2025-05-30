# Лабораторная работа №6. Внедрение маршрутизации между виртуальными локальными сетями.

## Задачи

Часть 1. Создание сети и настройка основных параметров устройства

Часть 2. Создание сетей VLAN и назначение портов коммутатора

Часть 3. Настройка транка 802.1Q между коммутаторами

Часть 4. Настройка маршрутизации между сетями VLAN

Часть 5. Проверка, что маршрутизация между VLAN работает

## Таблица адресации

| Устройство | Интерфейс      | IP-адрес       | Маска подсети    | Шлюз по умолчанию |
|:----------:|:--------------:|:--------------:|:----------------:|:-----------------:|
| R1         | G0/0/1.10<br>G0/0/1.20<br>G0/0/1.30<br>G0/0/1.1000      | 192.168.10.1<br>192.168.20.1<br>192.168.30.1   | 255.255.255.0<br>255.255.255.0<br>255.255.255.0    | —                 |
| S1         | VLAN 10        | 192.168.10.11  | 255.255.255.0    | 192.168.10.1      |
| S2         | VLAN 10        | 192.168.10.12  | 255.255.255.0    | 192.168.10.1      |
| PC-A       | NIC            | 192.168.20.3   | 255.255.255.0    | 192.168.20.1      |
| PC-B       | NIC            | 192.168.30.3   | 255.255.255.0    | 192.168.30.1      |

## Таблица VLAN

| VLAN | Имя          | Назначенный интерфейс                                 |
|:----:|:------------:|:------------------------------------------------------|
| 10   | Управление   | S1: VLAN 10<br>S2: VLAN 10                            |
| 20   | Sales        | S1: F0/6                                             |
| 30   | Operations   | S2: F0/18                                            |
| 999  | Parking_Lot  | С1: F0/2-4, F0/7-24, G0/1-2<br>С2: F0/2-17, F0/19-24, G0/1-2 |
| 1000   | Собственная   | —                                           |

## Часть 1. Создание сети и настройка основных параметров устройства

В первой части лабораторной работы вам предстоит создать топологию сети и настроить базовые параметры для узлов ПК и коммутаторов.

### Шаг 1. Создайте сеть согласно топологии.

Подключите устройства, как показано в топологии, и подсоедините необходимые кабели.

> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-06/img/0_topology.png?raw=true)

### Шаг 2. Настройте базовые параметры для маршрутизатора.

a.	Подключитесь к маршрутизатору с помощью консоли и активируйте привилегированный режим EXEC.

> ```
> Press RETURN to get started!
> 
> 
> 
> Router>en
> ```

b.	Войдите в режим конфигурации.

> ```
> Router#conf t
> Enter configuration commands, one per line.  End with CNTL/Z.
> ```

c.	Назначьте маршрутизатору имя устройства.

> ```
> Router(config)#hostname R1
> ```

d.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

> ```
> R1(config)#no ip domain-lookup 
> ```

e.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

> ```
> R1(config)#enable secret class
> ```

f.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

> ```
> R1(config)#line con 0
> R1(config-line)#password cisco
> R1(config-line)#login
> ```

g.	Установите cisco в качестве пароля виртуального терминала и активируйте вход.

> ```
> R1(config-line)#line vty 0 4
> R1(config-line)#password cisco
> R1(config-line)#login
> R1(config-line)#exit
> ```

h.	Зашифруйте открытые пароли.

> ```
> R1(config)#service password-encryption
> ```

i.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

> ```
> R1(config)#ban m #GET THE DUCK OUT!!!#
> ```

j.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

> ```
> R1(config)#exit
> R1#
> %SYS-5-CONFIG_I: Configured from console by console
> 
> R1#copy run st
> Destination filename [startup-config]? 
> Building configuration...
> [OK]
> R1#
> ```

k.	Настройте на маршрутизаторе время.

> ```
> R1#clock set 17:50:00 30 May 2025
> ```

### Шаг 3. Настройте базовые параметры каждого коммутатора.

a.	Присвойте коммутатору имя устройства.

b.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

c.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

d.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

e.	Установите cisco в качестве пароля виртуального терминала и активируйте вход.

f.	Зашифруйте открытые пароли.

g.	Создайте баннер с предупреждением о запрете несанкционированного доступа к устройству.

h.	Настройте на коммутаторах время.

i.	Сохранение текущей конфигурации в качестве начальной.

> Конфигурируем коммутатор S1:
> 
> ```
> S1#en
> S1#conf t
> Enter configuration commands, one per line.  End with CNTL/Z.
> S1(config)#hostname S1
> S1(config)#no ip domain-lookup
> S1(config)#enable secret class
> S1(config)#line con 0
> S1(config-line)#password cisco
> S1(config-line)#login
> S1(config-line)#logging synchronous
> S1(config-line)#exit
> S1(config)#line vty 0 4
> S1(config-line)#password cisco
> S1(config-line)#login
> S1(config-line)#exit
> S1(config)#service password-encryption
> S1(config)#banner motd #GET THE DUCK OUT!!!# 
> S1(config)#end
> S1#clock set 17:55:00 30 May 2025
> S1#copy running-config startup-config
> Destination filename [startup-config]? 
> Building configuration...
> [OK]
> S1#
> ```
> 
> Аналогичным образом настраиваем коммутатор S2:
> 
> ```
> Switch>
> Switch>en
> Switch#conf t
> Enter configuration commands, one per line.  End with CNTL/Z.
> Switch(config)#hostname S2
> S2(config)#no ip domain-lookup
> S2(config)#enable secret class
> S2(config)#line con 0
> S2(config-line)#password cisco
> S2(config-line)#login
> S2(config-line)#logging synchronous
> S2(config-line)#exit
> S2(config)#line vty 0 4
> S2(config-line)#password cisco
> S2(config-line)#login
> S2(config-line)#exit
> S2(config)#service password-encryption
> S2(config)#banner motd #GET THE DUCK OUT!!!# 
> S2(config)#end
> S2#clock set 17:59:00 30 May 2025
> S2#copy running-config startup-config
> Destination filename [startup-config]? 
> Building configuration...
> [OK]
> S2#
> ```
> 
> **P.S.** Да, я уже сделал себе файлик с базовой конфигурацией коммутатора, поэтому просто ctrl+c + ctrl+v...


### Шаг 4. Настройте узлы ПК.

Адреса ПК можно посмотреть в таблице адресации.

> Конфигурация PC-A:
>
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-06/img/1-4_pc-a_ip_config.png?raw=true)
>
> Конфигурация PC-B:
>
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-06/img/1-4_pc-b_ip_config.png?raw=true)

## Часть 2. Создание сетей VLAN и назначение портов коммутатора

Во второй части вы создадите VLAN, как указано в таблице выше, на обоих коммутаторах. Затем вы назначите VLAN соответствующему интерфейсу и проверите настройки конфигурации. Выполните следующие задачи на каждом коммутаторе.

### Шаг 1. Создайте сети VLAN на коммутаторах.

a.	Создайте и назовите необходимые VLAN на каждом коммутаторе из таблицы выше.

> ```
> S1#conf t
> Enter configuration commands, one per line.  End with CNTL/Z.
> S1(config)#vlan 10
> S1(config-vlan)#name mgmt
> S1(config-vlan)#exit
> S1(config)#vlan 20
> S1(config-vlan)#name Sales
> S1(config-vlan)#vlan 30
> S1(config-vlan)#name Operations
> S1(config-vlan)#vlan 999
> S1(config-vlan)#name Parking
> S1(config-vlan)#vlan 1000
> S1(config-vlan)#name Native
> S1(config-vlan)#exit
> S1(config)#do show vlan
> 
> VLAN Name                             Status    Ports
> ---- -------------------------------- --------- -------------------------------
> 1    default                          active    Fa0/1, Fa0/2, Fa0/3, Fa0/4
>                                                 Fa0/5, Fa0/6, Fa0/7, Fa0/8
>                                                 Fa0/9, Fa0/10, Fa0/11, Fa0/12
>                                                 Fa0/13, Fa0/14, Fa0/15, Fa0/16
>                                                 Fa0/17, Fa0/18, Fa0/19, Fa0/20
>                                                 Fa0/21, Fa0/22, Fa0/23, Fa0/24
>                                                 Gig0/1, Gig0/2
> 10   mgmt                             active    
> 20   Sales                            active    
> 30   Operations                       active    
> 999  Parking                          active    
> 1000 Native                           active    
> 1002 fddi-default                     active    
> 1003 token-ring-default               active    
> 1004 fddinet-default                  active    
> 1005 trnet-default                    active    
> 
> <---- omitted ---->
>
> S1(config)#
> ```
>
> **P.S.** можно было короче, используя команду **show vlan brief**, не пришлось бы вырезать остальную часть вывода :)
>
> Повторим настройку на коммутаторе S2:
>
> ```
> S2(config)#
> S2(config)#vlan 10
> S2(config-vlan)#name mgmt
> S2(config-vlan)#vlan 20
> S2(config-vlan)#name Sales
> S2(config-vlan)#vlan 30
> S2(config-vlan)#name Operations
> S2(config-vlan)#vlan 999
> S2(config-vlan)#name Parking
> S2(config-vlan)#vlan 1000
> S2(config-vlan)#name Native
> S2(config-vlan)#exit
> S2(config)#
> ```
> 
> **#show vlan** выводить не буду, дабы не растягивать лабу :)

b.	Настройте интерфейс управления и шлюз по умолчанию на каждом коммутаторе, используя информацию об IP-адресе в таблице адресации. 

> Конфигурация MGMT-влана на коммутаторе S1:
> 
> ```
> S1(config)#interface vlan 10
> S1(config-if)#
> %LINK-5-CHANGED: Interface Vlan10, changed state to up
> 
> S1(config-if)#ip ad
> S1(config-if)#ip address 192.168.10.11 255.255.255.0
> S1(config-if)#exit
> S1(config)#ip defa
> S1(config)#ip default-gateway 192.168.10.1
> S1(config)#
> ```
> 
> Конфигурация MGMT-влана на коммутаторе S2:
> 
> ```
> S2(config)#interface vlan 10
> S2(config-if)#
> %LINK-5-CHANGED: Interface Vlan10, changed state to up
> 
> S2(config-if)#ip address 192.168.10.12 255.255.255.0
> S2(config-if)#exit
> S2(config)#ip default-gateway 192.168.10.1
> S2(config)#
> ```

c.	Назначьте все неиспользуемые порты коммутатора VLAN Parking_Lot, настройте их для статического режима доступа и административно деактивируйте их.

> ```
> S1(config)#interface range F0/2-5,F0/7-24,G0/1-2
> S1(config-if-range)#swit
> S1(config-if-range)#switchport mode access 
> S1(config-if-range)#switchport access vlan 999
> S1(config-if-range)#shutdown
> 
> %LINK-5-CHANGED: Interface FastEthernet0/2, changed state to administratively down
> 
> <---- omitted ---->
> 
> %LINK-5-CHANGED: Interface FastEthernet0/5, changed state to administratively down
> 
> %LINK-5-CHANGED: Interface FastEthernet0/7, changed state to administratively down
> 
> <---- omitted ---->
> 
> %LINK-5-CHANGED: Interface FastEthernet0/24, changed state to administratively down
> 
> %LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to administratively down
> 
> %LINK-5-CHANGED: Interface GigabitEthernet0/2, changed state to administratively down
> S1(config-if-range)#
> S1(config-if-range)#do show vlan
> 
> VLAN Name                             Status    Ports
> ---- -------------------------------- --------- -------------------------------
> 1    default                          active    Fa0/1, Fa0/6
> 10   mgmt                             active    
> 20   Sales                            active    
> 30   Operations                       active    
> 999  Parking                          active    Fa0/2, Fa0/3, Fa0/4, Fa0/5
>                                                 Fa0/7, Fa0/8, Fa0/9, Fa0/10
>                                                 Fa0/11, Fa0/12, Fa0/13, Fa0/14
>                                                 Fa0/15, Fa0/16, Fa0/17, Fa0/18
>                                                 Fa0/19, Fa0/20, Fa0/21, Fa0/22
>                                                 Fa0/23, Fa0/24, Gig0/1, Gig0/2
> 1000 Native                           active    
> 1002 fddi-default                     active    
> 1003 token-ring-default               active    
> 1004 fddinet-default                  active    
> 1005 trnet-default                    active    
> ```
> 
> Аналогично повторим для S2 (вырезан вывод об отключении портов):
> 
> ```
> S2(config)#interface range F0/2-17,F0/19-24,G0/1-2
> S2(config-if-range)#switchport mode access 
> S2(config-if-range)#switchport access vlan 999
> S2(config-if-range)#shutdown
> 
> <---- omitted ---->
> 
> S2(config-if-range)#do show vlan
> 
> VLAN Name                             Status    Ports
> ---- -------------------------------- --------- -------------------------------
> 1    default                          active    Fa0/1, Fa0/18
> 10   mgmt                             active    
> 20   Sales                            active    
> 30   Operations                       active    
> 999  Parking                          active    Fa0/2, Fa0/3, Fa0/4, Fa0/5
>                                                 Fa0/6, Fa0/7, Fa0/8, Fa0/9
>                                                 Fa0/10, Fa0/11, Fa0/12, Fa0/13
>                                                 Fa0/14, Fa0/15, Fa0/16, Fa0/17
>                                                 Fa0/19, Fa0/20, Fa0/21, Fa0/22
>                                                 Fa0/23, Fa0/24, Gig0/1, Gig0/2
> 1000 Native                           active    
> 1002 fddi-default                     active    
> 1003 token-ring-default               active    
> 1004 fddinet-default                  active    
> 1005 trnet-default                    active    
> ```

### Шаг 2. Назначьте сети VLAN соответствующим интерфейсам коммутатора.

a.	Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа.

> Настройм access-порт на коммутаторах S1 и S2:
> 
> ```
> S1(config-if-range)#interface f0/6
> S1(config-if)#switchport mode access 
> S1(config-if)#switchport access vlan 20
> ```
> 
> ```
> S2(config-if-range)#int f0/18
> S2(config-if)#sw mode a
> S2(config-if)#sw a vlan 30
> S2(config-if)#
> ```

b.	Убедитесь, что VLAN назначены на правильные интерфейсы.

> Проверим командой **show vlan** на S1:
> 
> ```
> S1(config-if)#do sh vl
> 
> VLAN Name                             Status    Ports
> ---- -------------------------------- --------- -------------------------------
> 1    default                          active    Fa0/1
> 10   mgmt                             active    
> 20   Sales                            active    Fa0/6
> 
> <---- omitted ---->
> ```
> 
> ... и на S2:
>
> ```
> S2(config-if)#do sh vlan
> 
> VLAN Name                             Status    Ports
> ---- -------------------------------- --------- -------------------------------
> 1    default                          active    Fa0/1
> 10   mgmt                             active    
> 20   Sales                            active    
> 30   Operations                       active    Fa0/18
> 
> <---- omitted ---->
> ```
>
> Видим, что порты, смотрящие в сторону конечных устройств, в нужных vlan.


## Часть 3. Конфигурация магистрального канала стандарта 802.1Q между коммутаторами

В части 3 вы вручную настроите интерфейс F0/1 как транк.

### Шаг 1. Вручную настройте магистральный интерфейс F0/1 на коммутаторах S1 и S2.

a.	Настройка статического транкинга на интерфейсе F0/1 для обоих коммутаторов.

> Настройка транка на коммутаторе S1:
> 
> ```
> S1(config-if)#int f0/1
> S1(config-if)#switchport mode trunk 
> S1(config-if)#
> ```
> 
> Аналогично для S2:
> 
> ```
> S2(config-if)#int f0/1
> S2(config-if)#sw m tr
> S2(config-if)#
> ```


b.	Установите native VLAN 1000 на обоих коммутаторах.

> Включим native vlan 1000 на коммутаторе S2:
> 
> ```
> S2(config-if)#switchport trunk native vlan 1000
> S2(config-if)#
> 
> %SPANTREE-2-RECV_PVID_ERR: Received BPDU with inconsistent peer vlan id 1 on FastEthernet0/1 VLAN1000.
> 
> %SPANTREE-2-BLOCK_PVID_LOCAL: Blocking FastEthernet0/1 on VLAN1000. Inconsistent local vlan.
> 
> S2(config-if)#
> ```
> 
> Видим, что протокол STP начал ругаться на несоответствие vlan на коммутаторах, так как от соседнего коммутатора получен BDPU с vlan id 1. Поэтому он блокирует интерфейс F0/1.
> Штош, исправим это, указав native vlan 1000 на коммутаторе S1:
> 
> ```
> S1(config-if)#sw tr n vlan 1000
> S1(config-if)#
> ```
> 
> После чего блокировка на S2 снимается:
> 
> ```
> S2(config-if)#
> 
> %SPANTREE-2-UNBLOCK_CONSIST_PORT: Unblocking FastEthernet0/1 on VLAN1000. Port consistency restored.
> 
> S2(config-if)#
> ```

c.	Укажите, что VLAN 10, 20, 30 и 1000 могут проходить по транку.

> Настроим порт на S1:
> 
> ```
> S1(config-if)#switchport trunk allowed vlan 10,20,30,1000
> S1(config-if)#
> ```
> 
> И аналогично на S2:
> 
> ```
> S2(config-if)#sw t a vlan 10,20,30,1000
> S2(config-if)#
> ```

d.	Проверьте транки, native VLAN и разрешенные VLAN через транк.

> Для этого используем команду **show int trunk**:
> 
> ```
> S2#show interfaces trunk 
> Port        Mode         Encapsulation  Status        Native vlan
> Fa0/1       on           802.1q         trunking      1000
> 
> Port        Vlans allowed on trunk
> Fa0/1       10,20,30,1000
> 
> Port        Vlans allowed and active in management domain
> Fa0/1       10,20,30,1000
> 
> Port        Vlans in spanning tree forwarding state and not pruned
> Fa0/1       10,20,30,1000
> ```
> 
> ```
> S1#show interfaces trunk
> Port        Mode         Encapsulation  Status        Native vlan
> Fa0/1       on           802.1q         trunking      1000
> 
> Port        Vlans allowed on trunk
> Fa0/1       10,20,30,1000
> 
> Port        Vlans allowed and active in management domain
> Fa0/1       10,20,30,1000
> 
> Port        Vlans in spanning tree forwarding state and not pruned
> Fa0/1       10,20,30,1000
> ```

### Шаг 2. Вручную настройте магистральный интерфейс F0/5 на коммутаторе S1.

a.	Настройте интерфейс S1 F0/5 с теми же параметрами транка, что и F0/1. Это транк до маршрутизатора.

> ```
> S1(config)#int f0/5
> S1(config-if)#switchport mode trunk 
> S1(config-if)#switchport trunk native vlan 1000
> S1(config-if)#switchport trunk allowed vlan 10,20,30,1000
> S1(config-if)#
> ```

b.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

> ```
> S1(config-if)#end
> S1#
> %SYS-5-CONFIG_I: Configured from console by console
> 
> S1#copy run start
> Destination filename [startup-config]? 
> Building configuration...
> [OK]
> S1#
> ```

c.	Проверка транкинга.

> ```
> S1#show interfaces trunk 
> Port        Mode         Encapsulation  Status        Native vlan
> Fa0/1       on           802.1q         trunking      1000
> 
> Port        Vlans allowed on trunk
> Fa0/1       10,20,30,1000
> 
> Port        Vlans allowed and active in management domain
> Fa0/1       10,20,30,1000
> 
> Port        Vlans in spanning tree forwarding state and not pruned
> Fa0/1       10,20,30,1000
> ```
> 
> Хм, ничего не изменилось... А, ну да, я же отключил порт f0/5 в самом начале и перенес его в vlan 999.
> 
> ```
> S1#conf t
> Enter configuration commands, one per line.  End with CNTL/Z.
> S1(config)#interface f0/5
> S1(config-if)#no shutdown 
> 
> %LINK-5-CHANGED: Interface FastEthernet0/5, changed state to down
> S1(config-if)#do show vlan
> 
> VLAN Name                             Status    Ports
> ---- -------------------------------- --------- -------------------------------
> 1    default                          active    Fa0/5
> 10   mgmt                             active    
> 20   Sales                            active    Fa0/6
> 30   Operations                       active    
> 999  Parking                          active    Fa0/2, Fa0/3, Fa0/4, Fa0/7, Fa0/8
>                                                 Fa0/9, Fa0/10, Fa0/11, Fa0/12
>                                                 Fa0/13, Fa0/14, Fa0/15, Fa0/16
>                                                 Fa0/17, Fa0/18, Fa0/19, Fa0/20
>                                                 Fa0/21, Fa0/22, Fa0/23, Fa0/24
>                                                 Gig0/1, Gig0/2
> ```                                                
> 
> Оп, внезапно оказывается, что порт F0/5 попал в vlan 1. Хотя настройки у него правильные:
> 
> ```
> S1(config-if)#do show run
> 
> <---- omitted ---->
> !
> interface FastEthernet0/5
>  switchport trunk native vlan 1000
>  switchport trunk allowed vlan 10,20,30,1000
>  switchport mode trunk
> !
> <---- omitted ---->
> ```
> 
> ...Спустя 15 минут...
> 
> Долго ковырялся, в итоге понял, что проблема в том, что порт на другом конце (на маршрутизаторе) выключен, соответственно коммутатор не может поднять транк на этом порту.
> Поэтому порт переключается в режим access и назначается в vlan по умолчанию (vlan 1).
> 
> Вот я, сам того не ожидая, ответил на
>
> ***Вопрос:** Что произойдет, если G0/0/1 на R1 будет отключен?*


## Часть 4. Настройка маршрутизации между сетями VLAN

### Шаг 1. Настройте маршрутизатор.

a.	При необходимости активируйте интерфейс G0/0/1 на маршрутизаторе.

> Это я уже сделал при поиске проблемы на прошлом шаге :)
> 
> ```
> R1(config)#int g0/0/1
> R1(config-if)#no shutdown 
> 
> R1(config-if)#
> %LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up
> 
> %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up
> 
> %LINK-5-CHANGED: Interface GigabitEthernet0/0/1.1000, changed state to up
> 
> %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1.1000, changed state to up
> 
> R1(config-if)#
> ```
> 
> Ну и подынтерфейс g0/0/1.1000 я сделал тогда же, поэтому он сразу поднялся при включении интерфейса g0/0/1

b.	Настройте подинтерфейсы для каждой VLAN, как указано в таблице IP-адресации. Все подинтерфейсы используют инкапсуляцию 802.1Q. Убедитесь, что подинтерфейсу для native VLAN не назначен IP-адрес. Включите описание для каждого подинтерфейса.

> Подыинтерфейс для vlan 10:
>
> ```
> R1(config)#int g0/0/1.10
> R1(config-subif)#
> %LINK-5-CHANGED: Interface GigabitEthernet0/0/1.10, changed state to up
> 
> %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1.10, changed state to up
> 
> R1(config-subif)#description Sales
> R1(config-subif)#en
> R1(config-subif)#encapsulation 
> R1(config-subif)#encapsulation dot1Q 10
> R1(config-subif)#ip address 192.168.10.1 255.255.255.0
> ```
> 
> Подыинтерфейс для vlan 20:
>
> ```
> R1(config-subif)#int g0/0/1.20
> R1(config-subif)#
> %LINK-5-CHANGED: Interface GigabitEthernet0/0/1.20, changed state to up
> 
> %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1.20, changed state to up
> 
> R1(config-subif)#encapsulation dot1Q 20
> R1(config-subif)#ip address 192.168.20.1 255.255.255.0
> R1(config-subif)#description Sales
> ```
> 
> Подыинтерфейс для vlan 30:
>
> ```
> R1(config-subif)#int g0/0/1.30
> R1(config-subif)#description Operations
> R1(config-subif)#encapsulation dot1Q 30
> R1(config-subif)#ip address 192.168.30.1 255.255.255.0
> R1(config-subif)#
> ```
> 
> Native vlan я уже поднял ранее, поэтому просто изменю ему описание:
> 
> ```
> R1(config-subif)#int g0/0/1.1000
> R1(config-subif)#description Native
> ```


c.	Убедитесь, что вспомогательные интерфейсы работают

> Сделаем это командой **show ip interfaces brief**:
> 
> ```
> R1#show ip int br
> Interface              IP-Address      OK? Method Status                Protocol 
> GigabitEthernet0/0/0   unassigned      YES unset  administratively down down 
> GigabitEthernet0/0/1   unassigned      YES unset  up                    up 
> GigabitEthernet0/0/1.10192.168.10.1    YES manual up                    up 
> GigabitEthernet0/0/1.20192.168.20.1    YES manual up                    up 
> GigabitEthernet0/0/1.30192.168.30.1    YES manual up                    up 
> GigabitEthernet0/0/1.1000unassigned      YES unset  up                    up 
> GigabitEthernet0/0/2   unassigned      YES unset  administratively down down 
> Vlan1                  unassigned      YES unset  administratively down down
> ```


## Часть 5. Проверьте, работает ли маршрутизация между VLAN

### Шаг 1. Выполните следующие тесты с PC-A. Все должно быть успешно.

Примечание. Возможно, вам придется отключить брандмауэр ПК для работы ping

a.	Отправьте эхо-запрос с PC-A на шлюз по умолчанию.

> ```
> C:\>ping 192.168.20.1
> 
> Pinging 192.168.20.1 with 32 bytes of data:
> 
> Reply from 192.168.20.1: bytes=32 time=2ms TTL=255
> Reply from 192.168.20.1: bytes=32 time=1ms TTL=255
> Reply from 192.168.20.1: bytes=32 time<1ms TTL=255
> Reply from 192.168.20.1: bytes=32 time<1ms TTL=255
> 
> Ping statistics for 192.168.20.1:
>     Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
> Approximate round trip times in milli-seconds:
>     Minimum = 0ms, Maximum = 2ms, Average = 0ms
> ```

b.	Отправьте эхо-запрос с PC-A на PC-B.

> ```
> C:\>ping 192.168.30.3
> 
> Pinging 192.168.30.3 with 32 bytes of data:
> 
> 
> Ping statistics for 192.168.30.3:
>     Packets: Sent = 1, Received = 0, Lost = 1 (100% loss),
> 
> Control-C
> ^C
> C:\>ping 192.168.30.3
> 
> Pinging 192.168.30.3 with 32 bytes of data:
> 
> Reply from 192.168.30.3: bytes=32 time<1ms TTL=127
> Reply from 192.168.30.3: bytes=32 time<1ms TTL=127
> Reply from 192.168.30.3: bytes=32 time<1ms TTL=127
> Reply from 192.168.30.3: bytes=32 time<1ms TTL=127
> 
> Ping statistics for 192.168.30.3:
>     Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
> Approximate round trip times in milli-seconds:
>     Minimum = 0ms, Maximum = 0ms, Average = 0ms
> ```
> 
> Я сначала подумал, что что-то настроил не так, но оказалось, что это была просто задержка ICMP из-за ARP-запроса, типичная для CPT.

c.	Отправьте команду ping с компьютера PC-A на коммутатор S2.

> ```
> C:\>ping 192.168.10.12
> 
> Pinging 192.168.10.12 with 32 bytes of data:
> 
> Reply from 192.168.10.12: bytes=32 time<1ms TTL=254
> Reply from 192.168.10.12: bytes=32 time<1ms TTL=254
> Reply from 192.168.10.12: bytes=32 time<1ms TTL=254
> Reply from 192.168.10.12: bytes=32 time<1ms TTL=254
> 
> Ping statistics for 192.168.10.12:
>     Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
> Approximate round trip times in milli-seconds:
>     Minimum = 0ms, Maximum = 0ms, Average = 0ms
> ```

### Шаг 2. Пройдите следующий тест с PC-B

В окне командной строки на PC-B выполните команду tracert на адрес PC-A.

> ```
> C:\>tracert 192.168.20.3
> 
> Tracing route to 192.168.20.3 over a maximum of 30 hops: 
> 
>   1   7 ms      0 ms      0 ms      192.168.30.1
>   2   0 ms      0 ms      1 ms      192.168.20.3
> 
> Trace complete.
> ```

> **Вопрос:** *Какие промежуточные IP-адреса отображаются в результатах?*

> **Ответ:** *В промежуточных адресах мы видим ip-адрес подынтерфейса g0/0/1.30 маршрутизатора R1. Он же является шлюзом для компьютера PC-B.*

