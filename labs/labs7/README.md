# Лабораторная работа № 7. Развертывание коммутируемой сети с резервными каналами

## Топология

<img width="768" height="228" alt="image" src="https://github.com/user-attachments/assets/27e33787-f970-4434-a99d-9ccfe1035778" />

## Таблица адресации

<img width="739" height="135" alt="image" src="https://github.com/user-attachments/assets/6986bddf-a4ef-4150-87e4-5d114a6c01f5" />

## Цели

### Часть 1. Создание сети и настройка основных параметров устройства

### Часть 2. Выбор корневого моста

### Часть 3. Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов

### Часть 4. Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов


## Решение

### Часть 1. Создание сети и настройка основных параметров устройства

##### Шаг 1:	Создание сеть согласно топологии.

##### Шаг 2:	Выполните инициализацию и перезагрузку коммутаторов.

##### Шаг 3:	Настройте базовые параметры каждого коммутатора.

##### Шаг 4:	Проверьте связь.

Для хорошего запоминания, на каждом коммутаторе настроил SSH, с S3 подключился к S1 и S2

    S3#ssh -l fg66 192.168.1.1   

    Password: 

    S1>ssh -l fg66 192.168.1.2

    Password: 

    S2#

Подключение работает.


### Часть 2:	Определение корневого моста.

Шаг 1:	Отключите все порты на коммутаторах.

     S2(config)#int range f0/1 - 24,g0/1,g0/2
     S2(config-if-range)#shut

Шаг 2:	Настройте подключенные порты в качестве транковых.

    S2(config)#int range f0/1 - 4
    S2(config-if-range)#switchport mode trunk

Шаг 3:	Включите порты F0/2 и F0/4 на всех коммутаторах.

    S2(config)#int range f0/2,f0/4
    S2(config-if-range)#no shut
    
Шаг 4:	Отобразите данные протокола spanning-tree.

    S1#show spanning-tree 
    VLAN0001
      Spanning tree enabled protocol ieee
      Root ID    Priority    32769
                 Address     0002.17AD.22DA
                 This bridge is the root
                 Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

      Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
                 Address     0002.17AD.22DA
                 Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
                 Aging Time  20

    Interface        Role Sts Cost      Prio.Nbr Type
    ---------------- ---- --- --------- -------- --------------------------------
    Fa0/2            Desg FWD 19        128.2    P2p
    Fa0/4            Desg FWD 19        128.4    P2p



    S2#show spanning-tree 
    VLAN0001
      Spanning tree enabled protocol ieee
      Root ID    Priority    32769
                 Address     0002.17AD.22DA
                 Cost        19
                 Port        2(FastEthernet0/2)
                 Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
    
      Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
                 Address     00E0.F784.9017
                Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
                 Aging Time  20

    Interface        Role Sts Cost      Prio.Nbr Type
    ---------------- ---- --- --------- -------- --------------------------------
    Fa0/4            Altn BLK 19        128.4    P2p
    Fa0/2            Root FWD 19        128.2    P2p



    S3#show sp
    S3#show spanning-tree 
    VLAN0001
      Spanning tree enabled protocol ieee
      Root ID    Priority    32769
                 Address     0002.17AD.22DA
                 Cost        19
                 Port        4(FastEthernet0/4)
                 Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

      Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
                 Address     00D0.D304.8074
                 Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
                 Aging Time  20

    Interface        Role Sts Cost      Prio.Nbr Type
    ---------------- ---- --- --------- -------- --------------------------------
    Fa0/4            Root FWD 19        128.4    P2p
    Fa0/2            Desg FWD 19        128.2    P2p




### Схема

<img width="1666" height="626" alt="image" src="https://github.com/user-attachments/assets/c1b77828-f57c-44bb-9056-4b24f593758b" />

### С учетом выходных данных, поступающих с коммутаторов, ответьте на следующие вопросы.

1.Какой коммутатор является корневым мостом?

S1 , это видно по выводу из команды show spanning-tree show 

     This bridge is the root

    
2. Почему этот коммутатор был выбран протоколом spanning-tree в качестве корневого моста?

    Самый маленький MAC

3. Какие порты на коммутаторе являются корневыми портами?

На S2 это Fa0/2            Root
На S3 это Fa0/4            Root

4. Какие порты на коммутаторе являются назначенными портами?

