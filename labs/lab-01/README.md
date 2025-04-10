# Лабораторная работа №1. Базовая настройка коммутатора

## Задачи

1. Проверка конфигурации коммутатора по умолчанию;
2. Создание сети и настройка основных параметров устройства:
   - Настройте базовые параметры коммутатора;
   - Настройте IP-адрес для ПК.
3. Проверка сетевых подключений:
   - Отобразите конфигурацию устройства;
   - Протестируйте сквозное соединение, отправив эхо-запрос;
   - Протестируйте возможности удаленного управления с помощью Telnet.

## Часть 1. Создание сети и проверка настроек коммутатора по умолчанию

### Шаг 1. Создайте сеть согласно топологии.

#### Подсоедините консольный кабель, как показано в топологии. На данном этапе не подключайте кабель Ethernet компьютера PC-A.

![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/topology.png?raw=true)

#### Установите консольное подключение к коммутатору с компьютера PC-A с помощью Tera Term или другой программы эмуляции терминала.

 ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/terminal.png?raw=true)

>**Вопрос:** *Почему нужно использовать консольное подключение для первоначальной настройки коммутатора? Почему нельзя подключиться к коммутатору через Telnet или SSH?*

>**Ответ:** *Потому что на данном этапе у коммутатора еще не настроен виртуальный интерфейс, на который можно назначить ip-адрес, который необходим для подключения по telnet/ssh.*

### Шаг 2. Проверьте настройки коммутатора по умолчанию.

#### Команда **enable**:

 ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/enable.png?raw=true)

#### Команда **show running-config**:    

 ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/show_run.png?raw=true)
  
>**Вопрос:** *Сколько интерфейсов FastEthernet имеется на коммутаторе 2960?*

>**Ответ:** *24 интерфейса*

 ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/fastether_count.png?raw=true)

>**Вопрос:** *Сколько интерфейсов Gigabit Ethernet имеется на коммутаторе 2960?* 

>**Ответ:** *2 интерфейса*

 ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/gigaether_count.png?raw=true)

>**Вопрос:** *Каков диапазон значений, отображаемых в vty-линиях?*

>**Ответ:** *2 диапазона от 0 до 4 и до 5 до 15 (итого 16 vty-линий)*

 ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/vty_lines.png?raw=true)

#### Изучите файл загрузочной конфигурации (startup configuration), который содержится в энергонезависимом ОЗУ (NVRAM).

 ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/show_start.png?raw=true)

>**Вопрос:** *Почему появляется это сообщение?*

>**Ответ:** *потому что стартовая конфигурация отсутствует в энергонезависимой памяти коммутатора. Мы ещё её не сохраняли.*

#### Изучите характеристики SVI для VLAN 1.

 ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/svi_vlan1.png?raw=true)

>**Вопрос:** *Назначен ли IP-адрес сети VLAN 1?*

>**Ответ:** *нет, не назначен.*

 ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/svi_vlan1_no_ip.png?raw=true)

>**Вопрос:** *Какой MAC-адрес имеет SVI? Возможны различные варианты ответов.*

>**Ответ:**  *на VLAN 1 по-умолчанию назначен базовый MAC-адрес коммутатора, который мы можем увидеть, выполнив команду **show version**. В скобочках мы можем увидеть “зашитый” (Burned-In Address) MAC-адрес на данном интерфейсе - (bia 0002:1712:16dc).*

 ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/show_interface_vlan1.png?raw=true)

 ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/show_version_mac.png?raw=true)

>В более новых моделях коммутаторов на VLAN’ы можно назначить вручную с помощью команды **mac-address 0000.1111.2222**, в нашем же случае такой возможности нет:

 ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/vlan1_no_mac-address.png?raw=true)

>**Вопрос:** *Данный интерфейс включен?*   

>**Ответ:** *данный интерфейс выключен, судя по параметру **shutdown***

 ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/vlan1_shutdown.png?raw=true)

