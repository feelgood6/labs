
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

### Часть 5. Настройте удаленный доступ

##### Шаг 1. Настройте все сетевые устройства для базовой поддержки SSH.

##### Шаг 2. Включите защищенные веб-службы с проверкой подлинности на R1.

### Часть 6. Проверка подключения

##### Шаг 1. Настройте узлы ПК.

##### Шаг 2. Выполните следующие тесты. Эхозапрос должен пройти успешно.

<img width="856" height="350" alt="image" src="https://github.com/user-attachments/assets/d83c3f3f-6f87-4046-8896-b8f1df1e5ff4" />

### Часть 7. Настройка и проверка списков контроля доступа (ACL)

##### Шаг 1. Проанализируйте требования к сети и политике безопасности для планирования реализации ACL.

##### Шаг 2. Разработка и применение расширенных списков доступа, которые будут соответствовать требованиям политики безопасности.

##### Шаг 3. Убедитесь, что политики безопасности применяются развернутыми списками доступа.


<img width="872" height="364" alt="image" src="https://github.com/user-attachments/assets/da813443-379d-483d-8d3c-3255706a0da1" />






























