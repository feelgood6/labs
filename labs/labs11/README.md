
# Лабораторная работа №11. Настройка и проверка расширенных списков контроля доступа.

## Таблица адресации

<img width="869" height="433" alt="image" src="https://github.com/user-attachments/assets/5a1d9028-c43e-4e51-8ba0-01754afb5b99" />

## Таблица VLAN

<img width="874" height="236" alt="image" src="https://github.com/user-attachments/assets/7fc82ff1-e654-4ebc-9c37-5ddb35e46125" />


## Инструкции

### Часть 1. Создание сети и настройка основных параметров устройства

##### Шаг 1. Создайте сеть согласно топологии.

##### Шаг 2. Произведите базовую настройку маршрутизаторов.

##### Шаг 3. Настройте базовые параметры каждого коммутатора.

### Часть 2. Настройка сетей VLAN на коммутаторах.

##### Шаг 1. Создайте сети VLAN на коммутаторах.

a.	Создайте необходимые VLAN и назовите их на каждом коммутаторе из приведенной выше таблицы.

На S1

    S1(config)#vlan 20
    S1(config-vlan)#name Management

    S1(config)#vlan 30
    S1(config-vlan)#name Operations

    S1(config)#vlan 40
    S1(config-vlan)#name Sales

    S1(config)#vlan 999
    S1(config-vlan)#name ParkingLot

    S1(config)#vlan 1000
    S1(config-vlan)#name MyVlan

На S2

    S2(config)#vlan 20
    S2(config-vlan)#name Management

    S2(config)#vlan 30
    S2(config-vlan)#name Operations

    S2(config)#vlan 40
    S2(config-vlan)#name Sales

    S2(config)#vlan 999
    S2(config-vlan)#name ParkingLot

    S2(config)#vlan 1000
    S2(config-vlan)#name MyVlan

b.	Настройте интерфейс управления и шлюз по умолчанию на каждом коммутаторе, используя информацию об IP-адресе в таблице адресации. 

На S1

    S1(config)#int vlan 20
    S1(config-if)#ip address 10.20.0.2 255.255.255.0
    S1(config-if)#no sh

    S1(config)#ip default-gateway 10.20.0.1

На S2

    S2(config)#int vlan 20
    S2(config-if)#ip address 10.20.0.3 255.255.255.0
    S2(config-if)#no sh

    S2(config)#ip default-gateway 10.20.0.1

c.	Назначьте все неиспользуемые порты коммутатора VLAN Parking Lot, настройте их для статического режима доступа и административно деактивируйте их.

На S1

    S1(config)#in range f0/2-4,f0/7-24,g0/1-2
    S1(config-if-range)#switchport mode access 
    S1(config-if-range)#switchport access vlan 999
    S1(config-if-range)#sh

На S2

    S2(config)#int range f0/2-4,f0/6-17,f0/19-24,g0/1-2
    S2(config-if-range)#switchport mode access 
    S2(config-if-range)#switchport access vlan 999
    S2(config-if-range)#sh


##### Шаг 2. Назначьте сети VLAN соответствующим интерфейсам коммутатора.

a.	Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа.

На S1

    S1(config)#int f0/6
    S1(config-if)#switchport mode access 
    S1(config-if)#switchport access vlan 30
    S1(config-if)#no sh

На S2

    S2(config)#int f0/5
    S2(config-if)#switchport mode access 
    S2(config-if)#switchport access vlan 20
    S2(config-if)#no sh

    S2(config)#int f0/18
    S2(config-if)#switchport mode access 
    S2(config-if)#switchport access vlan 40
    S2(config-if)#no sh

b.	Выполните команду show vlan brief, чтобы убедиться, что сети VLAN назначены правильным интерфейсам.

    S1#show vlan brief 

    VLAN Name                             Status    Ports
    ---- -------------------------------- --------- -------------------------------
    1    default                          active    Fa0/1, Fa0/5
    20   Management                       active    
    30   Operations                       active    Fa0/6
    40   Sales                            active    
    999  ParkingLot                       active    Fa0/2, Fa0/3, Fa0/4, Fa0/7
                                                    Fa0/8, Fa0/9, Fa0/10, Fa0/11
                                                    Fa0/12, Fa0/13, Fa0/14, Fa0/15
                                                    Fa0/16, Fa0/17, Fa0/18, Fa0/19
                                                    Fa0/20, Fa0/21, Fa0/22, Fa0/23
                                                    Fa0/24, Gig0/1, Gig0/2
    1000 MyVlan                           active    
    1002 fddi-default                     active    
    1003 token-ring-default               active    
    1004 fddinet-default                  active    
    1005 trnet-default                    active    