На S1 - Fa0/2            Desg 
        Fa0/4            Desg 

На S3 -  Fa0/2            Desg


5. Какой порт отображается в качестве альтернативного и в настоящее время заблокирован?

На S2 - Fa0/4            Altn BLK

6. Почему протокол spanning-tree выбрал этот порт в качестве невыделенного (заблокированного) порта?

Есть другой путь к root с меньшей стоимостью

STP оставляет только один логический путь




### Часть 3:	Наблюдение за процессом выбора протоколом STP порта, исходя из стоимости портов


##### Шаг 1:	Определите коммутатор с заблокированным портом.

Из выводов выше видно, что в моем случае это S2.

##### Шаг 2:	Измените стоимость порта.

Как я понял из примера, нужно на коммутаторе с заблокированным портом поменять стоимость не заблокированного порта

    S2(config-if)#spanning-tree vlan 1 cost 18

### Часть 4:	Наблюдение за процессом выбора протоколом STP порта, исходя из приоритета портов

##### Шаг 1.	Включите порты F0/1 и F0/3 на всех коммутаторах.

    S1(config)#int range f0/1 - 4
    и еще на двух коммутаторах

Выводы после включения

    S1#show spanning-tree
    VLAN0001
      Spanning tree enabled protocol ieee
      Root ID    Priority    32769
                 Address     0002.17AD.22DA
                 This bridge is the root
                 Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

      Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
                 Address     0002.17AD.22DA
                 Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
                 Aging Time  20

    Interface        Role Sts Cost      Prio.Nbr Type
    ---------------- ---- --- --------- -------- --------------------------------
    Fa0/1            Desg FWD 19        128.1    P2p
    Fa0/3            Desg FWD 19        128.3    P2p
    Fa0/2            Desg FWD 19        128.2    P2p
    Fa0/4            Desg FWD 19        128.4    P2p


  
    S2#show sp
    S2#show spanning-tree 
    VLAN0001
      Spanning tree enabled protocol ieee
      Root ID    Priority    32769
                 Address     0002.17AD.22DA
                 Cost        18
                 Port        2(FastEthernet0/2)
                 Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

      Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
                 Address     00E0.F784.9017
                 Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
                 Aging Time  20

    Interface        Role Sts Cost      Prio.Nbr Type
    ---------------- ---- --- --------- -------- --------------------------------
    Fa0/1            Altn BLK 19        128.1    P2p
    Fa0/4            Desg FWD 19        128.4    P2p
    Fa0/2            Root FWD 18        128.2    P2p
    Fa0/3            Desg FWD 19        128.3    P2p



    S3#show spanning-tree 
    VLAN0001
      Spanning tree enabled protocol ieee
      Root ID    Priority    32769
                 Address     0002.17AD.22DA
                 Cost        19
                 Port        3(FastEthernet0/3)
                 Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

      Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
                 Address     00D0.D304.8074
                 Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
                 Aging Time  20

    Interface        Role Sts Cost      Prio.Nbr Type
    ---------------- ---- --- --------- -------- --------------------------------
    Fa0/1            Altn BLK 19        128.1    P2p
    Fa0/4            Altn BLK 19        128.4    P2p
    Fa0/2            Altn BLK 19        128.2    P2p
    Fa0/3            Root FWD 19        128.3    P2p


Какой порт выбран протоколом STP в качестве порта корневого моста на каждом коммутаторе некорневого моста? 

На S2 выбрал тот порт, у которого мы понизили стоимость 

    Fa0/2            Root FWD

На S3 

    Fa0/3            Root FWD

    

### 	Вопросы для повторения

1.	Какое значение протокол STP использует первым после выбора корневого моста, чтобы определить выбор порта?

STP смотрит стоимость портов (Path Cost)
   
2.	Если первое значение на двух портах одинаково, какое следующее значение будет использовать протокол STP при выборе порта?
   
Если стоимости портов равны, процесс сравнивает BID.

    Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)


3.	Если оба значения на двух портах равны, каким будет следующее значение, которое использует протокол STP при выборе порта?

   
Если BID равны, для определения корневого моста используются приоритеты портов. Наиболее низкие значения являются предпочтительными. 



Выгрузка из CPT [Здесь](https://github.com/feelgood6/labs/blob/76c34c16e588e97426c889c8df6cb394831c9e12/labs/labs7/labs7.pkt)
















