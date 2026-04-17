
# Лабораторная работа - Конфигурация безопасности коммутатора

## Топология

<img width="641" height="356" alt="image" src="https://github.com/user-attachments/assets/124ed84e-39ec-45e7-abb3-f75b4aa87b48" />

## Таблица адресации

<img width="700" height="201" alt="image" src="https://github.com/user-attachments/assets/e83b5d90-df70-4638-a4fe-94e7e78ba902" />


## Задачи 

### Часть 1. Настройка основного сетевого устройства
•	Создайте сеть.

•	Настройте маршрутизатор R1.

•	Настройка и проверка основных параметров коммутатора

### Часть 2. Настройка сетей VLAN
•	Сконфигруриуйте VLAN 10.

•	Сконфигруриуйте SVI для VLAN 10.

•	Настройте VLAN 333 с именем Native на S1 и S2.

•	Настройте VLAN 999 с именем ParkingLot на S1 и S2.

### Часть 3: Настройки безопасности коммутатора.
•	Реализация магистральных соединений 802.1Q.

•	Настройка портов доступа

•	Безопасность неиспользуемых портов коммутатора

•	Документирование и реализация функций безопасности порта.

•	Реализовать безопасность DHCP snooping .

•	Реализация PortFast и BPDU Guard

•	Проверка сквозной связанности.



## Решение

### Часть 1. Настройка основного сетевого устройства

##### Шаг 1. Создайте сеть.

##### Шаг 2. Настройте маршрутизатор R1

a.	Вписывал все руками
    
    
    enable
    configure terminal
    hostname R1
    no ip domain lookup
    ip dhcp excluded-address 192.168.10.1 192.168.10.9
    ip dhcp excluded-address 192.168.10.201 192.168.10.202
    ip dhcp relay information trust-all
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
     ip address 192.168.10.1 255.255.255.0
     no shutdown
    !
    line con 0
     logging synchronous
     exec-timeout 0 0
 
b.	Проверьте текущую конфигурацию на R1, используя следующую команду:
R1# show ip interface brief
c.	Убедитесь, что IP-адресация и интерфейсы находятся в состоянии up / up (при необходимости устраните неполадки).
    R1#show ip interface brief
    
    Interface              IP-Address      OK? Method Status                Protocol 
    GigabitEthernet0/0/0   unassigned      YES unset  administratively down down 
    GigabitEthernet0/0/1   192.168.10.1    YES manual up                    up 
    GigabitEthernet0/0/2   unassigned      YES unset  administratively down down 
    Loopback0              10.10.1.1       YES manual up                    up 
    Vlan1                  unassigned      YES unset  administratively down down


##### Шаг 3. Настройка и проверка основных параметров коммутатора

a.	Настройте имя хоста для коммутаторов S1 и S2.
b.	Запретите нежелательный поиск в DNS.
c.	Настройте описания интерфейса для портов, которые используются в S1 и S2.
d.	Установите для шлюза по умолчанию для VLAN управления значение 192.168.10.1 на обоих коммутаторах.


### Часть 2. Настройка сетей VLAN на коммутаторах.

##### Шаг 1. Сконфигруриуйте VLAN 10.

    S1(config)#vlan 10
    S1(config-vlan)#name Management


##### Шаг 2. Сконфигруриуйте SVI для VLAN 10.

    S1#conf t
    S1(config)#int vlan 10
    S1(config-if)#ip address 192.168.10.202 255.255.255.0
    S1(config-if)no shutdown
    S1(config-if)description Management SVI

##### Шаг 3. Настройте VLAN 333 с именем Native на S1 и S2.

    S1(config)#vlan 333
    S1(config-vlan)#name Native

##### Шаг 4. Настройте VLAN 999 с именем ParkingLot на S1 и S2.

    S2(config)#vlan 999
    S2(config-vlan)#name ParkingLot




### Часть 3. Настройки безопасности коммутатора.


##### Шаг 1. Релизация магистральных соединений 802.1Q.

a.	Настройте все магистральные порты Fa0/1 на обоих коммутаторах для использования VLAN 333 в качестве native VLAN.


    S1(config)#int f0/1
    S1(config-if)#sw
    S1(config-if)#switchport mode trunk
    S1(config-if)#switchport trunk native vlan 333
    S1(config-if)#switchport trunk allowed vlan 1,10,333,999


    S1#show interface trunk
    Port        Mode         Encapsulation  Status        Native vlan
    Fa0/1       on           802.1q         trunking      333

    Port        Vlans allowed on trunk
    Fa0/1       1,10,333,999

    Port        Vlans allowed and active in management domain
    Fa0/1       1,10,333,999

    Port        Vlans in spanning tree forwarding state and not pruned
    Fa0/1       1,10,333,999


