# Лабораторная работа №5. Доступ к сетевым устройствам по протоколу SSH

## 	Задачи
Часть 1. Настройка основных параметров устройства

Часть 2. Настройка маршрутизатора для доступа по протоколу SSH

Часть 3. Настройка коммутатора для доступа по протоколу SSH

Часть 4. SSH через интерфейс командной строки (CLI) коммутатора

## Таблица адресации

| Устройство | Интерфейс | IP-адрес      | Маска подсети      | Шлюз по умолчанию |
|------------|-----------|---------------|--------------------|-------------------|
| R1         | G0/0/1    | 192.168.1.1   | 255.255.255.0      | —                |
| S1         | VLAN 1    | 192.168.1.11  | 255.255.255.0      | 192.168.1.1      |
| PC-A       | NIC       | 192.168.1.3   | 255.255.255.0      | 192.168.1.1      |

## Часть 1. Настройка основных параметров устройств

В части 1 потребуется настроить топологию сети и основные параметры, такие как IP-адреса интерфейсов, доступ к устройствам и пароли на маршрутизаторе.

### Шаг 1. Создайте сеть согласно топологии.

> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-05/img/0_topology.png?raw=true)

### Шаг 2. Выполните инициализацию и перезагрузку маршрутизатора и коммутатора.

> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-05/img/1-2_reload.png?raw=true)

### Шаг 3. Настройте маршрутизатор.

a.	Подключитесь к маршрутизатору с помощью консоли и активируйте привилегированный режим EXEC.

> ```
> Press RETURN to get started!
> 
> Router>en
> Router#
> ```

b.	Войдите в режим конфигурации.

> ```
> Router#conf t
> Enter configuration commands, one per line.  End with CNTL/Z.
> Router(config)#
> ```

c.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

> ```
> Router(config)#no ip domain-lookup
> Router(config)#
> ```

d.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

> ```
> Router(config)#enable password class
> Router(config)#
> ```

e.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

> ```
> Router(config)#line con 0
> Router(config-line)#pas
> Router(config-line)#password cisco
> Router(config-line)#login
> Router(config-line)#
> ```

f.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

> ```
> Router(config-line)#line vty 0 4
> Router(config-line)#pas
> Router(config-line)#password cisco
> Router(config-line)#login
> Router(config-line)#
> Router(config-line)#exit
> ```

g.	Зашифруйте открытые пароли.

> ```
> Router(config)#service pa
> Router(config)#service password-encryption 
> Router(config)#
> ```

h.	Создайте баннер, который предупреждает о запрете несанкционированного доступа.

> ```
> Router(config)#banner motd #GET OUT!!!#
> Router(config)#
> ```

i.	Настройте и активируйте на маршрутизаторе интерфейс G0/0/1, используя информацию, приведенную в таблице адресации.

> ```
> Router(config)#interface G0/0/1
> Router(config-if)#ip ad
> Router(config-if)#ip address 192.168.1.1 255.255.255.0
> Router(config-if)#no shutdown
> 
> Router(config-if)#
> %LINK-5-CHANGED: Interface GigabitEthernet0/0/1, changed state to up
> 
> %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0/1, changed state to up
> 
> Router(config-if)#
> ```

j.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

> А как же hostname? :) Немного посвоевольничаю и задам hostname для маршрутизатора.

> ```
> Router(config-if)#exit
> Router(config)#hostname R1
> R1(config)#exit
> R1#
> %SYS-5-CONFIG_I: Configured from console by console
> 
> R1#copy running-config st
> R1#copy running-config startup-config 
> Destination filename [startup-config]? 
> Building configuration...
> [OK]
> R1#
> ```

### Шаг 4. Настройте компьютер PC-A.

a.	Настройте для PC-A IP-адрес и маску подсети.

b.	Настройте для PC-A шлюз по умолчанию.

> Пожалуй, не буду делать это в 2 этапа :)
>
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-05/img/1-4_pc_ip_settings.png?raw=true)

### Шаг 5. Проверьте подключение к сети.

