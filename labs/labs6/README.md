# Лабораторная работа № 6. Внедрение маршрутизации между виртуальными локальными сетями 


## Топология

<img width="725" height="229" alt="image" src="https://github.com/user-attachments/assets/f660e419-5ca2-4011-8430-f1f22ddd1753" />


##  Таблица адресации

<img width="777" height="281" alt="image" src="https://github.com/user-attachments/assets/f258469b-0c70-4b38-8342-4ccb096891c0" />


## Таблица VLAN

<img width="782" height="239" alt="image" src="https://github.com/user-attachments/assets/122f721c-db2e-47ba-ae5b-e0b1ef52d955" />



## Задачи

### Часть 1. Создание сети и настройка основных параметров устройства
### Часть 2. Создание сетей VLAN и назначение портов коммутатора
### Часть 3. Настройка транка 802.1Q между коммутаторами.
### Часть 4. Настройка маршрутизации между сетями VLAN
### Часть 5. Проверка, что маршрутизация между VLAN работает


## Решение

### Часть 1. Создание сети и настройка основных параметров устройства

##### Шаг 1. Создание сеть согласно топологии.

##### Шаг 2. Настройка базовых параметров для маршрутизатора.

##### Шаг 3. Настройка базовых параметров каждого коммутатора.

##### Шаг 4. Настройка узлов ПК.


## Часть 2. Создание сетей VLAN и назначение портов коммутатора

##### Шаг 1. Создайте сети VLAN на коммутаторах.

a.	Создайте и назовите необходимые VLAN на каждом коммутаторе из таблицы выше.

    S1(config)#vlan 10
    S1(config-vlan)#name control
    S1(config-vlan)#ex
    
    S1(config)#vlan 20
    S1(config-vlan)#name Sales
    S1(config-vlan)#ex
    
    S1(config)#vlan 30
    S1(config-vlan)#name Operations
    S1(config-vlan)#ex

    S1(config)#vlan 999
    S1(config-vlan)#name Parking_Lot
    S1(config-vlan)#ex

    S1(config)#vlan 1000
    S1(config-vlan)#name Own
    S1(config-vlan)#ex


Проверяем, все ли получилось

    S2#show vlan

    VLAN Name                             Status    Ports
    ---- -------------------------------- --------- -------------------------------
    1    default                          active    Fa0/1, Fa0/2, Fa0/3, Fa0/4
                                                Fa0/5, Fa0/6, Fa0/7, Fa0/8
                                                Fa0/9, Fa0/10, Fa0/11, Fa0/12
                                                Fa0/13, Fa0/14, Fa0/15, Fa0/16
                                                Fa0/17, Fa0/18, Fa0/19, Fa0/20
                                                Fa0/21, Fa0/22, Fa0/23, Fa0/24
                                                Gig0/1, Gig0/2
    10   Control                          active    
    20   Sales                            active    
    30   Operations                       active    
    999  Parking_Lot                      active    
    1000 Own                              active    
    1002 fddi-default                     active    
    1003 token-ring-default               active    
    1004 fddinet-default                  active    
    1005 trnet-default                    active  
    
b.	Настройте интерфейс управления и шлюз по умолчанию на каждом коммутаторе, используя информацию об IP-адресе в таблице адресации. 

    S1(config)#int vlan 10
    S1(config-if)#ip address 192.168.10.11 255.255.255.0
    S1(config-if)#no shutdown


c.	Назначьте все неиспользуемые порты коммутатора VLAN Parking_Lot, настройте их для статического режима доступа и административно деактивируйте их.

Неиспользуемые порты на S1

    S1: F0/2-4, F0/7-24, G0/1-2

Переводим их в Vlan 999 и выключаем(деактивируем)

    
    S1(config)#int range f0/2-4 , f0/7-24 , g0/1-2
    S1(config-if-range)#switchport access vlan 999
    S1(config-if-range)#shutdown 