c.	Отключить согласование DTP F0/1 на S1 и S2. 

    S1(config)#int f0/1
    S1(config-if)#switchport nonegotiate

d.	Проверьте с помощью команды show interfaces.

    S1#show interfaces f0/1 switchport | include Negotiation
    Negotiation of Trunking: Off

    S2#show interfaces f0/1 switchport | include Negotiation
    Negotiation of Trunking: Off

    
##### Шаг 2. Настройка портов доступа


a.	На S1 настройте F0/5 и F0/6 в качестве портов доступа и свяжите их с VLAN 10.

    S1(config)#int range f0/5-6
    S1(config-if-range)#switchport mode access 
    S1(config-if-range)#switchport access vlan 10

b.	На S2 настройте порт доступа Fa0/18 и свяжите его с VLAN 10.
  
    S2(config)#int f0/18
    S2(config-if)#switchport mode access 
    S2(config-if)#switchport access vlan 10



##### Шаг 3. Безопасность неиспользуемых портов коммутатора


a.	На S1 и S2 переместите неиспользуемые порты из VLAN 1 в VLAN 999 и отключите неиспользуемые порты.

    S1(config)#int range f0/2-4,f0/7-24,g0/1-2
    S1(config-if-range)#switchport access vlan 999
    S1(config-if-range)#shutdown 




    S2(config)#int range f0/2-17,f0/19-24,g0/1-2
    S2(config-if-range)#switchport access vlan 999
    S2(config-if-range)#shutdown 

b.	Убедитесь, что неиспользуемые порты отключены и связаны с VLAN 999

    S1#show interfaces status
    Port      Name               Status       Vlan       Duplex  Speed Type
    Fa0/1                        connected    trunk      auto    auto  10/100BaseTX
    Fa0/2                        disabled 999        auto    auto  10/100BaseTX
    Fa0/3                        disabled 999        auto    auto  10/100BaseTX
    Fa0/4                        disabled 999        auto    auto  10/100BaseTX
    Fa0/5                        connected    10         auto    auto  10/100BaseTX
    Fa0/6                        connected    10         auto    auto  10/100BaseTX
    Fa0/7                        disabled 999        auto    auto  10/100BaseTX
    Fa0/8                        disabled 999        auto    auto  10/100BaseTX
    Fa0/9                        disabled 999        auto    auto  10/100BaseTX
    Fa0/10                       disabled 999        auto    auto  10/100BaseTX
    Fa0/11                       disabled 999        auto    auto  10/100BaseTX
    Fa0/12                       disabled 999        auto    auto  10/100BaseTX
    Fa0/13                       disabled 999        auto    auto  10/100BaseTX
    Fa0/14                       disabled 999        auto    auto  10/100BaseTX
    Fa0/15                       disabled 999        auto    auto  10/100BaseTX
    Fa0/16                       disabled 999        auto    auto  10/100BaseTX
    Fa0/17                       disabled 999        auto    auto  10/100BaseTX
    Fa0/18                       disabled 999        auto    auto  10/100BaseTX
    Fa0/19                       disabled 999        auto    auto  10/100BaseTX
    Fa0/20                       disabled 999        auto    auto  10/100BaseTX
    Fa0/21                       disabled 999        auto    auto  10/100BaseTX
    Fa0/22                       disabled 999        auto    auto  10/100BaseTX
    Fa0/23                       disabled 999        auto    auto  10/100BaseTX
    Fa0/24                       disabled 999        auto    auto  10/100BaseTX
    Gig0/1                       disabled 999        auto    auto  10/100BaseTX
    Gig0/2                       disabled 999        auto    auto  10/100BaseTX



    S2#show interfaces status
    Port      Name               Status       Vlan       Duplex  Speed Type
    Fa0/1                        connected    trunk      auto    auto  10/100BaseTX
    Fa0/2                        disabled 999        auto    auto  10/100BaseTX
    Fa0/3                        disabled 999        auto    auto  10/100BaseTX
    Fa0/4                        disabled 999        auto    auto  10/100BaseTX
    Fa0/5                        disabled 999        auto    auto  10/100BaseTX
    Fa0/6                        disabled 999        auto    auto  10/100BaseTX
    Fa0/7                        disabled 999        auto    auto  10/100BaseTX
    Fa0/8                        disabled 999        auto    auto  10/100BaseTX
    Fa0/9                        disabled 999        auto    auto  10/100BaseTX
    Fa0/10                       disabled 999        auto    auto  10/100BaseTX
    Fa0/11                       disabled 999        auto    auto  10/100BaseTX
    Fa0/12                       disabled 999        auto    auto  10/100BaseTX
    Fa0/13                       disabled 999        auto    auto  10/100BaseTX
    Fa0/14                       disabled 999        auto    auto  10/100BaseTX
    Fa0/15                       disabled 999        auto    auto  10/100BaseTX
    Fa0/16                       disabled 999        auto    auto  10/100BaseTX
    Fa0/17                       disabled 999        auto    auto  10/100BaseTX
    Fa0/18                       connected    10         auto    auto  10/100BaseTX
    Fa0/19                       disabled 999        auto    auto  10/100BaseTX
    Fa0/20                       disabled 999        auto    auto  10/100BaseTX
    Fa0/21                       disabled 999        auto    auto  10/100BaseTX
    Fa0/22                       disabled 999        auto    auto  10/100BaseTX
    Fa0/23                       disabled 999        auto    auto  10/100BaseTX
    Fa0/24                       disabled 999        auto    auto  10/100BaseTX
    Gig0/1                       disabled 999        auto    auto  10/100BaseTX
    Gig0/2                       disabled 999        auto    auto  10/100BaseTX