Fa0/1, Fa0/5 - будут транками, так что на данный момент они остались в vlan 1


На S2

    S2#show vlan brief 

    VLAN Name                             Status    Ports
    ---- -------------------------------- --------- -------------------------------
    1    default                          active    Fa0/1
    20   Management                       active    Fa0/5
    30   Operations                       active    
    40   Sales                            active    Fa0/18
    999  ParkingLot                       active    Fa0/2, Fa0/3, Fa0/4, Fa0/6
                                                    Fa0/7, Fa0/8, Fa0/9, Fa0/10
                                                    Fa0/11, Fa0/12, Fa0/13, Fa0/14
                                                    Fa0/15, Fa0/16, Fa0/17, Fa0/19
                                                    Fa0/20, Fa0/21, Fa0/22, Fa0/23
                                                    Fa0/24, Gig0/1, Gig0/2
    1000 MyVlan                           active    
    1002 fddi-default                     active    
    1003 token-ring-default               active    
    1004 fddinet-default                  active    
    1005 trnet-default                    active 

    Fa0/1, Fa0/5 - Тоже будут транками



### Часть 3. ·Настройте транки (магистральные каналы).

##### Шаг 1. Вручную настройте магистральный интерфейс F0/1.

a.	Измените режим порта коммутатора на интерфейсе F0/1, чтобы принудительно создать магистральную связь. Не забудьте сделать это на обоих коммутаторах.

b.	В рамках конфигурации транка установите для native vlan значение 1000 на обоих коммутаторах. При настройке двух интерфейсов для разных собственных VLAN сообщения об ошибках могут отображаться временно.

c.	В качестве другой части конфигурации транка укажите, что VLAN 20, 30, 40 и 1000 разрешены в транке.

На S1 и S2 настройки одинаковые

    S1(config)#int f0/1
    S1(config-if)#switchport mode trunk 
    S1(config-if)#switchport trunk native vlan 1000
    S1(config-if)#switchport trunk allowed vlan 20,30,40,1000

d.	Выполните команду show interfaces trunk для проверки портов магистрали, собственной VLAN и разрешенных VLAN через магистраль.

Вывод на S1 и S2 одинаковые

    S2#show int trunk 
    Port        Mode         Encapsulation  Status        Native vlan
    Fa0/1       on           802.1q         trunking      1000

    Port        Vlans allowed on trunk
    Fa0/1       20,30,40,1000

    Port        Vlans allowed and active in management domain
    Fa0/1       20,30,40,1000

    Port        Vlans in spanning tree forwarding state and not pruned
    Fa0/1       20,30,40,1000


##### Шаг 2. Вручную настройте магистральный интерфейс F0/5 на коммутаторе S1.

a.	Настройте интерфейс S1 F0/5 с теми же параметрами транка, что и F0/1. Это транк до маршрутизатора.
b.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.
c.	Используйте команду show interfaces trunk для проверки настроек транка.

    S1(config)#int f0/5
    S1(config-if)#switchport mode trunk 
    S1(config-if)#switchport trunk native vlan 1000
    S1(config-if)#switchport trunk allowed vlan 20,30,40,1000
    S1(config-if)#no sh
    S1#show int trunk 
    Port        Mode         Encapsulation  Status        Native vlan
    Fa0/1       on           802.1q         trunking      1000

    Port        Vlans allowed on trunk
    Fa0/1       20,30,40,1000

    Port        Vlans allowed and active in management domain
    Fa0/1       20,30,40,1000

    Port        Vlans in spanning tree forwarding state and not pruned
    Fa0/1       20,30,40,1000

Транк не поднялся, тк нет настроек на обратной стороне(то есть со стороны роутера).



### Часть 4. Настройте маршрутизацию.