#### Изучите IP-свойства интерфейса SVI сети VLAN 1.

 ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/show_ip_inter_vlan1.png?raw=true)

>**Вопрос:** *Какие выходные данные вы видите?*

>**Ответ:** *Мы видим, что порт административно выключен (**shutdown**), протокол линии не активен и для SVI не назначен ip-адрес (**no ip address**).*

#### Подсоедините кабель Ethernet компьютера PC-A к порту 6 на коммутаторе и изучите IP-свойства интерфейса SVI сети VLAN 1. Дождитесь согласования параметров скорости и дуплекса между коммутатором и ПК.

 ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/topology_ethernet.png?raw=true)

 ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/port_is_up.png?raw=true)

>**Вопрос:** *Какие выходные данные вы видите?*

>**Ответ:** *Мы видим, что кабель был подключен к интерфейсу FastEthernet0/1, статус порта изменился на **up**, а также в статус **up** перешёл протокол линии на этом порту.
В этом мы можем убедиться, выполнив команду **show interface FastEthernet 0/1***

 ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/show_fastether_01.png?raw=true)

#### Изучите сведения о версии ОС Cisco IOS на коммутаторе.

 ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/show_version_ios.png?raw=true)

>**Вопрос:** *Под управлением какой версии ОС Cisco IOS работает коммутатор?*

>**Ответ:** *устройство работает под управлением версии **15.0(2)SE4***

>**Вопрос:**  *Как называется файл образа системы?*

>**Ответ:**  *файл образа системы называется **c2960-lanbasek9-mz.150-2.SE4.bin***

>Всю эту информацию мы можем получить, выполнив команду **show version**

#### Изучите свойства по умолчанию интерфейса FastEthernet, который используется компьютером PC-A.

 ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/show_fastether_mac_duplex.png?raw=true)

>**Вопрос:** *Интерфейс включен или выключен?*   

>**Ответ:** *интерфейс включен*

>**Вопрос:** *Что нужно сделать, чтобы включить интерфейс?*

>**Ответ:** *В данном случае интерфейс уже включен. Но если бы был выключен, его можно было бы включить командой **no shutdown***

>**Вопрос:** *Какой MAC-адрес у интерфейса?*

>**Ответ:** *00e0:f96c.a401*

>**Вопрос:** *Какие настройки скорости и дуплекса заданы в интерфейсе?*   

>**Ответ:** *на интерфейсе включен режим **full-duplex** (интерфейс одновременно и принимает, и передаёт данные). Скорость интерфейса **100 Mb/s***

#### Изучите флеш-память. Выполните одну из следующих команд, чтобы изучить содержимое флеш-каталога.

```
Switch# show flash 

Switch# dir flash:
```    

 ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/show_flash.png?raw=true)

>**Вопрос:** *Какое имя присвоено образу Cisco IOS?*

>**Ответ:** *имя образа - **2960-lanbasek9-mz.150-2.SE4***



## Часть 2. Настройка базовых параметров сетевых устройств

### Шаг 1. Настройте базовые параметры коммутатора.

#### В режиме глобальной конфигурации скопируйте следующие базовые параметры конфигурации и вставьте их в файл на коммутаторе S1:

```
  no ip domain-lookup

  hostname S1

  service password-encryption

  enable secret class

  banner motd #Unauthorized access is strictly prohibited. #
```

 ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/base_settings.png?raw=true)

#### Назначьте IP-адрес интерфейсу SVI на коммутаторе. Благодаря этому вы получите возможность удаленного управления коммутатором.

 ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/vlan1_set_ip.png?raw=true)

>Забыл включить интерфейс, исправляюсь :grinning:

 ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/vlan1_no_shutdown.png?raw=true)

#### Доступ через порт консоли также следует ограничить  с помощью пароля. Используйте **cisco** в качестве пароля для входа в консоль в этом задании. Конфигурация по умолчанию разрешает все консольные подключения без пароля. Чтобы консольные сообщения не прерывали выполнение команд, используйте параметр **logging synchronous**.

 ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/vty_password.png?raw=true)