##### Шаг 4. Документирование и реализация функций безопасности порта.

a.	На S1, введите команду show port-security interface f0/6  для отображения настроек по умолчанию безопасности порта для интерфейса F0/6. Запишите свои ответы ниже.

<img width="685" height="312" alt="image" src="https://github.com/user-attachments/assets/1f41cd7d-3aaf-4e88-aa8f-3a1053193bcb" />

b.	На S1 включите защиту порта на F0 / 6 со следующими настройками
o	Максимальное количество записей MAC-адресов: 3
o	Режим безопасности: restrict
o	Aging time: 60 мин.
o	Aging type: неактивный


    S1(config)#int f0/6
    S1(config-if)#switchport port-security 
    S1(config-if)#switchport port-security maximum 3
    S1(config-if)#switchport port-security violation restrict 
    S1(config-if)#switchport port-security aging time 60

    # switchport port-security aging type inactivity - эта команда не прописывается

c.	Verify port security on S1 F0/6.

    S1# show port-security interface f0/6
    Port Security              : Enabled
    Port Status                : Secure-up
    Violation Mode             : Restrict
    Aging Time                 : 60 mins
    Aging Type                 : Absolute
    SecureStatic Address Aging : Disabled
    Maximum MAC Addresses      : 3
    Total MAC Addresses        : 0
    Configured MAC Addresses   : 0
    Sticky MAC Addresses       : 0
    Last Source Address:Vlan   : 0000.0000.0000:0
    Security Violation Count   : 0

    S1#show port-security address
                   Secure Mac Address Table
    -----------------------------------------------------------------------------
    Vlan    Mac Address       Type                          Ports   Remaining Age
                                                                       (mins)
    ----    -----------       ----                          -----   -------------
    -----------------------------------------------------------------------------
    Total Addresses in System (excluding one mac per port)     : 0
    Max Addresses limit in System (excluding one mac per port) : 1024
    

d.	Включите безопасность порта для F0 / 18 на S2. Настройте каждый активный порт доступа таким образом, чтобы он автоматически добавлял адреса МАС, изученные на этом порту, в текущую конфигурацию.

    S2(config-if)#switchport port-security 
    S2(config-if)#switchport port-security mac-address sticky 

e.	Настройте следующие параметры безопасности порта на S2 F / 18:
o	Максимальное количество записей MAC-адресов: 2
o	Тип безопасности: Protect
o	Aging time: 60 мин.

    S2(config-if)#switchport port-security maximum 2
    S2(config-if)#switchport port-security violation protect 
    S2(config-if)#switchport port-security aging time 60