##### Шаг 1. Настройка маршрутизации между сетями VLAN на R1.
a.	Активируйте интерфейс G0/0/1 на маршрутизаторе.

b.	Настройте подинтерфейсы для каждой VLAN, как указано в таблице IP-адресации. Все подинтерфейсы используют инкапсуляцию 802.1Q. Убедитесь, что подинтерфейс для собственной VLAN не имеет назначенного IP-адреса. Включите описание для каждого подинтерфейса.

c.	Настройте интерфейс Loopback 1 на R1 с адресацией из приведенной выше таблицы.

d.	С помощью команды show ip interface brief проверьте конфигурацию подынтерфейса.

    R1(config)#int g0/0/1
    R1(config-if)#no sh
  
    R1(config)#int g0/0/1.20
    R1(config-subif)#encapsulation dot1Q 20
    R1(config-subif)#ip address 10.20.0.1 255.255.255.0
  
    R1(config)#int g0/0/1.30
    R1(config-subif)#encapsulation dot1Q 30
    R1(config-subif)#ip address 10.30.0.1 255.255.255.0

    R1(config)#int g0/0/1.40
    R1(config-subif)#encapsulation dot1Q 40
    R1(config-subif)#ip address 10.40.0.1 255.255.255.0

    R1(config)#int g0/0/1.1000
    R1(config-subif)#encapsulation dot1Q 1000

  

  


##### Шаг 2. Настройка интерфейса R2 g0/0/1 с использованием адреса из таблицы и маршрута по умолчанию с адресом следующего перехода 10.20.0.1


    R2(config)#int g0/0/1
    R2(config-if)#ip address 10.20.0.4 255.255.255.0
    R2(config-if)#ip route 0.0.0.0 0.0.0.0 10.20.0.1

### Часть 5. Настройте удаленный доступ

##### Шаг 1. Настройте все сетевые устройства для базовой поддержки SSH.

Настройки на каждом устройстве одинаковые

    R1#conf t
    R1(config)#ip domain-name ccna-lab.com 
    R1(config)#crypto key generate rsa 
    The name for the keys will be: R1.ccna-lab.com
    Choose the size of the key modulus in the range of 360 to 2048 for your
      General Purpose Keys. Choosing a key modulus greater than 512 may take
      a few minutes.

    How many bits in the modulus [512]: 1024
    % Generating 1024 bit RSA keys, keys will be non-exportable...[OK]

    *Mar 1 0:11:30.177: %SSH-5-ENABLED: SSH 1.99 has been enabled

    R1(config)#ip ssh v 2
    R1(config)#username SSHadmin privilege 15 secret $cisco123!

    R1(config)#line vty 0 15
    R1(config-line)#login local 
    R1(config-line)# transport input ssh


##### Шаг 2. Включите защищенные веб-службы с проверкой подлинности на R1.

На этом этапе CPT не дал сделать эту настройку, посмотрел в чате, подсказали идею с сервером, теперь топология немного поменялась

<img width="800" height="476" alt="image" src="https://github.com/user-attachments/assets/5cb22cc9-25b3-4cd0-942a-04e81b9801d2" />

Настройки интерфейса g0/0/0

    R1(config-if)#ip address 10.50.0.1 255.255.255.0
    R1(config-if)# no sh

IP сервера - 10.50.0.5/24

Проверка с PC-A

    C:\>ping 10.50.0.5

    Pinging 10.50.0.5 with 32 bytes of data:

    Request timed out.
    Reply from 10.50.0.5: bytes=32 time=1ms TTL=127
    Reply from 10.50.0.5: bytes=32 time<1ms TTL=127
    Reply from 10.50.0.5: bytes=32 time<1ms TTL=127

    Ping statistics for 10.50.0.5:
        Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 1ms, Average = 0ms

<img width="704" height="682" alt="image" src="https://github.com/user-attachments/assets/1f3a5f0b-f9b6-4cdc-8516-8a70ee59b5fe" />

HTTPS тоже работает





### Часть 6. Проверка подключения

##### Шаг 1. Настройте узлы ПК.

Настроил выше, когда сервер подключал.

##### Шаг 2. Выполните следующие тесты. Эхозапрос должен пройти успешно.

<img width="856" height="350" alt="image" src="https://github.com/user-attachments/assets/d83c3f3f-6f87-4046-8896-b8f1df1e5ff4" />