Неиспользуемые порты на S2

    S2: F0/2-17, F0/19-24, G0/1-2

Переводим их в Vlan 999 и выключаем(деактивируем)

    S2(config)#int range f0/2-17 , f0/19-24 , g0/1-2
    S2(config-if-range)#switchport access vlan 999
    S2(config-if-range)#shutdown 


##### Шаг 2. Назначьте сети VLAN соответствующим интерфейсам коммутатора.

a.	Назначьте используемые порты соответствующей VLAN (указанной в таблице VLAN выше) и настройте их для режима статического доступа.

Для S1 Нужно перевести f0/6 в vlan 20 , а остальные (кроме Деактивированных) в vlan 10

    S1(config)#int f0/6
    S1(config-if)#switchport access vlan 20
    S1(config-if)#no shutdown 

Не помню, какие порты остались для распределения, поэтому посмотрю что получается, наверное логичней было сначала все перевести в нужный нам vlan 10, а потом уже переводить в остальные.

    S1#show vlan

    VLAN Name                             Status    Ports
    ---- -------------------------------- --------- -------------------------------
    1    default                          active    Fa0/1, Fa0/5
    10   control                          active    
    20   Sales                            active    Fa0/6
    30   Operations                       active    
    999  Parking_Lot                      active    Fa0/2, Fa0/3, Fa0/4, Fa0/7
                                                Fa0/8, Fa0/9, Fa0/10, Fa0/11
                                                Fa0/12, Fa0/13, Fa0/14, Fa0/15
                                                Fa0/16, Fa0/17, Fa0/18, Fa0/19
                                                Fa0/20, Fa0/21, Fa0/22, Fa0/23
                                                Fa0/24, Gig0/1, Gig0/2
    1000 Own                              active    
    1002 fddi-default                     active    
    1003 token-ring-default               active    
    1004 fddinet-default                  active    
    1005 trnet-default                    active    

Тут видно, что остались Fa0/1, Fa0/5

    S1(config)#int range f0/1 , f0/5
    S1(config-if-range)#switchport access vlan 10
    S1(config-if-range)#no sh


Теперь S2

    S2(config)#int f0/18
    S2(config-if)#switchport access vlan 30
    S2(config-if)#no sh

Проверяю какие порты остались в Vlan 1

    S2#show vlan

    VLAN Name                             Status    Ports
    ---- -------------------------------- --------- -------------------------------
    1    default                          active    Fa0/1
    10   Control                          active    
    20   Sales                            active    
    30   Operations                       active    Fa0/18
    999  Parking_Lot                      active    Fa0/2, Fa0/3, Fa0/4, Fa0/5
                                                Fa0/6, Fa0/7, Fa0/8, Fa0/9
                                                Fa0/10, Fa0/11, Fa0/12, Fa0/13
                                                Fa0/14, Fa0/15, Fa0/16, Fa0/17
                                                Fa0/19, Fa0/20, Fa0/21, Fa0/22
                                                Fa0/23, Fa0/24, Gig0/1, Gig0/2
    1000 Own                              active    
    1002 fddi-default                     active    
    1003 token-ring-default               active    
    1004 fddinet-default                  active    
    1005 trnet-default                    active   

Перевожу в нужный vlan 10

    S2(config)#int f0/1
    S2(config-if)#switchport access vlan 10
    S2(config-if)#no sh
    
b.	Убедитесь, что VLAN назначены на правильные интерфейсы.

Еще раз смотрю, чтобы все порты были в нужных vlan, не буду сюда вставлять, чтобы сильно не засорять домашнее задание.

Да, все получилось.

## Часть 3. Конфигурация магистрального канала стандарта 802.1Q между коммутаторами

##### Шаг 1. Вручную настройте магистральный интерфейс F0/1 на коммутаторах S1 и S2.

a.	Настройка статического транкинга на интерфейсе F0/1 для обоих коммутаторов.