f.	Проверка функции безопасности портов на S2 F0/18.

    S2#show port-security interface f0/18
    Port Security              : Enabled
    Port Status                : Secure-up
    Violation Mode             : Protect
    Aging Time                 : 60 mins
    Aging Type                 : Absolute
    SecureStatic Address Aging : Disabled
    Maximum MAC Addresses      : 2
    Total MAC Addresses        : 0
    Configured MAC Addresses   : 0
    Sticky MAC Addresses       : 0
    Last Source Address:Vlan   : 0000.0000.0000:0
    Security Violation Count   : 0

    S2#show port-security address
                   Secure Mac Address Table
    -----------------------------------------------------------------------------
    Vlan    Mac Address       Type                          Ports   Remaining Age
                                                                       (mins)
    ----    -----------       ----                          -----   -------------
    -----------------------------------------------------------------------------
    Total Addresses in System (excluding one mac per port)     : 0
    Max Addresses limit in System (excluding one mac per port) : 1024


##### Шаг 5. Реализовать безопасность DHCP snooping.

a.	На S2 включите DHCP snooping и настройте DHCP snooping во VLAN 10.

    S2(config)#ip dhcp snooping 
    S2(config)#ip dhcp snooping vlan 10


b.	Настройте магистральные порты на S2 как доверенные порты.

    S2(config)#int f0/1
    S2(config-if)#ip dhcp snooping trust 


c.	Ограничьте ненадежный порт Fa0/18 на S2 пятью DHCP-пакетами в секунду.

    S2(config)#int f0/18
    S2(config-if)#ip dhcp snooping limit rate 5

d.	Проверка DHCP Snooping на S2.

    S2#show ip dhcp snooping
    Switch DHCP snooping is enabled
    DHCP snooping is configured on following VLANs:
    10
    Insertion of option 82 is enabled
    Option 82 on untrusted port is not allowed
    Verification of hwaddr field is enabled
    Interface                  Trusted    Rate limit (pps)
    -----------------------    -------    ----------------
    FastEthernet0/1            yes        unlimited       
    FastEthernet0/18           no         5  

e.	В командной строке на PC-B освободите, а затем обновите IP-адрес.

    C:\>ipconfig /release

       IP Address......................: 0.0.0.0
       Subnet Mask.....................: 0.0.0.0
       Default Gateway.................: 0.0.0.0
       DNS Server......................: 0.0.0.0

    C:\>ipconfig /renew

       IP Address......................: 192.168.10.10
       Subnet Mask.....................: 255.255.255.0
       Default Gateway.................: 192.168.10.1
       DNS Server......................: 0.0.0.0


f.	Проверьте привязку отслеживания DHCP с помощью команды show ip dhcp snooping binding.

    S2#show ip dhcp snooping
    Switch DHCP snooping is enabled
    DHCP snooping is configured on following VLANs:
    10
    Insertion of option 82 is enabled
    Option 82 on untrusted port is not allowed
    Verification of hwaddr field is enabled
    Interface                  Trusted    Rate limit (pps)
    -----------------------    -------    ----------------
    FastEthernet0/1            yes        unlimited       
    FastEthernet0/18           no         5               
    S2#show ip dhcp snooping binding 
    MacAddress          IpAddress        Lease(sec)  Type           VLAN  Interface
    ------------------  ---------------  ----------  -------------  ----  -----------------
    00:01:42:92:CE:1E   192.168.10.10    0           dhcp-snooping  10    FastEthernet0/18
    Total number of bindings: 1

##### Отключение 82 опции на двух коммутаторах

    S1(config)#no ip dhcp snooping information option
    
    S2(config)#no ip dhcp snooping information option


##### Шаг 6. Реализация PortFast и BPDU Guard


    S1(config)#int f0/6
    S1(config-if)#spanning-tree portfast 
    %Warning: portfast should only be enabled on ports connected to a single
    host. Connecting hubs, concentrators, switches, bridges, etc... to this
    interface  when portfast is enabled, can cause temporary bridging loops.
    Use with CAUTION

    %Portfast has been configured on FastEthernet0/6 but will only
    have effect when the interface is in a non-trunking mode.
    
    S1(config-if)#spanning-tree bpduguard enable 
  


    S2(config)#int f0/18
    S2(config-if)#spanning-tree portfast 
    %Warning: portfast should only be enabled on ports connected to a single
    host. Connecting hubs, concentrators, switches, bridges, etc... to this
    interface  when portfast is enabled, can cause temporary bridging loops.
    Use with CAUTION

    %Portfast has been configured on FastEthernet0/18 but will only
    have effect when the interface is in a non-trunking mode.

    S2(config-if)#spanning-tree bpduguard enable 


