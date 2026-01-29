
# Лабораторная работа № 4. Настройка IPv6-адресов на сетевых устройствах 

## Топология

<img width="952" height="128" alt="image" src="https://github.com/user-attachments/assets/9560e7da-92e5-4ff5-8ec0-a9c555006536" />

## Таблица адресации

<img width="752" height="216" alt="image" src="https://github.com/user-attachments/assets/e30a5458-6967-47ba-b8ed-8842c843e52c" />



## Задачи

#### Часть 1. Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора

#### Часть 2. Ручная настройка IPv6-адресов

#### Часть 3. Проверка сквозного соединения

### Необходимые ресурсы
•	1 Маршрутизатор (Cisco 4221 с универсальным образом Cisco IOS XE версии 16.9.4 или аналогичным)

•	1 коммутатор (Cisco 2960 с ПО Cisco IOS версии 15.2(2) с образом lanbasek9 или аналогичная модель)

•	2 ПК (Windows и программа эмуляции терминала, такая как Tera Term)

•	Консольные кабели для настройки устройств Cisco IOS через консольные порты.

•	Кабели Ethernet, расположенные в соответствии с топологией


## Решение


#### Часть 1. Настройка топологии и конфигурация основных параметров маршрутизатора и коммутатора.

##### Шаг 1. Построение сети согласно топологии инициализация и перезагрузка маршрутизатора и коммутатора.
Консольным кабелем подключаемся к коммутатору и маршрутизатору.
        Router>enable 
        Router#erase startup-config
        Router#reload 
        
##### Шаг 2. Настройка маршрутизатора.
    Router>enable
    Router#conf t
    Router(config)#hostname R1 
    R1(config)# enable secret cisco          
    R1(config)# service password-encryption  
    R1(config)#banner motd # FEELGOOD66#
    R1(config)#no ip domain-lookup
    R1(config)#ipv6 unicast-routing 
    R1(config)#line con 0
    R1(config-line)#password cisco
    R1(config-line)#login
    R1(config-line)#logging synchronous 
    R1(config-line)#exit
    R1(config)#line vty 0 4
    R1(config-line)#password cisco
    R1(config-line)#login
    R1(config-line)#transport input telnet 
    R1(config-line)#exec-timeout 5
    R1#copy running-config startup-config 
        

        
    
##### Шаг 3. Настройка коммутатора.
    Switch>enable
    Switch#conf t
    Switch(config)#hostname S1 
    S1(config)# enable secret class          
    S1(config)# service password-encryption  
    S1(config)#banner motd # FEELGOOD66#
    S1(config)#no ip domain-lookup
    S1(config)#line con 0
    S1(config-line)#password class
    S1(config-line)#login
    S1(config-line)#logging synchronous 
    S1(config-line)#exit
    S1(config)#line vty 0 4
    S1(config-line)#password class
    S1(config-line)#login
    S1(config-line)#transport input telnet 
    S1(config-line)#exec-timeout 5
    S1#copy running-config startup-config 

        
### В следующих лабораторных работах базовые настройки будут пропускаться, тк базовые настройки считаю, что я выучил.



#### Часть 2. Ручная настройка IPv6-адресов.

##### Шаг 1. Назначьте IPv6-адреса интерфейсам Ethernet на R1.
Назначаем глобальные индивидуальные IPv6-адреса, указанные в таблице адресации обоим интерфейсам Ethernet на R1.

Для интерфейса G0/0/0

    R1(config)#int G0/0/0
    R1(config-if)#ipv6 address 2001:db8:acad:a::1/64

Для интерфейса G0/0/1
    R1(config)#int G0/0/1
    R1(config-if)#ipv6 address 2001:db8:acad:1::1/64

Проверяем назначения IPv6 на интерфейсах

    R1#show ipv6 interface brief 
    GigabitEthernet0/0/0       [administratively down/down]
        FE80::201:96FF:FE6B:9A01
        2001:DB8:ACAD:A::1
    GigabitEthernet0/0/1       [administratively down/down]
        FE80::201:96FF:FE6B:9A02
        2001:DB8:ACAD:1::1
    GigabitEthernet0/0/2       [administratively down/down]
        unassigned
    Vlan1                      [administratively down/down]


Настройка локального IPv6 адреса на интерфейсах

Для интерфейса G0/0/0

    R1(config)#int G0/0/0
    R1(config-if)#ipv6 address 2001:db8:acad:a::1/64

Для интерфейса G0/0/1
    R1(config)#int G0/0/1
    R1(config-if)#ipv6 address 2001:db8:acad:1::1/64

Проверяем 

    R1#show ipv6 interface brief 
    GigabitEthernet0/0/0       [administratively down/down]
        FE80::1
        2001:DB8:ACAD:A::1
    GigabitEthernet0/0/1       [administratively down/down]
        FE80::1
        2001:DB8:ACAD:1::1
    GigabitEthernet0/0/2       [administratively down/down]
    unassigned
    Vlan1                      [administratively down/down]


Какие группы многоадресной рассылки назначены интерфейсу G0/0?

    R1#show  ipv6 interface g0/0/0
      Joined group address(es):
        FF02::1

##### Шаг 2. Активируйте IPv6-маршрутизацию на R1.

В командной строке на PC-B вводим команду ipconfig и видим, что IPv6 не назначен

На этом этапе есть трудности, забыл ввести команду no shutdown на интерфейчах R1, также сразу ввел IPv6 unicast-routing, после этой команды линки появились

<img width="743" height="155" alt="image" src="https://github.com/user-attachments/assets/031d93a2-66d7-4396-9d8a-a45217b4ca4c" />

