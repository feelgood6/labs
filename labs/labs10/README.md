# Лабораторная работа N10. Настройка протокола OSPFv2 для одной области

### Топология

<img width="701" height="140" alt="image" src="https://github.com/user-attachments/assets/2e3e11ce-e864-4a59-bf8a-d7d359551199" />

### Таблица адресации

<img width="682" height="151" alt="image" src="https://github.com/user-attachments/assets/f53c89cc-6860-468f-8c32-163a4661e1e1" />


### Цели

##### Часть 1. Создание сети и настройка основных параметров устройства
##### Часть 2. Настройка и проверка базовой работы протокола  OSPFv2 для одной области
##### Часть 3. Оптимизация и проверка конфигурации OSPFv2 для одной области

### Решение

### Часть 1. Создание сети и настройка основных параметров устройства

##### Шаг 1. Создайте сеть согласно топологии.
##### Шаг 2. Произведите базовую настройку маршрутизаторов.
##### Шаг 3. Настройте базовые параметры каждого коммутатора.


### Часть 2. Настройка и проверка базовой работы протокола OSPFv2 для одной области

##### Шаг 1. Настройте адреса интерфейса и базового OSPFv2 на каждом маршрутизаторе.

a.	Настройте адреса интерфейсов на каждом маршрутизаторе, как показано в таблице адресации выше.

На R1

    R1(config)#int g0/0/1
    R1(config-if)#ip address 10.53.0.1 255.255.255.0
    R1(config-if)#no sh

    R1(config)#int loopback 1
    R1(config-if)#ip address 172.16.1.1 255.255.255.0

На R2

    R2(config)#int g0/0/1
    R2(config-if)#ip address 10.53.0.2 255.255.255.0
    R2(config-if)#no sh

    R2(config)#int loopback1
    R2(config-if)#ip address 192.168.1.1 255.255.255.0
  
  
b.	Перейдите в режим конфигурации маршрутизатора OSPF, используя идентификатор процесса 56.
c.	Настройте статический идентификатор маршрутизатора для каждого маршрутизатора (1.1.1.1 для R1, 2.2.2.2 для R2).

На R1

    R1(config)#router ospf 56
    R1(config-router)#router-id 1.1.1.1
  
На R2

    R2(config)#router ospf 56
    R2(config-router)#router-id 2.2.2.2



d.	Настройте инструкцию сети для сети между R1 и R2, поместив ее в область 0.

На R1

    R1(config)#int g0/0/1
    R1(config-if)#ip ospf 56 area 0

На R2

    R2(config)#int g0/0/1
    R2(config-if)#ip ospf 56 area 0




e.	Только на R2 добавьте конфигурацию, необходимую для объявления сети Loopback 1 в область OSPF 0.

    R2(config)#int loopback1
    R2(config-if)#ip ospf 56 area 0

f.	Убедитесь, что OSPFv2 работает между маршрутизаторами. Выполните команду, чтобы убедиться, что R1 и R2 сформировали смежность.

    R2#show ip ospf neighbor


    Neighbor ID     Pri   State           Dead Time   Address         Interface
    1.1.1.1           1   FULL/BDR        00:00:37    10.53.0.1       GigabitEthernet0/0/1

    R1#show ip ospf neighbor


    Neighbor ID     Pri   State           Dead Time   Address         Interface
    2.2.2.2           1   FULL/DR         00:00:30    10.53.0.2       GigabitEthernet0/0/1
  

  
Вопрос:
Какой маршрутизатор является DR? Какой маршрутизатор является BDR? Каковы критерии отбора?

  DR - тот, у кого выше Router ID
  BDR - второй по величине

  В моем случает, BDR - R1, DR - R2


g.	На R1 выполните команду show ip route ospf, чтобы убедиться, что сеть R2 Loopback1 присутствует в таблице маршрутизации. Обратите внимание, что поведение OSPF по умолчанию заключается в объявлении интерфейса обратной связи в качестве маршрута узла с использованием 32-битной маски.

    R1#show ip route ospf
         192.168.1.0/32 is subnetted, 1 subnets
    O       192.168.1.1 [110/2] via 10.53.0.2, 00:01:30, GigabitEthernet0/0/1

h.	Запустите Ping до  адреса интерфейса R2 Loopback 1 из R1. Выполнение команды ping должно быть успешным.

    R1#ping 192.168.1.1

    Type escape sequence to abort.
    Sending 5, 100-byte ICMP Echos to 192.168.1.1, timeout is 2 seconds:
    !!!!!
    Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/0 ms


### Часть 3. Оптимизация и проверка конфигурации OSPFv2 для одной области

##### Шаг 1. Реализация различных оптимизаций на каждом маршрутизаторе.

a.	На R1 настройте приоритет OSPF интерфейса G0/0/1 на 50, чтобы убедиться, что R1 является назначенным маршрутизатором.

        R1(config)#int g0/0/1
        R1(config-if)#ip ospf cost 50
    
