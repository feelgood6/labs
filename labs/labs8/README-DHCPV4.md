
# Лабраторная работа №8 - Реализация DHCPv4 

# Реализация DHCPv4 

## Топология

<img width="680" height="67" alt="image" src="https://github.com/user-attachments/assets/a4ddb895-cbe2-4010-84eb-72f6040f3d40" />


## Таблица адресации

<img width="692" height="343" alt="image" src="https://github.com/user-attachments/assets/4fb1fd32-1f2e-451f-b039-8f99e9cea9bb" />


## Таблица VLAN

<img width="675" height="171" alt="image" src="https://github.com/user-attachments/assets/58de8766-e566-4b53-802a-30041e20e5f8" />

## Задачи 

### Часть 1. Создание сети и настройка основных параметров устройства
### Часть 2. Настройка и проверка двух серверов DHCPv4 на R1
### Часть 3. Настройка и проверка DHCP-ретрансляции на R2


### Решение 

### Часть 1.	Создание сети и настройка основных параметров устройства

##### Шаг 1.	Создание схемы адресации

# 128 64 32 16 8 4 2 1

Формула 2^n - 2 >= x

Подсеть сети 192.168.1.0/24 в соответствии со следующими требованиями:

a.	Одна подсеть «Подсеть A», поддерживающая 58 хостов (клиентская VLAN на R1).
Подсеть A

Запишите первый IP-адрес в таблице адресации для R1 G0/0/1.100 . 

Расчитываем подcеть А

2^n - 2 >= 58 -> 2^6 - 2 = 62

Нам нужна 26 маска

Первый адрес 192.168.1.1 маска 255.255.255.192

Даем ip интерфейсу

    R1(config)#int g0/0/1
    R1(config-if)#no sh
    
    R1(config)#int g0/0/1.100
    R1(config-subif)#encapsulation dot1Q 100
    R1(config-subif)#ip address 192.168.1.1 255.255.255.192
    R1(config-subif)#no sh

b.	Одна подсеть «Подсеть B», поддерживающая 28 хостов (управляющая VLAN на R1). 
Подсеть B:

Формула 2^n - 2 >= 28 -> 2^5 - 2 = 34

Нужна 27 маска

Первый адрес 192.168.1.65 маска 255.255.255.224

    R1(config)#int g0/0/0.200
    R1(config-subif)#encapsulation dot1Q 200
    R1(config-subif)#ip address 192.168.1.65 255.255.255.224


Заходим на S1, назначаем vlan 100 второй адрес сети 192.168.1.64/27 и сразу создаем все vlan


    S1(config)#vlan 200
    S1(config-vlan)#name Management
    S1(config-vlan)#int vlan 200
    S1(config-if)#ip address 192.168.1.66 255.255.255.224
    
    
    S1(config)#vlan 100
    S1(config-vlan)#name Clients
    S1(config-vlan)#ex
    
    S1(config)#vlan 999
    S1(config-vlan)#name Parking_Lot
    S1(config-vlan)#ex
    
    S1(config)#vlan 1000
    S1(config-vlan)#name Native
    S1(config-vlan)#ex


c.	Одна подсеть «Подсеть C», поддерживающая 12 узлов (клиентская сеть на R2).
Подсеть C:
Запишите первый IP-адрес в таблице адресации для R2 G0/0/1.

Формула 2^n - 2 >= 14 -> 2^4 - 2 = 14

Нужна /28 или 255.255.255.240 , адрес сети 192.168.1.96

Первый адрес сети будет 192.168.1.97 255.255.255.240


По заданию только сейчас понял, что нужно было сначала расчитать и записать в таблицу, а потом делать настройки. Так наверное было бы проще, потому что тут сильно путался и терялся между всем этим, так что настройки впишу уже потом, когда дойду до нужного пункта.

Итоговая таблица адресации

<img width="694" height="351" alt="image" src="https://github.com/user-attachments/assets/a0f5c050-c49d-41a2-82f6-3c924504a19b" />


##### Шаг 2.	Создайте сеть согласно топологии.
Сеть создана

##### Шаг 3.	Произведите базовую настройку маршрутизаторов.

