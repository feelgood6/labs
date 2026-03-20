
# Лабраторная работа №8 -  Настройка DHCPv6 

# Настройка DHCPv6 


## Таблица адресации

<img width="682" height="302" alt="image" src="https://github.com/user-attachments/assets/6ae1c0c5-dc16-4e0a-8c60-98a69d76959c" />


## Задачи

Часть 1. Создание сети и настройка основных параметров устройства
Часть 2. Проверка назначения адреса SLAAC от R1
Часть 3. Настройка и проверка сервера DHCPv6 без гражданства на R1
Часть 4. Настройка и проверка состояния DHCPv6 сервера на R1
Часть 5. Настройка и проверка DHCPv6 Relay на R2


## Инструкции

### Часть 1. Создание сети и настройка основных параметров устройства

##### Шаг 1. Создайте сеть согласно топологии.

##### Шаг 2. Настройте базовые параметры каждого коммутатора. (необязательно)

##### Шаг 3. Произведите базовую настройку маршрутизаторов.

##### Шаг 4. Настройка интерфейсов и маршрутизации для обоих маршрутизаторов.



### Часть 2. Проверка назначения адреса SLAAC от R1

Откуда взялась часть адреса с идентификатором хоста?
Она сгенерирована автоматически на основе MAC-адреса сетевой карты с использованием механизма EUI-64

### Часть 3. Настройка и проверка сервера DHCPv6 на R1

##### Шаг 1. Более подробно изучите конфигурацию PC-A.


    FastEthernet0 Connection:(default port)

       Connection-specific DNS Suffix..: 
       Physical Address................: 0001.637E.E4D6
       Link-local IPv6 Address.........: FE80::201:63FF:FE7E:E4D6
       IPv6 Address....................: 2001:DB8:ACAD:1:201:63FF:FE7E:E4D6
       Autoconfiguration IP Address....: 169.254.228.214
       Subnet Mask.....................: 255.255.0.0
       Default Gateway.................: FE80::1
                                         0.0.0.0
       DHCP Servers....................: 0.0.0.0
       DHCPv6 IAID.....................: 
       DHCPv6 Client DUID..............: 00-01-00-01-10-E5-11-DC-00-01-63-7E-E4-D6
       DNS Servers.....................: ::
                                         0.0.0.0

Почему то вывод другой, не такой как в примере,в DNS ничего нет, может где то ошибся, дальше буду смотреть.

##### Шаг 2. Настройте R1 для предоставления DHCPv6 без состояния для PC-A.
Прописываем настройки

    R1(config)# ipv6 dhcp pool R1-STATELESS
    R1(config-dhcp)# dns-server 2001:db8:acad::254
    R1(config-dhcp)# domain-name STATELESS.com
    R1(config)#exit
    R1(config)# interface g0/0/1
    R1(config-if)# ipv6 nd other-config-flag 
    R1(config-if)# ipv6 dhcp server R1-STATELESS

Сохраняем, перезапускаем ПК

    C:\>ipconfig /all

    FastEthernet0 Connection:(default port)

       Connection-specific DNS Suffix..: STATELESS.com 
       Physical Address................: 0001.637E.E4D6
       Link-local IPv6 Address.........: FE80::201:63FF:FE7E:E4D6
       IPv6 Address....................: 2001:DB8:ACAD:1:201:63FF:FE7E:E4D6
       IPv4 Address....................: 0.0.0.0
       Subnet Mask.....................: 0.0.0.0
       Default Gateway.................: FE80::1
                                         0.0.0.0
       DHCP Servers....................: 0.0.0.0
       DHCPv6 IAID.....................: 869353894
       DHCPv6 Client DUID..............: 00-01-00-01-10-E5-11-DC-00-01-63-7E-E4-D6
       DNS Servers.....................: 2001:DB8:ACAD::254
                                         0.0.0.0

появился адрес DNS, может так и надо

Пингуем G0/1 R2


    C:\>ping 2001:db8:acad:3::1

    Pinging 2001:db8:acad:3::1 with 32 bytes of data:

    Reply from 2001:DB8:ACAD:3::1: bytes=32 time<1ms TTL=254
    Reply from 2001:DB8:ACAD:3::1: bytes=32 time<1ms TTL=254
    Reply from 2001:DB8:ACAD:3::1: bytes=32 time<1ms TTL=254
    Reply from 2001:DB8:ACAD:3::1: bytes=32 time<1ms TTL=254

