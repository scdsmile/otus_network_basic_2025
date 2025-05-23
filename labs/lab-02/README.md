# Лабораторная работа №2. Просмотр таблицы MAC-адресов коммутатора

**Цели:**

Часть 1. Создание и настройка сети

Часть 2. Изучение таблицы МАС-адресов коммутатора


## Часть 1. Создание и настройка сети

### Шаг 1. Подключите сеть в соответствии с топологией.

> Топология:
>
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-02/img/1-1_topology.png?raw=true)

### Шаг 2. Настройте узлы ПК.

> Настройки PC0:
> 
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-02/img/1-2_PC0-ip_settings.png?raw=true)

> Настройки PC1:
>
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-02/img/1-2_PC1-ip_settings.png?raw=true)

### Шаг 3. Выполните инициализацию и перезагрузку коммутаторов.

> Подключаемся к коммутатору Switch0 консольником:
>
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-02/img/1-3_switch0_reload_init.png?raw=true)
>
> После инициализации перезагружаем его:
>
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-02/img/1-3_switch1_reload_proccess.png?raw=true)
>
> Повторяем то же самое и с другим коммутатором:
>
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-02/img/1-3_switch1_reload_init.png?raw=true)

### Шаг 4. Настройте базовые параметры каждого коммутатора.

Откройте окно конфигурации

- Настройте имена устройств в соответствии с топологией.

- Настройте IP-адреса, как указано в таблице адресации.

- Назначьте **cisco** в качестве паролей консоли и VTY.

- Назначьте **class** в качестве пароля доступа к привилегированному режиму EXEC.

> Переходим в режим EXEC, и включаем режим конфигурации. Меняем хостнейм коммутатора и настраиваем ip-адрес на интерфейсе vlan 1:
>
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-02/img/1-4_S1_hostname_ip.png?raw=true)
>
> Устанавливаем пароль на линии vty и консольную линию (между делом включим logging synchronous для синхронизации вывода сообщений):
>
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-02/img/1-4_S1_line_con_vty.png?raw=true)
>
> Также ставим пароль на режим EXEC и включаем шифрование паролей:
>
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-02/img/1-4_S1_enable_pass.png?raw=true)
>
> Проделаем те же операции на коммутаторе S2:
>
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-02/img/1-4_S2_hostname_ip_line_con_vty_enable.png?raw=true)
>
> 😰 *При оформлении ДЗ вспоминаем, что забыли включить требование пароля при подключении консольником и через vty:*
>
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-02/img/1-4_vty_login_fix.png?raw=true)

## Часть 2. Изучение таблицы МАС-адресов коммутатора

Как только между сетевыми устройствами начинается передача данных, коммутатор выясняет МАС-адреса и строит таблицу.

### Шаг 1. Запишите МАС-адреса сетевых устройств.

- Откройте командную строку на PC-A и PC-B и введите команду **ipconfig /all**.

Назовите физические адреса адаптера Ethernet.

> MAC-адрес компьютера PC-A: **00E0.A3DB.1228**
>
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-02/img/2-1_PC0_ipconfig.png?raw=true)
>
> MAC-адрес компьютера PC-B: **0001.4287.8C8E**
>
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-02/img/2-1_PC1_ipconfig.png?raw=true)

- Подключитесь к коммутаторам S1 и S2 через консоль и введите команду **show interface F0/1** на каждом коммутаторе.

Назовите адреса оборудования во второй строке выходных данных команды (или зашитый адрес — bia).

> МАС-адрес коммутатора S1 Fast Ethernet 0/1: **0009.7C40.4001**
>
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-02/img/2-1_S1_F01_MAC.png?raw=true)
>
> МАС-адрес коммутатора S2 Fast Ethernet 0/1: **0050.0F25.8501**
>
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-02/img/2-1_S2_F01_MAC.png?raw=true)

### Шаг 2. Просмотрите таблицу МАС-адресов коммутатора.

Подключитесь к коммутатору S2 через консоль и просмотрите таблицу МАС-адресов до и после тестирования сетевой связи с помощью эхо-запросов.

- Подключитесь к коммутатору S2 через консоль и войдите в привилегированный режим EXEC.

- В привилегированном режиме EXEC введите команду **show mac address-table** и нажмите клавишу ввода.

Записаны ли в таблице МАС-адресов какие-либо МАС-адреса?
Какие МАС-адреса записаны в таблице? С какими портами коммутатора они сопоставлены и каким устройствам принадлежат?

> Записан MAC-адрес соседнего коммутатора, он "висит" на порту Fa0/1:
>
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-02/img/2-2_S2_show_mac_address-table.png?raw=true)
  

Если вы не записали МАС-адреса сетевых устройств в шаге 1, как можно определить, каким устройствам принадлежат МАС-адреса, используя только выходные данные команды **show mac address-table**? Работает ли это решение в любой ситуации?