##### Шаг 4.	Настройка маршрутизации между сетями VLAN на маршрутизаторе R1

a.	Активируйте интерфейс G0/0/1 на маршрутизаторе.
Готово

b.	Настройте подинтерфейсы для каждой VLAN в соответствии с требованиями таблицы IP-адресации. Все субинтерфейсы используют инкапсуляцию 802.1Q и назначаются первый полезный адрес из вычисленного пула IP-адресов. Убедитесь, что подинтерфейсу для native VLAN не назначен IP-адрес. Включите описание для каждого подинтерфейса.


Я уже прописывал настройки для G0/0/1.100, но пропишу еще раз, чтобы перепроверить себя, забыл добавить комментарии

    R1(config)#int g0/0/1.100
    R1(config-subif)#description Clients
    R1(config-subif)#encapsulation dot1Q 100
    R1(config-subif)#ip address 192.168.1.1 255.255.255.192

    R1(config-subif)#ip address 192.168.1.65 255.255.255.224
    % 192.168.1.64 overlaps with GigabitEthernet0/0/0.200

Нашел ошибку, ip назначил интерфейсу g0/0/0.200

    R1(config)#int g0/0/0.200
    R1(config-subif)#no ip add
    R1(config-subif)#no ip address 192.168.1.65 255.255.255.224
    R1(config-subif)#ex

    R1(config)#int g0/0/1.200
    R1(config-subif)#ip address 192.168.1.65 255.255.255.224
    R1(config-subif)#

    R1(config)#int g0/0/1.1000
    R1(config-subif)#description Native
    R1(config-subif)#encapsulation dot1Q 1000


c.	Убедитесь, что вспомогательные интерфейсы работают.

    R1#show ip int brief 
    Interface              IP-Address      OK? Method Status                Protocol 
    GigabitEthernet0/0/0   unassigned      YES unset  up                    up 
    GigabitEthernet0/0/0.200unassigned      YES manual administratively down down 
    GigabitEthernet0/0/1   unassigned      YES unset  up                    up 
    GigabitEthernet0/0/1.100192.168.1.1     YES manual up                    up 
    GigabitEthernet0/0/1.200192.168.1.65    YES manual up                    up 
    GigabitEthernet0/0/1.1000unassigned      YES unset  up                    up 
    GigabitEthernet0/0/2   unassigned      YES unset  administratively down down 
    Vlan1                  unassigned      YES unset  administratively down down


Увидел GigabitEthernet0/0/0.200, пока не знаю как его убрать, сейчас его просто выключил.



##### Шаг 5.	Настройте G0/1 на R2, затем G0/0/0 и статическую маршрутизацию для обоих маршрутизаторов

a.	Настройте G0/0/1 на R2 с первым IP-адресом подсети C, рассчитанным ранее.

    R2(config)#int g0/0/1
    R2(config-if)#ip address 192.168.1.97 255.255.255.240
    R2(config-if)#no sh

b.	Настройте интерфейс G0/0/0 для каждого маршрутизатора на основе приведенной выше таблицы IP-адресации. 
c.	Настройте маршрут по умолчанию на каждом маршрутизаторе, указываемом на IP-адрес G0/0/0 на другом маршрутизаторе.

    R2(config)#int g0/0/0
    R2(config-if)#ip address 10.0.0.2 255.255.255.252
    R2(config-if)#no sh
    R2(config)#ip default-gateway 10.0.0.1

    R1(config)#int g0/0/0
    R1(config-if)#ip address 10.0.0.1 255.255.255.252
    R1(config-if)#no sh
    R1(config)#ip default-gateway 10.0.0.2


d.	Убедитесь, что статическая маршрутизация работает с помощью пинга до адреса G0/0/1 R2 от R1.

    R1#ping 10.0.0.2

    Type escape sequence to abort.
    Sending 5, 100-byte ICMP Echos to 10.0.0.2, timeout is 2 seconds:
    .!!!!
    Success rate is 80 percent (4/5), round-trip min/avg/max = 0/0/0 ms



e.	Сохраните текущую конфигурацию в файл загрузочной конфигурации.

    wr m



##### Шаг 6.	Настройте базовые параметры каждого коммутатора.