Пошлите с PC-A команду Ping на маршрутизатор R1. Если эхо-запрос с помощью команды ping не проходит, найдите и устраните неполадки подключения.

> ```
> C:\>ping 192.168.1.1
> 
> Pinging 192.168.1.1 with 32 bytes of data:
> 
> Reply from 192.168.1.1: bytes=32 time=12ms TTL=255
> Reply from 192.168.1.1: bytes=32 time=1ms TTL=255
> Reply from 192.168.1.1: bytes=32 time<1ms TTL=255
> Reply from 192.168.1.1: bytes=32 time<1ms TTL=255
> 
> Ping statistics for 192.168.1.1:
>     Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
> Approximate round trip times in milli-seconds:
>     Minimum = 0ms, Maximum = 12ms, Average = 3ms
> 
> C:\>
> ```

## Часть 2. Настройка маршрутизатора для доступа по протоколу SSH

Подключение к сетевым устройствам по протоколу Telnet сопряжено с риском для безопасности, поскольку вся информация передается в виде открытого текста. Протокол SSH шифрует данные сеанса и обеспечивает аутентификацию устройств, поэтому для удаленных подключений рекомендуется использовать именно этот протокол. В части 2 вам нужно настроить маршрутизатор для приема соединений SSH по линиям VTY.

### Шаг 1. Настройте аутентификацию устройств.

При генерации ключа шифрования в качестве его части используются имя устройства и домен. Поэтому эти имена необходимо указать перед вводом команды crypto key.

a.	Задайте имя устройства.

> Аааа, теперь понял, почему мы раньше не указали hostname :) Ну ладно, он уже указан, поэтому первый этап пропустим.

b.	Задайте домен для устройства.

> ```
> R1(config)#ip domain-name dom
> R1(config)#
> ```

### Шаг 2. Создайте ключ шифрования с указанием его длины.

> ```
> R1(config)#crypto key generate rsa
> The name for the keys will be: R1.dom
> Choose the size of the key modulus in the range of 360 to 2048 for your
>   General Purpose Keys. Choosing a key modulus greater than 512 may take
>   a few minutes.
> 
> How many bits in the modulus [512]: 2048
> % Generating 2048 bit RSA keys, keys will be non-exportable...[OK]
> 
> R1(config)#
> ```

### Шаг 3. Создайте имя пользователя в локальной базе учетных записей.

Настройте имя пользователя, используя admin в качестве имени пользователя и Adm1nP@55 в качестве пароля.

> ```
> R1(config)#username admin privilege 15 pass Adm1nP@55
> R1(config)#
> ```

### Шаг 4. Активируйте протокол SSH на линиях VTY.

a.	Активируйте протоколы Telnet и SSH на входящих линиях VTY с помощью команды transport input.

> ```
> R1(config)#line vty 0 4
> R1(config-line)#transport input ssh 
> ```

b.	Измените способ входа в систему таким образом, чтобы использовалась проверка пользователей по локальной базе учетных записей.

> ```
> R1(config-line)#login local
> R1(config-line)#
> ```

### Шаг 5. Сохраните текущую конфигурацию в файл загрузочной конфигурации.

> ```
> R1(config-line)#exit
> R1(config)#exit
> R1#
> %SYS-5-CONFIG_I: Configured from console by console
> 
> R1#copy run start
> Destination filename [startup-config]? 
> Building configuration...
> [OK]
> R1#
> ```

### Шаг 6. Установите соединение с маршрутизатором по протоколу SSH.

a.	Запустите Tera Term с PC-A.

> Не буду :) Подключусь через командную строку.

b.	Установите SSH-подключение к R1. Use the username admin and password Adm1nP@55. У вас должно получиться установить SSH-подключение к R1.

> ```
> C:\>ssh -l admin 192.168.1.1
> 
> Password: 
> 
> GET OUT!!!
> 
> R1#
> ```

## Часть 3. Настройка коммутатора для доступа по протоколу SSH

В части 3 вам предстоит настроить коммутатор для приема подключений по протоколу SSH, а затем установить SSH-подключение с помощью программы Tera Term.