При проверке с PC-B Loopback1 не пинговался, забыл его сделать.

    R1(config)#int loopback1
    R1(config-if)#ip address 172.16.1.1 255.255.255.0
    R1(config-if)#no sh

    Ping statistics for 172.16.1.1:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

    C:\>ping 172.16.1.1

    Pinging 172.16.1.1 with 32 bytes of data:

    Reply from 172.16.1.1: bytes=32 time<1ms TTL=255
    Reply from 172.16.1.1: bytes=32 time<1ms TTL=255
    Reply from 172.16.1.1: bytes=32 time<1ms TTL=255
    Reply from 172.16.1.1: bytes=32 time<1ms TTL=255

    Ping statistics for 172.16.1.1:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 0ms, Average = 0ms

  Все проверки, кроме HTTPS 10.20.0.1( проверка прошла на 10.50.0.5) и HTTPS 172.16.1.1, отработали.


### Часть 7. Настройка и проверка списков контроля доступа (ACL)

##### При проверке базового подключения компания требует реализации следующих политик безопасности:

##### Политика1. Сеть Sales не может использовать SSH в сети Management (но в  другие сети SSH разрешен). 

##### Политика 2. Сеть Sales не имеет доступа к IP-адресам в сети Management с помощью любого веб-протокола (HTTP/HTTPS). Сеть Sales также не имеет доступа к интерфейсам R1 с помощью любого веб-протокола. Разрешён весь другой веб-трафик (обратите внимание — Сеть Sales  может получить доступ к интерфейсу Loopback 1 на R1).

##### Политика3. Сеть Sales не может отправлять эхо-запросы ICMP в сети Operations или Management. Разрешены эхо-запросы ICMP к другим адресатам. 

##### Политика 4: Cеть Operations  не может отправлять ICMP эхозапросы в сеть Sales. Разрешены эхо-запросы ICMP к другим адресатам. 


##### Шаг 1. Проанализируйте требования к сети и политике безопасности для планирования реализации ACL.

Тут пока тяжело для понимания, постараюсь прописывать все шаги, чтобы самому понятней было.

Весь трафик маршрутизирует S1, на нем разворачиваем списки.

По правилу - расширенные списки  должны размещаться ближе к источнику, получается на вход сабинтерфейсов.

Сначала хочу создать списки, потом уже подключить на интерфейсе.



##### Шаг 2. Разработка и применение расширенных списков доступа, которые будут соответствовать требованиям политики безопасности.

создаем список по политикам Sales - у него 3 полити, так что назову Sales_ACL



1. Сеть Sales 10.40.0.0 255.255.255.0 , сеть Management 10.20.0.0 255.255.255.0 - это нужно запретить , остальной трафик ssh(22 порт) разрешить.

       R1(config-ext-nacl)#deny tcp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 eq 22

2. Сеть Sales 10.40.0.0 255.255.255.0 не имеет доступ с помощью любого веб-протокола (HTTP/HTTPS) к  Management 10.20.0.0 255.255.255.0, пропишу это правило, но тк веб трафик проверить можем только через сервер(10.50.0.5) заблокируем и всю сеть 10.50.0.0 255.255.255.0 для HTTP/HTTPS.

        R1(config-ext-nacl)#deny tcp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 eq 80
        R1(config-ext-nacl)#deny tcp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 eq 443
   
        R1(config-ext-nacl)#deny tcp 10.40.0.0 0.0.0.255 10.50.0.0 0.0.0.255 eq 80
        R1(config-ext-nacl)#deny tcp 10.40.0.0 0.0.0.255 10.50.0.0 0.0.0.255 eq 443

3. Сеть Sales 10.40.0.0 255.255.255.0 не может отправлять эхо-запросы ICMP в сети Operations 10.30.0.0 255.255.255.0 или Management 10.20.0.0 255.255.255.0

        R1(config-ext-nacl)#deny icmp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 echo
        R1(config-ext-nacl)#deny icmp 10.40.0.0 0.0.0.255 10.30.0.0 0.0.0.255 echo