Все хорошо.


### Часть 4. Настройка сервера DHCPv6 с сохранением состояния на R1

    R1(config)# ipv6 dhcp pool R2-STATEFUL
    R1(config-dhcp)# address prefix 2001:db8:acad:3:aaa::/80
    R1(config-dhcp)# dns-server 2001:db8:acad::254
    R1(config-dhcp)# domain-name STATEFUL.com
    R1(config)# interface g0/0/0
    R1(config-if)# ipv6 dhcp server R2-STATEFUL



### Часть 5. Настройка и проверка ретрансляции DHCPv6 на R2.

##### Шаг 1. Включите PC-B и проверьте адрес SLAAC, который он генерирует.
    C:\>ipconfig /all

    FastEthernet0 Connection:(default port)

      Connection-specific DNS Suffix..: 
      Physical Address................: 0060.7016.27C2
      Link-local IPv6 Address.........: FE80::260:70FF:FE16:27C2
      IPv6 Address....................: 2001:DB8:ACAD:3:260:70FF:FE16:27C2
      Autoconfiguration IP Address....: 169.254.39.194
      Subnet Mask.....................: 255.255.0.0
      Default Gateway.................: FE80::1
                                        0.0.0.0
      DHCP Servers....................: 0.0.0.0
      DHCPv6 IAID.....................: 
      DHCPv6 Client DUID..............: 00-01-00-01-86-26-C9-A0-00-60-70-16-27-C2
      DNS Servers.....................: ::
                                        0.0.0.0

##### Шаг 2. Настройте R2 в качестве агента DHCP-ретрансляции для локальной сети на G0/0/1.
Так как relay в CPT не реализован, то настраиваем Stateful DHCPv6 на R2

    R2(config)#ipv6 dhcp pool R2-STATEFUL
    R2(config-dhcpv6)#address prefix 2001:db8:acad:3::/64
    R2(config-dhcpv6)#dns-server 2001:db8:acad::254
    R2(config-dhcpv6)#domain-name STATEFUL.com
    R2(config-dhcpv6)#ex

    R2(config)#int g0/0/1
    R2(config-if)#ipv6 nd managed-config-flag
    R2(config-if)#ipv6 dhcp server R2-STATEFUL
   
##### Шаг 3. Попытка получить адрес IPv6 из DHCPv6 на PC-B.

Перезапустил компьютер

    C:\>ipconfig /all

    FastEthernet0 Connection:(default port)

      Connection-specific DNS Suffix..: STATEFUL.com 
      Physical Address................: 0060.7016.27C2
      Link-local IPv6 Address.........: FE80::260:70FF:FE16:27C2
      IPv6 Address....................: 2001:DB8:ACAD:3:64F7:48C2:3A20:2C9E
      IPv4 Address....................: 0.0.0.0
      Subnet Mask.....................: 0.0.0.0
      Default Gateway.................: FE80::1
                                     0.0.0.0
      DHCP Servers....................: 0.0.0.0
      DHCPv6 IAID.....................: 923934055
      DHCPv6 Client DUID..............: 00-01-00-01-86-26-C9-A0-00-60-70-16-27-C2
      DNS Servers.....................: 2001:DB8:ACAD::254
                                        0.0.0.0




c.	Проверьте подключение с помощью пинга IP-адреса интерфейса R0 G0/0/1.(как я понял, подключения к R1)

    C:\>ping 2001:db8:acad:1::1

    Pinging 2001:db8:acad:1::1 with 32 bytes of data:

    Reply from 2001:DB8:ACAD:1::1: bytes=32 time<1ms TTL=254
    Reply from 2001:DB8:ACAD:1::1: bytes=32 time=5ms TTL=254
    Reply from 2001:DB8:ACAD:1::1: bytes=32 time<1ms TTL=254
    Reply from 2001:DB8:ACAD:1::1: bytes=32 time<1ms TTL=254

    Ping statistics for 2001:DB8:ACAD:1::1:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 5ms, Average = 1ms

Выгрузка из CPT [Здесь](https://github.com/feelgood6/labs/blob/4abab065bff9d0ecb2177ce0433bef3e8bb1aee4/labs/labs8/labs8-DHCPV6.pkt)
