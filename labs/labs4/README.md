
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
    R1(config-line)#exec-timeout
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