Убедитесь, что защита BPDU и PortFast включены на соответствующих портах. 

    S1#show spanning-tree int f0/6 detail 



    Port 6 (FastEthernet0/6) of VLAN0010 is designated forwarding
      Port path cost 19, Port priority 128, Port Identifier 128.6
      Designated root has priority 32778, address 000D.BD84.7AC4
      Designated bridge has priority 32778, address 00D0.BCC9.844D
      Designated port id is 128.6, designated path cost 19
      Timers: message age 16, forward delay 0, hold 0
      Number of transitions to forwarding state: 1
      The port is in the portfast mode
      Link type is point-to-point by default

Про Bpdu guard is enabled не пишет, несколько раз прописал команду, так и должно быть в CPT?

##### Шаг 7. Проверьте наличие сквозного ⁪подключения.

На PC-A (192.168.10.11) пингуем R1 (192.168.10.1)

    C:\>ping 192.168.10.1

    Pinging 192.168.10.1 with 32 bytes of data:

    Reply from 192.168.10.1: bytes=32 time<1ms TTL=255
    Reply from 192.168.10.1: bytes=32 time<1ms TTL=255
    Reply from 192.168.10.1: bytes=32 time<1ms TTL=255
    Reply from 192.168.10.1: bytes=32 time<1ms TTL=255

    Ping statistics for 192.168.10.1:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 0ms, Average = 0ms


На PC-A (192.168.10.11) пингуем PC-B (192.168.10.10)

    C:\>ping 192.168.10.10

    Pinging 192.168.10.10 with 32 bytes of data:

    Reply from 192.168.10.10: bytes=32 time<1ms TTL=128
    Reply from 192.168.10.10: bytes=32 time<1ms TTL=128
    Reply from 192.168.10.10: bytes=32 time<1ms TTL=128
    Reply from 192.168.10.10: bytes=32 time<1ms TTL=128

    Ping statistics for 192.168.10.10:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 0ms, Average = 0ms

    
На PC-A (192.168.10.11) пингуем S1 (192.168.10.201)


    C:\>ping 192.168.10.201

    Pinging 192.168.10.201 with 32 bytes of data:

    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 192.168.10.201:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

Не дает пингануть, хотя S2(192.168.10.202) видит

    C:\>ping 192.168.10.202

    Pinging 192.168.10.202 with 32 bytes of data:

    Request timed out.
    Reply from 192.168.10.202: bytes=32 time<1ms TTL=255
    Reply from 192.168.10.202: bytes=32 time<1ms TTL=255
    Reply from 192.168.10.202: bytes=32 time=5ms TTL=255

    Ping statistics for 192.168.10.202:
        Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 5ms, Average = 1ms



<img width="1082" height="441" alt="image" src="https://github.com/user-attachments/assets/a4be8464-0d72-4067-8920-8bf535ecae5a" />

Одинаковый IP на Vlan 10 у двух коммутаторах

Исправляем

    S1(config)#int vlan 10
    S1(config-if)#ip address 192.168.10.201 255.255.255.0

Проверяем

    C:\>ping 192.168.10.201

    Pinging 192.168.10.201 with 32 bytes of data:

    Reply from 192.168.10.201: bytes=32 time<1ms TTL=255
    Reply from 192.168.10.201: bytes=32 time<1ms TTL=255
    Reply from 192.168.10.201: bytes=32 time<1ms TTL=255
    Reply from 192.168.10.201: bytes=32 time<1ms TTL=255

    Ping statistics for 192.168.10.201:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 0ms, Average = 0ms

### Вопросы для повторения

1.	С точки зрения безопасности порта на S2, почему нет значения таймера для оставшегося возраста в минутах, когда было сконфигурировано динамическое обучение - sticky?

В этом случае, адрес записывается в running-config, он по сути становится статическим, так что не стареет.

2.	Что касается безопасности порта на S2, если вы загружаете скрипт текущей конфигурации на S2, почему порту 18 на PC-B никогда не получит IP-адрес через DHCP?

Потому что порт Fa0/18 настроен как недоверенный для DHCP snooping.
После загрузки конфигурации коммутатор не имеет записи о привязке IP и MAC, поэтому DHCP-пакеты блокируются.

3.	Что касается безопасности порта, в чем разница между типом абсолютного устаревания и типом устаревание по неактивности?

Абсолютное устаревание - MAC удаляется в любом случае, когда заканчивается установленное время.
По неактивности - MAC удаляется только, если устройство не активно в течении заданного времени.

Выгруска из CPT (Здесь){}