b.	Установите native VLAN 1000 на обоих коммутаторах.

c.	Укажите, что VLAN 10, 20, 30 и 1000 могут проходить по транку.


Для S1 и S2 прописываем

    S1(config)#int f0/1 
    S1(config-if)#switchport mode trunk 
    S1(config-if)#switchport trunk native vlan 1000
    S2(config-if)#switchport trunk allowed vlan 10,20,30,1000



d.	Проверьте транки, native VLAN и разрешенные VLAN через транк.

На S1

    S1#show int trunk 
    Port        Mode         Encapsulation  Status        Native vlan
    Fa0/1       on           802.1q         trunking      1000

    Port        Vlans allowed on trunk
    Fa0/1       10,20,30,1000

    Port        Vlans allowed and active in management domain
    Fa0/1       10,20,30,1000

    Port        Vlans in spanning tree forwarding state and not pruned
    Fa0/1       10,20,30,1000


На S2

    S2#show int tr

    Port        Mode         Encapsulation  Status        Native vlan
    Fa0/1       on           802.1q         trunking      1000

    Port        Vlans allowed on trunk
    Fa0/1       10,20,30,1000

    Port        Vlans allowed and active in management domain
    Fa0/1       10,20,30,1000

    Port        Vlans in spanning tree forwarding state and not pruned
    Fa0/1       10,20,30,1000

Вроде все как надо.

##### Шаг 2. Вручную настройте магистральный интерфейс F0/5 на коммутаторе S1.
a.	Настройте интерфейс S1 F0/5 с теми же параметрами транка, что и F0/1. Это транк до маршрутизатора.

    S1(config)#int f0/5
    S1(config-if)#switchport mode trunk 
    S1(config-if)#switchport trunk native vlan 1000
    S1(config-if)#switchport trunk allowed vlan 10,20,30,1000

b.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

    S1#wr m
    
c.	Проверка транкинга.
    S1#show int trunk 
    Port        Mode         Encapsulation  Status        Native vlan
    Fa0/1       on           802.1q         trunking      1000

    Port        Vlans allowed on trunk
    Fa0/1       10,20,30,1000

    Port        Vlans allowed and active in management domain
    Fa0/1       10,20,30,1000

    Port        Vlans in spanning tree forwarding state and not pruned
    Fa0/1       10,20,30,1000
    
Не появился f0/5 , видимо нужно настроить на другой стороне этот порт

Вопрос:
Что произойдет, если G0/0/1 на R1 будет отключен?

Ответ:
Транк не поднимается между R1 и S1, следовательно никакой маршрутизации.


## Часть 4. Настройка маршрутизации между сетями VLAN

##### Шаг 1. Настройте маршрутизатор.

a.	При необходимости активируйте интерфейс G0/0/1 на маршрутизаторе.

После включения интерфейса проверил шнтерфейсы транков на S1

    S1#show interfaces tr
    Port        Mode         Encapsulation  Status        Native vlan
    Fa0/1       on           802.1q         trunking      1000
    Fa0/5       on           802.1q         trunking      1000

Поднялся транк на пятом порту.

b.	Настройте подинтерфейсы для каждой VLAN, как указано в таблице IP-адресации. Все подинтерфейсы используют инкапсуляцию 802.1Q. Убедитесь, что подинтерфейсу для native VLAN не назначен IP-адрес. Включите описание для каждого подинтерфейса.

    R1(config)#int g0/0/1.10
    R1(config-subif)#Description Default Gateway for Vlan 10
    1(config-subif)#encapsulation dot1Q 10
    R1(config-subif)#ip address 192.168.10.1 255.255.255.0
    R1(config-subif)#ex
    
    R1(config)#int g0/0/1.20
    R1(config-subif)#Description Default Gateway for Vlan 20
    R1(config-subif)#encapsulation dot1Q 20
    R1(config-subif)#ip address 192.168.20.1 255.255.255.0
    R1(config-subif)#ex

    R1(config)#int g0/0/1.30
    R1(config-subif)#Description Default Gateway for Vlan 30
    R1(config-subif)#encapsulation dot1Q 30
    R1(config-subif)#ip address 192.168.30.1 255.255.255.0
    R1(config-subif)#ex

    R1(config)#int g0/0/1.10
    R1(config-subif)#Description Native Vlan 1000
    R1(config-subif)#encapsulation dot1Q 1000
    R1(config-subif)#ex