### Шаг 1. Настройте основные параметры коммутатора.

a.	Подключитесь к коммутатору с помощью консольного подключения и активируйте привилегированный режим EXEC.

b.	Войдите в режим конфигурации.

c.	Отключите поиск DNS, чтобы предотвратить попытки маршрутизатора неверно преобразовывать введенные команды таким образом, как будто они являются именами узлов.

d.	Назначьте class в качестве зашифрованного пароля привилегированного режима EXEC.

e.	Назначьте cisco в качестве пароля консоли и включите вход в систему по паролю.

f.	Назначьте cisco в качестве пароля VTY и включите вход в систему по паролю.

g.	Зашифруйте открытые пароли.

h.	Создайте баннер, который предупреждает о запрете несанкционированного доступа.

i.	Настройте и активируйте на коммутаторе интерфейс VLAN 1, используя информацию, приведенную в таблице адресации.

j.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

> С вашего позволения, выложу всё одним блоком:
>
> ```
> Switch>en
> Switch#conf t
> Enter configuration commands, one per line.  End with CNTL/Z.
> Switch(config)#no ip domain-l
> Switch(config)#no ip domain-lookup 
> Switch(config)#enable pa
> Switch(config)#enable password class
> Switch(config)#line vty 0 4
> Switch(config-line)#pass
> Switch(config-line)#password cisco
> Switch(config-line)#login
> Switch(config-line)#exit
> Switch(config)#serv
> Switch(config)#service pa
> Switch(config)#service password-encryption 
> Switch(config)#ba
> Switch(config)#banner m
> Switch(config)#banner motd #GET OUT!!!#
> Switch(config)#inte
> Switch(config)#interface vl
> Switch(config)#interface vlan 1
> Switch(config-if)#ip ad
> Switch(config-if)#ip address 192.168.1.11 255.255.255.0
> Switch(config-if)#exit
> Switch(config)#ip de
> Switch(config)#ip default-gateway 192.168.1.1
> Switch(config)#exit
> Switch#
> %SYS-5-CONFIG_I: Configured from console by console
> Switch#copy run start
> Destination filename [startup-config]? 
> Building configuration...
> [OK]
> Switch#
> Switch#
> ```

### Шаг 2. Настройте коммутатор для соединения по протоколу SSH.

Для настройки протокола SSH на коммутаторе используйте те же команды, которые применялись для аналогичной настройки маршрутизатора в части 2.

a.	Настройте имя устройства, как указано в таблице адресации.

b.	Задайте домен для устройства.

c.	Создайте ключ шифрования с указанием его длины.

d.	Создайте имя пользователя в локальной базе учетных записей.

e.	Активируйте протоколы Telnet и SSH на линиях VTY.

f.	Измените способ входа в систему таким образом, чтобы использовалась проверка пользователей по локальной базе учетных записей.

> Аналогично, одним блоком:
> 
> ```
> Switch#conf t
> Enter configuration commands, one per line.  End with CNTL/Z.
> Switch(config)#hostname S1
> S1(config)#ip domain-n
> S1(config)#ip domain-name dom
> S1(config)#cryp
> S1(config)#crypto k
> S1(config)#crypto key g
> S1(config)#crypto key generate rsa
> The name for the keys will be: S1.dom
> Choose the size of the key modulus in the range of 360 to 2048 for your
>   General Purpose Keys. Choosing a key modulus greater than 512 may take
>   a few minutes.
> 
> How many bits in the modulus [512]: 2048
> % Generating 2048 bit RSA keys, keys will be non-exportable...[OK]
> 
> S1(config)#username admin pr
> *Mar 1 1:9:46.942: %SSH-5-ENABLED: SSH 1.99 has been enabled
> S1(config)#username admin privilege 15 pass Adm1nP@55
> S1(config)#line vty 0 4
> S1(config-line)#tra
> S1(config-line)#transport i
> S1(config-line)#transport input ssh
> S1(config-line)#login local
> S1(config-line)#?
> ```

