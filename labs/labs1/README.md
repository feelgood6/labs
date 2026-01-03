
# Лабораторная работа №1. Базовая настройка коммутатора.


###  Задание:

  1. Проверка конфигурации коммутатора по умолчанию
  2. Создание сети и настройка основных параметров устройства
  3. Проверка сетевых подключений

### Таблица адресации.

| Устройство|       Интерфейс          |     IP-адрес / префикс         |
|-----------|--------------------------|--------------------------------|
|    S1     |         VLAN 1           |      192.168.1.2               |
|           |                          |      255.255.255.0             |
|    PC-A   |           NIC            |      192.168.1.10              |
|           |                          |      255.255.255.0             |

### Часть 1. Создание сети и проверка настроек коммутатора по умолчанию

  #### 1. Берем коммутатор Cisco 2960 и ПК, соеденяем их консольным кабелем. Почему нельзя подключиться к коммутатору через Telnet или SSH? Потому что для этого нужно настроить vty-линии в настройках коммутатора, для этого нужно подключаться напрямую через консоль.
  
  
  #### 2. Входим в привилегированный режим и открываем в файл конфигурации.
    enable
    show running-config
  
  
  ##### Изучив файл файл конфигурации видим:
   -Интерфейсов FastEthernet - 24 шт,
  
  -Интерфейсов Gigabit Ethernet - 2 шт, 
  
  -Два диапазона значений, отображаемых в vty-линиях( от 0 до 4 и от 5 до 15)
  
  ##### Просматриваем startup configuration
    show startup configuration
  Появляется сообщение startup configuration is not present, появляется оно из-за того, что не записан running-config в startup configuration.

### Часть 2. Настройка базовых параметров сетевых устройств.

 #### 1. Настройка базовых параметров коммутатора.
 
    enable
    conf t
    no ip domain-lookup
    hostname S1
    service password-encryption
    enable secret class
    banner motd #
    Unauthorized access is strictly prohibited. #
    line con 0
    passwor class
    logging synchronous 
    login


##### Этими командами мы:

- Убираем автоматический поиск доменных имен по IP-адресам
- Задаем имя нашему коммутатору
- включаем шифрование паролей в конфигурации устройства
- Задаем зашифрованный пароль, который используется для входа в привилегированный режим.
- Устанавливаем баннер, который виден при входе на коммутатор
- Устанавливаем пароль на консольный вход в коммутатор, задаем login, без него не будет запрашивать пароль

 #### 2. Настройка vty - линий.
 Так как мы находимся в режиме глобальной конфигурации, от сюда вводим такие команды. Настраивать буду с 0 по 4 линию.
 
    line vty 0 4
    password class
    login
    transport input telnet

#### 3. Настройка IP адрессов на коммутаторе и компьютере.

 ##### Настройка IP на компьютере.
 PC-PT PC0(Нажимаетм на компьютер) > Desctop > IP Configuration

 Задаем адрес 192.168.1.10 в строке IPv4 Addrres и маску 255.255.255.0 в строке Subnet Mask

 ##### Настройка IP на коммутаторе.
 Все также через терминал на компьюторе заходим на коммутатор.
     
     enable
     *вводим пароль
     conf t
     interface vlan 1
     ip address 192.168.1.2 255.255.255.0
     no shutdown


### Часть 3. Проверка сетевых подключений.

 #### 1. Конфигурацию коммутатора.

 ##### Вывод конфигурации running-config.  
    
    S1# show run
    Building configuration...

    Current configuration : 2206 bytes
    !
    version 15.2
    no service pad
    service timestamps debug datetime msec
    service timestamps log datetime msec
    service password-encryption
    !
    hostname S1
    !
    boot-start-marker
    boot-end-marker
    !
    enable secret 5 $1$mtvC$6NC.1VKr3p6bj7YGE.jNg0
    !
    no aaa new-model
    system mtu routing 1500 
    !
    !
    no ip domain-lookup
    !
    <output omitted>
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
    ip default-gateway 192.168.1.1
    ip http server
    ip http secure-server
    !
    banner motd ^C
    Unauthorized access is strictly prohibited. ^C
    !
    line con 0
    password 7 00071A150754
    logging synchronous
    login
    line vty 0 4
    password 7 121A0C041104
    login
    line vty 5 15
    password 7 121A0C041104
    login
    !
    end


  ##### Проверка параметров VLAN 1. 
  
      S1# show interface vlan 1 

  Полоса пропускания
BW 100000 Kbit

   #### 2. Протестируем сквозное соединение, отправив эхо-запрос.

   ##### В командной строке компьютера PC-A с помощью утилиты ping проверяем связь с адресом PC-A.

    C:\>ping 192.168.1.10
    Pinging 192.168.1.10 with 32 bytes of data:

    Reply from 192.168.1.10: bytes=32 time<1ms TTL=128
    Reply from 192.168.1.10: bytes=32 time<1ms TTL=128
    Reply from 192.168.1.10: bytes=32 time<1ms TTL=128
    Reply from 192.168.1.10: bytes=32 time<1ms TTL=128

    Ping statistics for 192.168.1.10:
      Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
      Minimum = 0ms, Maximum = 0ms, Average = 0ms 

   ##### Из командной строки компьютера PC-A отправляем эхо-запрос на административный адрес интерфейса SVI коммутатора S1.  
   
     C:\>ping 192.168.1.2

     Pinging 192.168.1.2 with 32 bytes of data:

     Reply from 192.168.1.2: bytes=32 time<1ms TTL=255
     Reply from 192.168.1.2: bytes=32 time<1ms TTL=255
     Reply from 192.168.1.2: bytes=32 time<1ms TTL=255
     Reply from 192.168.1.2: bytes=32 time<1ms TTL=255

     Ping statistics for 192.168.1.2:
         Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
     Approximate round trip times in milli-seconds:
         Minimum = 0ms, Maximum = 0ms, Average = 0ms


   #### 3. Проверяем удаленное управление коммутатором S1.
1. Заходим в командную строку на ПК.
2. Вводим следующую команду в командную строку

      telnet 192.168.1.2
  
3. Вводим пароль class.
4. Сохраняем running-config в startup-config.
   
   Вывод из консоли:
   

     C:\>telnet 192.168.1.2
     Trying 192.168.1.2 ...OpenUnauthorized access is strictly prohibited. 


     User Access Verification

     Password: 
     Password: 
     S1>ena
     S1>enable 
     Password: 
     S1#
     S1#copy r
     S1#copy running-config st
     S1#copy running-config startup-config 
     Destination filename [startup-config]? 
     Building configuration...
     [OK]
   ### Вопросы для повторения.

1.	Зачем необходимо настраивать пароль VTY для коммутатора?
- Без настройки пароля не будет доступа подключения по vty-линиям к коммутатору.
2.	Что нужно сделать, чтобы пароли не отправлялись в незашифрованном виде?
- Нам в этом поможет настройка service password-encryption, которая предотвращает просмотр паролей в открытом виде при просмотре конфигурации устройства.



#### Выгрузка из CPT перед инициализацией и перезагрузкой здесь.



### Инициализация и перезагрузка коммутатора.