> Если опираться только на данные команды **show mac address-table**, по первым трём октетам (шести символам) MAC-адреса можно определить производителя устройства.
>
> Также по типу записи (dynamic/static) можно предположить характер устройства: динамические - это, как правило, различные конечные устройства, а статические прописываются вручную админом и могут обозначать различное серверное оборудование.
>
> Имена и конфигурации устройств данная команда не выводит.
>
> Для более точного определения лучше дополнительно использовать команду **show lldp neighbors** (протокол lldp должен быть предварительно включен на всех соседних сетевых устройствах).
>
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-02/img/2-2_lldp.png?raw=true)

### Шаг 3. Очистите таблицу МАС-адресов коммутатора S2 и снова отобразите таблицу МАС-адресов.

- В привилегированном режиме EXEC введите команду **clear mac address-table dynamic** и нажмите клавишу **Enter**.

- Снова быстро введите команду **show mac address-table**.

Указаны ли в таблице МАС-адресов адреса для VLAN 1? Указаны ли другие МАС-адреса?

> При выполнении команды **clear mac address-table dynamic** таблица mac-адресов очищается, соответственно, вывод команды пустой.
> 
> Через несколько секунд пробуем выполнить команду **show mac address-table** и видим, что в ней снова зарегистрировалась динамическая запись адреса коммутатора S1.
> 
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-02/img/2-3_S2_clear_mac_address-table.png?raw=true)

### Шаг 4. С компьютера PC-B отправьте эхо-запросы устройствам в сети и просмотрите таблицу МАС-адресов коммутатора.

- На компьютере PC-B откройте командную строку и введите команду **arp -a**.

Не считая адресов многоадресной и широковещательной рассылки, сколько пар IP- и МАС-адресов устройств было получено через протокол ARP?

> На данном этапе в ARP-таблицу компьютера не занесены никакие записи.
>
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-02/img/2-4_PC1_arp-a.png?raw=true)
>

- Из командной строки PC-B отправьте эхо-запросы на компьютер PC-A, а также коммутаторы S1 и S2.

От всех ли устройств получены ответы? Если нет, проверьте кабели и IP-конфигурации.

> Запускаем команду ping ииии...
>
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-02/img/2-4_PC1_ping.png?raw=true)
>
> ... и видим, что ответ пришёл только с адреса 192.168.1.1
>
> Проверяем конфигурацию на коммутаторах и видим, что интерфейс vlan 1 выключен. упс! 😅
>
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-02/img/2-4_error_vlan_shutdown.png?raw=true)
>
> Непорядок... Включаем виртуальный интерфейс на обоих коммутаторах:
>
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-02/img/2-4_error_vlan_no_shutdown.png?raw=true)s
>
> Пингуем коммутаторы еще раз:
>
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-02/img/2-4_PC1_ping_repeat.png?raw=true)
>
> На этот раз оба-два пингуются.

- Подключившись через консоль к коммутатору S2, введите команду **show mac address-table**.
Добавил ли коммутатор в таблицу МАС-адресов дополнительные МАС-адреса? Если да, то какие адреса и устройства?

> Таблица MAC-адресов на коммутаторе обновилась:
>
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-02/img/2-4_S2_mac_address-table.png?raw=true)
>
> В нее добавились записи обо всех соседних устройствах:
> 1. компьютер PC1 на порту Fa0/18
> 2. виртуальный интерфейс vlan 1 коммутатора S1 на порту Fa0/1
> 3. компьютер PC0 на порту Fa0/1

- На компьютере PC-B откройте командную строку и еще раз введите команду **arp -a**.
Появились ли в ARP-кэше компьютера PC-B дополнительные записи для всех сетевых устройств, которым были отправлены эхо-запросы?

> Отображаем еще раз ARP-таблицу:
> 
> ![](https://github.com/scdsmile/otus_network_basic_2025/blob/main/labs/lab-02/img/2-4_PC1_arp-a_repeat.png?raw=true)
>
> В ней также появились записи всех устройств:
> 1. компьютер PC0
> 2. vlan 1 коммутатора S1
> 3. vlan 1 коммутатора S2

## Вопрос для повторения

В сетях Ethernet данные передаются на устройства по соответствующим МАС-адресам. Для этого коммутаторы и компьютеры динамически создают ARP-кэш и таблицы МАС-адресов. 
Если компьютеров в сети немного, эта процедура выглядит достаточно простой. Какие сложности могут возникнуть в крупных сетях?

> С ростом количества устройств увеличивается и размер ARP-кэша, а также размер таблиц MAC-адресов. Это увеличивает нагрузку на узлы, так как им приходится обрабатывать большие объёмы данных.
> Увеличится и объем широковещательного трафика, что замедлит пропускную способность сети.
> Ну и кэш ARP начнет медленнее обновляться и сетевые устройства будут периодически пересылать трафик не по месту назначения, что опять же замедлит пропускную способность сети.