Также интересно заблокировать эхо-запросы ICMP на адрес интерфейса g0/0/0 на S1(10.50.0.1) ? но для сервера(10.50.0.5) оставить, веб ресурс на нем заблокирован, пинг хочу оставить

        R1(config-ext-nacl)#deny icmp 10.40.0.0 0.0.0.255 host 10.50.0.1 echo

Получается, что запреты мы прописали, остальной трафик должен проходить, значит нужно прописать разрешение

        R1(config-ext-nacl)#permit ip any any


4. сетm Operations 10.30.0.0 255.255.255.0 сидит на другом интерфейсе, сделаю другой список Operations_ACL

        R1(config)#ip access-list extended Operations_ACL

   
Cеть Operations 10.30.0.0 255.255.255 не может отправлять ICMP эхозапросы в сеть Sales 10.40.0.0 255.255.255.0

        R1(config-ext-nacl)#deny icmp 10.30.0.0 0.0.0.255 10.40.0.0 0.0.0.255 echo
        R1(config-ext-nacl)#permit ip any any

    
        

##### Шаг 3. Убедитесь, что политики безопасности применяются развернутыми списками доступа.

Включаем работу списков на интерфейсах, для Sales_ACL это сабинтерфейс g0/0/1.40 , для Operations_ACL - g0/0/1.30

        R1(config)#int g0/0/1.30
        R1(config-subif)#ip access-group Operations_ACL in

        R1(config)#int g0/0/1.40
        R1(config-subif)#ip access-group Sales_ACL in

        


Проверяем

<img width="872" height="364" alt="image" src="https://github.com/user-attachments/assets/da813443-379d-483d-8d3c-3255706a0da1" />


На PC-A

        C:\>ping 10.40.0.10

        Pinging 10.40.0.10 with 32 bytes of data:

        Reply from 10.30.0.1: Destination host unreachable.
        Reply from 10.30.0.1: Destination host unreachable.
        Reply from 10.30.0.1: Destination host unreachable.
        Reply from 10.30.0.1: Destination host unreachable.

        Ping statistics for 10.40.0.10:
            Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

        C:\>ping 10.20.0.1

        Pinging 10.20.0.1 with 32 bytes of data:

        Reply from 10.20.0.1: bytes=32 time<1ms TTL=255
        Reply from 10.20.0.1: bytes=32 time<1ms TTL=255
        Reply from 10.20.0.1: bytes=32 time<1ms TTL=255
        Reply from 10.20.0.1: bytes=32 time=3ms TTL=255

        Ping statistics for 10.20.0.1:
            Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
        Approximate round trip times in milli-seconds:
            Minimum = 0ms, Maximum = 3ms, Average = 0ms



На PC-B


        C:\>ping 10.30.0.10

        Pinging 10.30.0.10 with 32 bytes of data:

        Reply from 10.40.0.1: Destination host unreachable.
        Reply from 10.40.0.1: Destination host unreachable.
        Reply from 10.40.0.1: Destination host unreachable.
        Reply from 10.40.0.1: Destination host unreachable.

        Ping statistics for 10.30.0.10:
            Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

        C:\>ping 10.20.0.1

        Pinging 10.20.0.1 with 32 bytes of data:

        Reply from 10.40.0.1: Destination host unreachable.
        Reply from 10.40.0.1: Destination host unreachable.
        Reply from 10.40.0.1: Destination host unreachable.
        Reply from 10.40.0.1: Destination host unreachable.

        Ping statistics for 10.20.0.1:
            Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

        C:\>ping 172.16.1.1

        Pinging 172.16.1.1 with 32 bytes of data:

        Reply from 172.16.1.1: bytes=32 time<1ms TTL=255
        Reply from 172.16.1.1: bytes=32 time<1ms TTL=255
        Reply from 172.16.1.1: bytes=32 time<1ms TTL=255
        Reply from 172.16.1.1: bytes=32 time<1ms TTL=255

        Ping statistics for 172.16.1.1:
            Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
        Approximate round trip times in milli-seconds:
            Minimum = 0ms, Maximum = 0ms, Average = 0ms