Забыл назначить шлюз по умолчанию на S1 и S2

    S1(config)#ip default-gateway 192.168.10.1

    
c.	Убедитесь, что вспомогательные интерфейсы работают

    R1#show ip interface brief 
    Interface              IP-Address      OK? Method Status                Protocol 
    GigabitEthernet0/0/0   unassigned      YES NVRAM  administratively down down 
    GigabitEthernet0/0/1   unassigned      YES NVRAM  up                    up 
    GigabitEthernet0/0/1.10192.168.10.1    YES manual up                    up 
    GigabitEthernet0/0/1.20192.168.20.1    YES manual up                    up 
    GigabitEthernet0/0/1.30192.168.30.1    YES manual up                    up 
    GigabitEthernet0/0/1.1000unassigned      YES unset  up                    up 
    GigabitEthernet0/0/2   unassigned      YES NVRAM  administratively down down 
    Vlan1                  unassigned      YES unset  administratively down down
    R1#


## Часть 5. Проверьте, работает ли маршрутизация между VLAN

##### Шаг 1. Выполните следующие тесты с PC-A. Все должно быть успешно. 

a.	Отправьте эхо-запрос с PC-A на шлюз по умолчанию.
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

b.	Отправьте эхо-запрос с PC-A на PC-B.

    C:\>ping 192.168.30.3

    Pinging 192.168.30.3 with 32 bytes of data:

    Request timed out.
    Reply from 192.168.30.3: bytes=32 time<1ms TTL=127
    Reply from 192.168.30.3: bytes=32 time<1ms TTL=127
    Reply from 192.168.30.3: bytes=32 time<1ms TTL=127

    Ping statistics for 192.168.30.3:
        Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 0ms, Average = 0ms

c.	Отправьте команду ping с компьютера PC-A на коммутатор S2.

    C:\>ping 192.168.10.12

    Pinging 192.168.10.12 with 32 bytes of data:

    Request timed out.
    Request timed out.
    Reply from 192.168.10.12: bytes=32 time<1ms TTL=254
    Reply from 192.168.10.12: bytes=32 time<1ms TTL=254

    Ping statistics for 192.168.10.12:
        Packets: Sent = 4, Received = 2, Lost = 2 (50% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 0ms, Average = 0ms
    
##### Шаг 2. Пройдите следующий тест с PC-B

В окне командной строки на PC-B выполните команду tracert на адрес PC-A.

    C:\>tracert 192.168.20.3

    Tracing route to 192.168.20.3 over a maximum of 30 hops: 

      1   0 ms      0 ms      0 ms      192.168.30.1
      2   0 ms      0 ms      0 ms      192.168.20.3

    Trace complete.

Вопрос:
Какие промежуточные IP-адреса отображаются в результатах?

Промежуточный IP 192.168.30.1 - это IP подинтерфейса на роутере, тк компьтеры в разных Vlan, они напрямую друг друга не видят, общараются к роутеру.

Транковые порты, которые оставались при распределении по vlan, мог не убирать с vlan 1, они стали trunk , поэтому было лишнее дествие, понял уже потом, но оставил ход лабы такой, как был.

Выгрузка из CPT [Здесь](https://github.com/feelgood6/labs/blob/cbfb373518e805037e22f11db94d1047c5e6ae19/labs/labs6/labs6.pkt)




    