но до этого был локальный IPv6 адрес , а сейчас нет ,по идее по SLAAC настройки доложны были назначится(как я понял), но не назначились.
    FastEthernet0 Connection:(default port)

    Connection-specific DNS Suffix..: 
    Link-local IPv6 Address.........: ::
    IPv6 Address....................: ::
    IPv4 Address....................: 0.0.0.0
    Subnet Mask.....................: 0.0.0.0
    Default Gateway.................: ::
                                     0.0.0.0



Вроде понял в чем проблема, автоматическая настройка на ПК не была включена, после включения мы получаем такой вывод

    Connection-specific DNS Suffix..: 
    Link-local IPv6 Address.........: FE80::20C:85FF:FEA4:51B1
    IPv6 Address....................: 2001:DB8:ACAD:A:20C:85FF:FEA4:51B1
    IPv4 Address....................: 0.0.0.0
    Subnet Mask.....................: 0.0.0.0
    Default Gateway.................: FE80::1
                                     0.0.0.0





Почему PC-B получил глобальный префикс маршрутизации и идентификатор подсети, которые вы настроили на R1?
Я думаю, что когда ПК получает RA-пакет, в котором уже есть данные сети маршрутизатора и префикс, ну и чтобы дать ответ обратно, ему нужно быть в этой же сети.


##### Шаг 3. Назначьте IPv6-адреса интерфейсу управления (SVI) на S1.

    S1(config)# interface vlan 1
    S1(config-if)# ipv6 address 2001:db8:acad:1::b/64
    S1(config-if)# ipv6 address fe80::b link-local
    S1(config-if)# no shutdown


##### Шаг 4. Назначьте компьютерам статические IPv6-адреса.

Захожу в IP configuration на ПК

Пример атоматической настройки 

<img width="672" height="170" alt="image" src="https://github.com/user-attachments/assets/e3ea575d-d030-47b5-a622-774a2e5c306c" />

Ставим галочку на Static и вписываем настройки из таблицы адресации вручную.


#### Часть 3. Проверка сквозного подключения.


Отправляем эхо запрос на FE80::1

    C:\>ping FE80::1

    Pinging FE80::1 with 32 bytes of data:

    Reply from FE80::1: bytes=32 time<1ms TTL=255
    Reply from FE80::1: bytes=32 time<1ms TTL=255
    Reply from FE80::1: bytes=32 time<1ms TTL=255
    Reply from FE80::1: bytes=32 time=4ms TTL=255

    Ping statistics for FE80::1:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 4ms, Average = 1ms



Отправляем эхо-запрос на интерфейс управления S1 с PC-A.

    C:\>ping 2001:db8:acad:1::b

    Pinging 2001:db8:acad:1::b with 32 bytes of data:

    Reply from 2001:DB8:ACAD:1::B: bytes=32 time<1ms TTL=255
    Reply from 2001:DB8:ACAD:1::B: bytes=32 time<1ms TTL=255
    Reply from 2001:DB8:ACAD:1::B: bytes=32 time<1ms TTL=255
    Reply from 2001:DB8:ACAD:1::B: bytes=32 time<1ms TTL=255

    Ping statistics for 2001:DB8:ACAD:1::B:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 0ms, Average = 0ms



Вводим команду tracert на PC-A, чтобы проверить наличие сквозного подключения к PC-B.


    C:\>tracert 2001:db8:acad:a::3

    Tracing route to 2001:db8:acad:a::3 over a maximum of 30 hops: 

      1   0 ms      0 ms      0 ms      2001:DB8:ACAD:1::1
      2   0 ms      0 ms      0 ms      2001:DB8:ACAD:A::3

    Trace complete.

С PC-B отправьте эхо-запрос на PC-A.

    C:\>ping 2001:db8:acad:1::3

    Pinging 2001:db8:acad:1::3 with 32 bytes of data:

    Reply from 2001:DB8:ACAD:1::3: bytes=32 time<1ms TTL=127
    Reply from 2001:DB8:ACAD:1::3: bytes=32 time<1ms TTL=127
    Reply from 2001:DB8:ACAD:1::3: bytes=32 time<1ms TTL=127
    Reply from 2001:DB8:ACAD:1::3: bytes=32 time<1ms TTL=127

    Ping statistics for 2001:DB8:ACAD:1::3:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 0ms, Average = 0ms


С PC-B отправьте эхо-запрос на локальный адрес канала G0/0 на R1

    C:\>ping fe80::1

    Pinging fe80::1 with 32 bytes of data:

    Reply from FE80::1: bytes=32 time<1ms TTL=255
    Reply from FE80::1: bytes=32 time<1ms TTL=255
    Reply from FE80::1: bytes=32 time=5ms TTL=255
    Reply from FE80::1: bytes=32 time<1ms TTL=255

    Ping statistics for FE80::1:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 5ms, Average = 1ms


#### Вопросы для повторения.

Почему обоим интерфейсам Ethernet на R1 можно назначить один и тот же локальный адрес канала — FE80::1?

Я думаю, потому что это по сути две разных локальных сети, поэтому можно назначить один и тот же адрес.

Какой идентификатор подсети в индивидуальном IPv6-адресе 2001:db8:acad::aaaa:1234/64?

В адресе IPv6 содержится 8 хекстетов. 

**2001:0db8:acad:0000(Сеть)**:0000:0000:aaaa:1234(хосты)

#### Выгрузка из CPT [здесь](https://github.com/feelgood6/labs/blob/172bdb762d4d6aa666f3e9bdb160daaadc76fc76/labs/labs4/labs4.pkt)