Настроил, только время не ставил

    S1#clock set 11:24:00 Mar 17 2026



##### Шаг 7.	Создайте сети VLAN на коммутаторе S1.

a.	Создайте необходимые VLAN на коммутаторе 1 и присвойте им имена из приведенной выше таблицы.
b.	Настройте и активируйте интерфейс управления на S1 (VLAN 200), используя второй IP-адрес из подсети, рассчитанный ранее. Кроме того установите шлюз по умолчанию на S1.

    S1(config)#vlan 100
    S1(config-vlan)#name Clients
    S1(config-vlan)#ex

    S1(config)#vlan 200
    S1(config-vlan)#name Menegment
    S1(config-vlan)#ex

    S1(config)#vlan 999
    S1(config-vlan)#name Parking_Lot
    S1(config-vlan)#ex

    S1(config)#vlan 1000
    S1(config-vlan)#name Native
    S1(config-vlan)#ex

    S1(config)#int vlan 200
    S1(config-if)#no sh
    S1(config-if)#ip address 192.168.1.66 255.255.255.224
    S1(config-vlan)#ex

    S1(config)#ip default-gateway 192.168.1.65


c.	Настройте и активируйте интерфейс управления на S2 (VLAN 1), используя второй IP-адрес из подсети, рассчитанный ранее. Кроме того, установите шлюз по умолчанию на S2


    S2(config)#int vlan 1
    S2(config-if)#no sh
    S2(config-if)#ip address 192.168.1.98 255.255.255.240
    S2(config-if)#ex

    S2(config)#ip default-gateway 192.168.1.97



d.	Назначьте все неиспользуемые порты S1 VLAN Parking_Lot, настройте их для статического режима доступа и административно деактивируйте их. На S2 административно деактивируйте все неиспользуемые порты.

    S1(config)#int range f0/1-4,f0/7-24,g0/1-2
    S1(config-if-range)#switchport mode access 
    S1(config-if-range)#switchport access vlan 999
    S1(config-if-range)#shutdown 



S2

    S2(config)#int range f0/1-4,f0/6-17,f0/19-24, g0/1-2
    S2(config-if-range)#sh




##### Шаг 8.	Назначьте сети VLAN соответствующим интерфейсам коммутатора.

На S1

    S1(config)#int f0/6
    S1(config-if)#switchport mode access 
    S1(config-if)#switchport access vlan 100


На S1 f0/5 и на S2 f0/18 в и так Vlan 1 потому что они там по умолчанию были.


##### Шаг 9.	Вручную настройте интерфейс S1 F0/5 в качестве транка 802.1Q.

    S1(config-if)#switchport mode trunk 
    S1(config-if)#switchport trunk native 1000
    S1(config-if)#switchport trunk native vlan 1000
    S1(config-if)#switchport trunk allowed vlan 100,200,1000

Какой IP-адрес был бы у ПК, если бы он был подключен к сети с помощью DHCP?

PC-A в подсети А, то есть в IP выдавался бы в диапазане 192.168.1.1 – 192.168.1.62.

### Часть 2.	Настройка и проверка двух серверов DHCPv4 на R1


##### Шаг 1.	Настройте R1 с пулами DHCPv4 для двух поддерживаемых подсетей. Ниже приведен только пул DHCP для подсети A


a.	Исключите первые пять используемых адресов из каждого пула адресов.

    R1(config)#ip dhcp excluded-address 192.168.1.1 192.168.1.5

b.	Создайте пул DHCP (используйте уникальное имя для каждого пула).

    R1(config)#ip dhcp pool subnetA

c.	Укажите сеть, поддерживающую этот DHCP-сервер.

    R1(dhcp-config)#network 192.168.1.0 255.255.255.192

d.	В качестве имени домена укажите CCNA-lab.com.

    R1(dhcp-config)#domain-name CCNA-lab.com

e.	Настройте соответствующий шлюз по умолчанию для каждого пула DHCP.

    R1(dhcp-config)#default-router 10.0.0.2
    
f.	Настройте время аренды на 2 дня 12 часов и 30 минут.

    Не нашел как сделать эту компанду, нашел lease , но она не работает.