Два дополнительныйх теста по ICMP запросам интерфейса g0/0/0(заблокирован) и сервера(оставили разрешенным)


        C:\>ping 10.50.0.1

        Pinging 10.50.0.1 with 32 bytes of data:

        Reply from 10.40.0.1: Destination host unreachable.
        Reply from 10.40.0.1: Destination host unreachable.
        Reply from 10.40.0.1: Destination host unreachable.
        Reply from 10.40.0.1: Destination host unreachable.

        Ping statistics for 10.50.0.1:
            Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

        C:\>ping 10.50.0.5

        Pinging 10.50.0.5 with 32 bytes of data:

        Reply from 10.50.0.5: bytes=32 time<1ms TTL=127
        Reply from 10.50.0.5: bytes=32 time<1ms TTL=127
        Reply from 10.50.0.5: bytes=32 time<1ms TTL=127
        Reply from 10.50.0.5: bytes=32 time<1ms TTL=127

        Ping statistics for 10.50.0.5:
            Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
        Approximate round trip times in milli-seconds:
            Minimum = 0ms, Maximum = 0ms, Average = 0ms


Тут я решил немного изменить список для теста, c PC-B HTTP заблокировать на серваке, а для HTTPS открыть

для этого смотрим списки

    R1#show access-lists 
    Extended IP access list Sales_ACL
        10 deny tcp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 eq 22
        20 deny tcp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 eq www
        30 deny tcp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 eq 443
        40 deny tcp 10.40.0.0 0.0.0.255 10.50.0.0 0.0.0.255 eq www
        50 deny tcp 10.40.0.0 0.0.0.255 10.50.0.0 0.0.0.255 eq 443
        60 deny icmp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 echo (4 match(es))
        70 deny icmp 10.40.0.0 0.0.0.255 10.30.0.0 0.0.0.255 echo (4 match(es))
        80 deny icmp 10.40.0.0 0.0.0.255 host 10.50.0.1 echo (4 match(es))
        90 permit ip any any (8 match(es))
    Extended IP access list Operations_ACL
        10 deny icmp 10.30.0.0 0.0.0.255 10.40.0.0 0.0.0.255 echo (4 match(es))
        20 permit ip any any (4 match(es))

Хочу поменять 50 deny tcp 10.40.0.0 0.0.0.255 10.50.0.0 0.0.0.255 eq 443 - убираем его, для наглядности поставлю 50 разрешение на доступ , оно и так будет , это для наглядности.

permit tcp 10.40.0.0 0.0.0.255 10.50.0.0 0.0.0.255 eq 443



        R1#conf t
        R1(config)#ip access-list extended Sales_ACL
        R1(config-ext-nacl)#no 50
        R1(config-ext-nacl)#50 permit tcp 10.40.0.0 0.0.0.255 10.50.0.0 0.0.0.255 eq 443

        R1#show access-lists 
        Extended IP access list Sales_ACL
            10 deny tcp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 eq 22
            20 deny tcp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 eq www
            30 deny tcp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 eq 443
            40 deny tcp 10.40.0.0 0.0.0.255 10.50.0.0 0.0.0.255 eq www
            50 permit tcp 10.40.0.0 0.0.0.255 10.50.0.0 0.0.0.255 eq 443
            60 deny icmp 10.40.0.0 0.0.0.255 10.20.0.0 0.0.0.255 echo (4 match(es))
            70 deny icmp 10.40.0.0 0.0.0.255 10.30.0.0 0.0.0.255 echo (4 match(es))
            80 deny icmp 10.40.0.0 0.0.0.255 host 10.50.0.1 echo (4 match(es))
            90 permit ip any any (8 match(es))
        Extended IP access list Operations_ACL
            10 deny icmp 10.30.0.0 0.0.0.255 10.40.0.0 0.0.0.255 echo (4 match(es))
            20 permit ip any any (4 match(es))

Проверяем


<img width="703" height="685" alt="image" src="https://github.com/user-attachments/assets/f9dad67c-b4ff-4a07-956f-f2151fbb085c" />

<img width="705" height="689" alt="image" src="https://github.com/user-attachments/assets/67483540-5fae-4101-ba9b-c4142f4ae528" />

Отлично:)

Теперь SSH на 10.20.0.4 и 172.16.1.1

        C:\>ssh -l SSHadmin 10.20.0.4

        % Connection timed out; remote host not responding
        
        C:\>ssh -l SSHadmin 172.16.1.1

        Password: 

        FG66

        R1#


Все тесты прошли успешно!