### Шаг 3. Установите соединение с коммутатором по протоколу SSH.

Запустите программу Tera Term на PC-A, затем установите подключение по протоколу SSH к интерфейсу SVI коммутатора S1.

Вопрос: Удалось ли вам установить SSH-соединение с коммутатором?

> ```
> C:\>ssh -l admin 192.168.1.11
> 
> % Connection timed out; remote host not responding
> C:\>
> ```
> 
> Не получилось. Раз таймаут при подключении, значит коммутатор банально не ответил на наш запрос. Веоятнее всего, проблема в настройке vlan 1.
> 
> ```
> S1#show interfaces vlan 1
> Vlan1 is administratively down, line protocol is down
>   Hardware is CPU Interface, address is 0001.c963.0c9c (bia 0001.c963.0c9c)
>   Internet address is 192.168.1.11/24
>   MTU 1500 bytes, BW 100000 Kbit, DLY 1000000 usec,
>      reliability 255/255, txload 1/255, rxload 1/255
> ```
> 
> ... ну да, так и вышло, "Vlan1 is administratively down", видимо, забыл его включить.
> 
> ```
> S1#conf t
> Enter configuration commands, one per line.  End with CNTL/Z.
> S1(config)#interface vlan 1
> S1(config-if)#no shutdown 
> 
> S1(config-if)#
> %LINK-5-CHANGED: Interface Vlan1, changed state to up
> 
> %LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up
> ```
> 
> Отлично. Теперь пробуем еще раз:
> 
> ```
> C:\>ssh -l admin 192.168.1.11
> 
> Password: 
> 
> GET OUT!!!
> 
> S1#
> ```
> 
> Подключение выполнено!


## Часть 4. Настройка протокола SSH с использованием интерфейса командной строки (CLI) коммутатора

Клиент SSH встроен в операционную систему Cisco IOS и может запускаться из интерфейса командной строки. В части 4 вам предстоит установить соединение с маршрутизатором по протоколу SSH, используя интерфейс командной строки коммутатора.

### Шаг 1. Посмотрите доступные параметры для клиента SSH в Cisco IOS.


Используйте вопросительный знак (?), чтобы отобразить варианты параметров для команды ssh.

```
S1# ssh? 
  -c Select encryption algorithm
  -l Log in using this user name
  -m Select HMAC algorithm
  -o Specify options
  -p Connect to this port
  -v Specify SSH Protocol Version
  -vrf Specify vrf name
  WORD IP-адрес или имя хоста удаленной системы
```

### Шаг 2. Установите с коммутатора S1 соединение с маршрутизатором R1 по протоколу SSH.

a.	Чтобы подключиться к маршрутизатору R1 по протоколу SSH, введите команду –l admin. Это позволит вам войти в систему под именем admin. При появлении приглашения введите в качестве пароля Adm1nP@55

```
S1# ssh -l admin 192.168.1.1
Password: 
Authorized Users Only!
R1>
```

b.	Чтобы вернуться к коммутатору S1, не закрывая сеанс SSH с маршрутизатором R1, нажмите комбинацию клавиш Ctrl+Shift+6. Отпустите клавиши Ctrl+Shift+6 и нажмите x. Отображается приглашение привилегированного режима EXEC коммутатора.

```
R1>
S1#
```

c.	Чтобы вернуться к сеансу SSH на R1, нажмите клавишу Enter в пустой строке интерфейса командной строки. Чтобы увидеть окно командной строки маршрутизатора, нажмите клавишу Enter еще раз.\

```
S1#
[Resuming connection 1 to 192.168.1.1 ... ]

R1>
```

d.	Чтобы завершить сеанс SSH на маршрутизаторе R1, введите в командной строке маршрутизатора команду exit.

```
R1# exit

[Connection to 192.168.1.1 closed by foreign host]
S1#
```

> **Вопрос:** *Какие версии протокола SSH поддерживаются при использовании интерфейса командной строки?*



## Вопрос для повторения

Как предоставить доступ к сетевому устройству нескольким пользователям, у каждого из которых есть собственное имя пользователя?