g.	Затем настройте второй пул DHCPv4, используя имя пула R2_Client_LAN и вычислите сеть, маршрутизатор по умолчанию, и используйте то же имя домена и время аренды, что и предыдущий пул DHCP.

    R2(config)#ip dhcp excluded-address 192.168.1.97 192.168.1.101
    R2(dhcp-config)#network 192.168.1.96 255.255.255.240
    R2(dhcp-config)#default-router 10.0.0.1
    R2(dhcp-config)#domain-name CCNA-lab.com
    

### Шаг 3.	Проверка конфигурации сервера DHCPv4


На R1

    R1#show ip dhcp pool 

    Pool subnetA :
     Utilization mark (high/low)    : 100 / 0
     Subnet size (first/next)       : 0 / 0 
     Total addresses                : 62
     Leased addresses               : 1
     Excluded addresses             : 1
     Pending event                  : none

     1 subnet is currently in the pool
     Current index        IP address range                    Leased/Excluded/Total
     192.168.1.1          192.168.1.1      - 192.168.1.62      1    / 1     / 62


    R1#show ip dhcp binding 
    IP address       Client-ID/              Lease expiration        Type
                 Hardware address
    192.168.1.6      0001.637E.E4D6           --                     Automatic


Команда R1#show ip dhcp server statistic не выполняется



На R2

    
    R2#show ip dhcp pool 

    Pool R2_Client_LAN :
     Utilization mark (high/low)    : 100 / 0
     Subnet size (first/next)       : 0 / 0 
     Total addresses                : 14
     Leased addresses               : 1
     Excluded addresses             : 3
     Pending event                  : none

     1 subnet is currently in the pool
     Current index        IP address range                    Leased/Excluded/Total
     192.168.1.97         192.168.1.97     - 192.168.1.110     1    / 3     / 14


    R2#show ip dhcp binding 
    IP address       Client-ID/              Lease expiration        Type
                     Hardware address
    192.168.1.102    0060.7016.27C2           --                     Automatic




### Шаг 4.	Попытка получить IP-адрес от DHCP на PC-A

a.	Из командной строки компьютера PC-A выполните команду ipconfig /all.

    C:\>ipconfig /all

    FastEthernet0 Connection:(default port)

       Connection-specific DNS Suffix..: 
       Physical Address................: 0001.637E.E4D6
       Link-local IPv6 Address.........: FE80::201:63FF:FE7E:E4D6
       IPv6 Address....................: ::
       IPv4 Address....................: 192.168.1.6
       Subnet Mask.....................: 255.255.255.192
       Default Gateway.................: FE80::1
                                     0.0.0.0
       DHCP Servers....................: 192.168.1.1
       DHCPv6 IAID.....................: 869353894
       DHCPv6 Client DUID..............: 00-01-00-01-10-E5-11-DC-00-01-63-7E-E4-D6
       DNS Servers.....................: 2001:DB8:ACAD::254
                                     0.0.0.0



b.	После завершения процесса обновления выполните команду ipconfig для просмотра новой информации об IP-адресе.

    C:\>ipconfig /all

    FastEthernet0 Connection:(default port)

       Connection-specific DNS Suffix..: CCNA-lab.com
       Physical Address................: 0001.637E.E4D6
       Link-local IPv6 Address.........: FE80::201:63FF:FE7E:E4D6
       IPv6 Address....................: ::
       IPv4 Address....................: 192.168.1.6
       Subnet Mask.....................: 255.255.255.192
       Default Gateway.................: FE80::1
                                         10.0.0.2
       DHCP Servers....................: 192.168.1.1
       DHCPv6 IAID.....................: 
       DHCPv6 Client DUID..............: 00-01-00-01-10-E5-11-DC-00-01-63-7E-E4-D6
       DNS Servers.....................: 2001:DB8:ACAD::254
                                         0.0.0.0


c.	Проверьте подключение с помощью пинга IP-адреса интерфейса R0 G0/0/1.


    C:\>ping 10.0.0.1

    Pinging 10.0.0.1 with 32 bytes of data:

    Reply from 10.0.0.1: bytes=32 time<1ms TTL=255
    Reply from 10.0.0.1: bytes=32 time<1ms TTL=255
    Reply from 10.0.0.1: bytes=32 time<1ms TTL=255
    Reply from 10.0.0.1: bytes=32 time<1ms TTL=255