>Проверим работу баннера, паролей на консольную линию и привилегированный режим, а также отключенный domain-lookup:

 ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/base_settings_check.png?raw=true)

#### Настройте каналы виртуального соединения для удаленного управления (vty), чтобы коммутатор разрешил доступ через Telnet. Если не настроить пароль VTY, будет невозможно подключиться к коммутатору по протоколу Telnet.   

 ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/vty_password.png?raw=true)

>**Вопрос:** *Для чего нужна команда **login**?*

>**Ответ:** *команда включает проверку пароля на выбранных линиях*

### Шаг 2. Настройте IP-адрес на компьютере PC-A.

#### Назначьте компьютеру IP-адрес и маску подсети в соответствии с таблицей адресации:

 ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/pc_set_ip.png?raw=true)

## Часть 3. Проверка сетевых подключений

### Шаг 1. Отобразите конфигурацию коммутатора.

#### Используйте консольное подключение на компьютере PC-A для отображения и проверки конфигурации коммутатора. Команда **show run** позволяет постранично отобразить всю текущую конфигурацию. Для пролистывания используйте клавишу пробела.   

```

S1#show run
Building configuration...

Current configuration : 1317 bytes
!
version 15.0
no service timestamps log datetime msec
no service timestamps debug datetime msec
service password-encryption
!
hostname S1
!
enable secret 5 $1$mERr$9cTjUIEqNGurQiFU.ZeCi1
!
!
!
no ip domain-lookup
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
...
<omitted>
...
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
 ip address 192.168.1.2 255.255.255.0
!
banner motd ^CUnauthorized access is strictly prohibited. ^C
!
!
!
line con 0
 password 7 0822455D0A16
 logging synchronous
 login
!
line vty 0 4
 password 7 0822455D0A16
 login
line vty 5 15
 password 7 0822455D0A16
 login
!
!
!
!
end
```


#### Проверьте параметры VLAN 1 .

![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/vlan1_check.png?raw=true)

>**Вопрос:** *Какова полоса пропускания этого интерфейса?*

>**Ответ:** *пропускная способность (bandwidth) интерфейса равна 100000 Kbit/s = 100 Mbit/s*


### Шаг 2. Протестируйте сквозное соединение, отправив эхо-запрос.

#### В командной строке компьютера PC-A с помощью утилиты ping проверьте связь с адресом PC-A.

 ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/ping_switch.png?raw=true)

#### Из командной строки компьютера PC-A отправьте эхо-запрос на административный адрес интерфейса SVI коммутатора S1.

![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/ping_cmd.png?raw=true)

>Поскольку компьютеру PC-A нужно преобразовать МАС-адрес коммутатора S1 с помощью ARP, время ожидания передачи первого пакета может истечь.

### Шаг 3. Проверьте удаленное управление коммутатором S1.

#### Используйте удаленный доступ к устройству с помощью Telnet:
  
a.     Откройте Tera Term или другую программу эмуляции терминала с возможностью Telnet. 

b.     Выберите сервер Telnet и укажите адрес управления SVI для подключения к S1.  Пароль: **cisco**.

c.     После ввода пароля **cisco** вы окажетесь в командной строке пользовательского режима. Для перехода в исполнительский режим EXEC введите команду **enable** и используйте секретный пароль **class**.

d.     Сохраните конфигурацию.

e.     Чтобы завершить сеанс Telnet, введите **exit**.

![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-01/img/telnet_connect.png?raw=true)



## Вопросы для повторения

>**Вопрос:** *Зачем необходимо настраивать пароль VTY для коммутатора?*

>**Ответ:** *чтобы предотвратить (в данном случае скорее “усложнить”) несанкционированный доступ к консоли коммутатора.*

>**Вопрос:** *Что нужно сделать, чтобы пароли не отправлялись в незашифрованном виде?*

>**Ответ:** *использовать протокол SSH для подключения к коммутатору.*