b.	Настройте таймеры OSPF на G0/0/1 каждого маршрутизатора для таймера приветствия, составляющего 30 секунд.

    R1(config-if)#ip ospf hello-interval 30
    R1(config-if)#ip ospf dead-interval 120

    R2(config-if)#ip ospf hello-interval 30
    R2(config-if)#ip ospf dead-interval 120

    


c.	На R1 настройте статический маршрут по умолчанию, который использует интерфейс Loopback 1 в качестве интерфейса выхода. Затем распространите маршрут по умолчанию в OSPF. Обратите внимание на сообщение консоли после установки маршрута по умолчанию.

    R1(config)#ip route 0.0.0.0 0.0.0.0 loopback1
    R1(config)#router ospf 56
    R1(config-router)#default-information originate 
    


d.	добавьте конфигурацию, необходимую для OSPF для обработки R2 Loopback 1 как сети точка-точка. Это приводит к тому, что OSPF объявляет Loopback 1 использует маску подсети интерфейса.

    R2(config)#int loopback1
    R2(config-if)#ip ospf network point-to-point 


e.	Только на R2 добавьте конфигурацию, необходимую для предотвращения отправки объявлений OSPF в сеть Loopback 1.

    R2(config)#router ospf 56
    R2(config-router)#passive-interface loopback1

f.	Измените базовую пропускную способность для маршрутизаторов. После этой настройки перезапустите OSPF с помощью команды clear ip ospf process . Обратите внимание на сообщение консоли после установки новой опорной полосы пропускания.

    R1(config)#router ospf 56
    R1(config-router)#auto-cost reference-bandwidth 10000

    R2(config)#router ospf 56
    R2(config-router)#auto-cost reference-bandwidth 10000

##### Шаг 2. Убедитесь, что оптимизация OSPFv2 реализовалась.

a.	Выполните команду show ip ospf interface g0/0/1 на R1 и убедитесь, что приоритет интерфейса установлен равным 50, а временные интервалы — Hello 30, Dead 120, а тип сети по умолчанию — Broadcast

    R1#show ip ospf interface g0/0/1 

    GigabitEthernet0/0/1 is up, line protocol is up
      Internet address is 10.53.0.1/24, Area 0
      Process ID 56, Router ID 1.1.1.1, Network Type BROADCAST, Cost: 50
      Transmit Delay is 1 sec, State BDR, Priority 1
      Designated Router (ID) 2.2.2.2, Interface address 10.53.0.2
      Backup Designated Router (ID) 1.1.1.1, Interface address 10.53.0.1
      Timer intervals configured, Hello 30, Dead 120, Wait 120, Retransmit 5
        Hello due in 00:00:11
      Index 1/1, flood queue length 0
      Next 0x0(0)/0x0(0)
      Last flood scan length is 1, maximum is 1
      Last flood scan time is 0 msec, maximum is 0 msec
      Neighbor Count is 1, Adjacent neighbor count is 1
        Adjacent with neighbor 2.2.2.2  (Designated Router)
      Suppress hello for 0 neighbor(s)

b.	На R1 выполните команду show ip route ospf, чтобы убедиться, что сеть R2 Loopback1 присутствует в таблице маршрутизации. Обратите внимание на разницу в метрике между этим выходным и предыдущим выходным. Также обратите внимание, что маска теперь составляет 24 бита, в отличие от 32 битов, ранее объявленных.

    R1#show ip route ospf
    O    192.168.1.0 [110/51] via 10.53.0.2, 00:04:19, GigabitEthernet0/0/1


c.	Введите команду show ip route ospf на маршрутизаторе R2. Единственная информация о маршруте OSPF должна быть распространяемый по умолчанию маршрут R1.

    R2#show ip route ospf

    ничего не показало, забыл ввести команду clear ip ospf process

     R2#show ip route ospf
     O*E2 0.0.0.0/0 [110/1] via 10.53.0.1, 00:00:14, GigabitEthernet0/0/1

d.	Запустите Ping до адреса интерфейса R1 Loopback 1 из R2. Выполнение команды ping должно быть успешным.

    R2#ping 172.16.1.1

    Type escape sequence to abort.
    Sending 5, 100-byte ICMP Echos to 172.16.1.1, timeout is 2 seconds:
    !!!!!
    Success rate is 100 percent (5/5), round-trip min/avg/max = 0/0/0 ms


##### Вопрос:
Почему стоимость OSPF для маршрута по умолчанию отличается от стоимости OSPF в R1 для сети 192.168.1.0/24?

Это внешний маршрут со своей метрикой, обозначается как Е2. Е2 не меняет свою стоимость при переходе, а стоимость в R1 для сети 192.168.1.0/24 OSPF высчитывает сам. Вы так говорили во второй части на 1:30:25. Вернее я так понял с ваших слов :)

Выгруска из CPT [Здесь](https://github.com/feelgood6/labs/blob/00b53513c725efdad64c998853b2c7e90eb7edd6/labs/labs10/labs10.pkt)
























