### Часть 3.	Настройка и проверка DHCP-ретрансляции на R2

##### Шаг 1.	Настройка R2 в качестве агента DHCP-ретрансляции для локальной сети на G0/0/1


R2(config-if)#ip helper-address 10.0.0.1



##### Шаг 2.	Попытка получить IP-адрес от DHCP на PC-B

a.	Из командной строки компьютера PC-B выполните команду ipconfig /all.

    C:\>ipconfig /all

    FastEthernet0 Connection:(default port)

       Connection-specific DNS Suffix..: STATEFUL.com 
       Physical Address................: 0060.7016.27C2
       Link-local IPv6 Address.........: FE80::260:70FF:FE16:27C2
       IPv6 Address....................: 2001:DB8:ACAD:3:8C94:6060:52CE:443C
       IPv4 Address....................: 192.168.1.102
       Subnet Mask.....................: 255.255.255.240
       Default Gateway.................: FE80::1
                                         0.0.0.0
       DHCP Servers....................: 192.168.1.97
       DHCPv6 IAID.....................: 923934055
       DHCPv6 Client DUID..............: 00-01-00-01-86-26-C9-A0-00-60-70-16-27-C2
       DNS Servers.....................: 2001:DB8:ACAD::254
                                        0.0.0.0



b.	После завершения процесса обновления выполните команду ipconfig для просмотра новой информации об IP-адресе.

    C:\>ipconfig /all

    FastEthernet0 Connection:(default port)

       Connection-specific DNS Suffix..: CCNA-lab.com
                                       : STATEFUL.com 
       Physical Address................: 0060.7016.27C2
       Link-local IPv6 Address.........: FE80::260:70FF:FE16:27C2
       IPv6 Address....................: 2001:DB8:ACAD:3:AED1:904F:741B:6679
       IPv4 Address....................: 192.168.1.102
       Subnet Mask.....................: 255.255.255.240
       Default Gateway.................: FE80::1
                                         10.0.0.1
       DHCP Servers....................: 192.168.1.97
       DHCPv6 IAID.....................: 1765752206
       DHCPv6 Client DUID..............: 00-01-00-01-86-26-C9-A0-00-60-70-16-27-C2
       DNS Servers.....................: 2001:DB8:ACAD::254
                                         0.0.0.0


c.	Проверьте подключение с помощью пинга IP-адреса интерфейса R1 G0/0/1.


    C:\>ping 192.168.1.1

    Pinging 192.168.1.1 with 32 bytes of data:

    Reply from 192.168.1.97: Destination host unreachable.
    Reply from 192.168.1.97: Destination host unreachable.
    Reply from 192.168.1.97: Destination host unreachable.
    Reply from 192.168.1.97: Destination host unreachable.

Тут споткнулся, сначала подумал, что не астроен транк между s2 и r2, ностроил, но потом понял, что ПК-Б во vlan 1, должно работать и без транка. Вернул обратно настройки.

Потом понял, то не настрона команда между роутерами. 

    R1(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.1
    R2(config)# ip route 0.0.0.0 0.0.0.0 10.0.0.1


Повторная проверка

    C:\>ping 192.168.1.1

    Pinging 192.168.1.1 with 32 bytes of data:

    Request timed out.
    Reply from 192.168.1.1: bytes=32 time<1ms TTL=254
    Reply from 192.168.1.1: bytes=32 time<1ms TTL=254
    Reply from 192.168.1.1: bytes=32 time<1ms TTL=254

d.	Выполните show ip dhcp binding для R1 для проверки назначений адресов в DHCP.

    R1#show ip dhcp binding
    IP address       Client-ID/              Lease expiration        Type
                     Hardware address
    192.168.1.6      0001.637E.E4D6           --                     Automatic

Выгрузка из CPT [Здесь](https://github.com/feelgood6/labs/blob/6dd32dd64e93abd41dab7547b57250e5ba78df78/labs/labs8/labs8-DHCPV4.pkt)

