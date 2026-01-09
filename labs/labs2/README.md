# Лабораторная работа. Просмотр таблицы MAC-адресов коммутатора 
##  Задание:

### Часть 1. Создание и настройка сети
### Часть 2. Изучение таблицы МАС-адресов коммутатора

## Топология.
<img width="501" height="295" alt="image" src="https://github.com/user-attachments/assets/402ef084-c1eb-440e-94a7-271c89b44d14" />

## Таблица адресации.

| Устройство|       Интерфейс          |     IP-адрес / префикс         |      Маска подсети             |
|-----------|--------------------------|--------------------------------|--------------------------------|
|    S1     |         VLAN 1           |      192.168.1.11              |      255.255.255.0             |
|    S2     |         VLAN 1           |      192.168.1.12              |      255.255.255.0             |
|    PC-A   |           NIC            |      192.168.1.1               |      255.255.255.0             |
|    PC-B   |           NIC            |      192.168.1.2               |      255.255.255.0             |
	
##  Решение

### Часть 1. Создание и настройка сети

##### Шаг 1.Подключите сеть в соответствии с топологией.

Берем 2 коммутатора и 2 хоста, подключаем их согласно топологии.

##### Шаг 2. Настройте узлы ПК.

Заходим в IP Configuration ПК и присваиваем IP адреса согласно таблицы адресации.

##### Шаг 3. Выполните инициализацию и перезагрузку коммутаторов.

С помощью консольного кабеля подключаемся к коммутаторам, я сначала с PC-A подключился к S1 , потом с PC-B к  S2, к каждому коммутатору выполнял одинаковые команды.

	Switch>enable 
	Switch#erase startup-config 
	Erasing the nvram filesystem will remove all configuration files! Continue? [confirm]
	Switch#reload
	Proceed with reload? [confirm]
		
##### Шаг 4. Настройте базовые параметры каждого коммутатора.

###### a. Настройте имена устройств в соответствии с топологией.

Также отключу поиск по доменному имени.

	Switch>enable
	Switch#configure terminal
	Switch(config)#hostname S1
	S1(config)#no ip domain-lookup 

###### b. Настройте IP-адреса, как указано в таблице адресации.

Так как мы уже в режиме кофигурации, тут мы вводим.

	S1(config)#interface vlan 1
	S1(config-if)#ip address 192.168.1.11 255.255.255.0
		
###### c. Назначьте cisco в качестве паролей консоли и VTY

	S1(config)#line vty 0 4
	S1(config-line)#password cisco
	S1(config-line)#login
	
Также хочу спрятать пароли командой service password-encryption.

	S1(config-line)#exit
	S1(config)#service password-encryption

		
###### d. Назначьте class в качестве пароля доступа к привилегированному режиму EXEC.

	S1(config)#enable secret class




### Часть 2. Изучение таблицы МАС-адресов коммутатора


##### Шаг 1. Запишисываем МАС-адреса сетевых устройств.

| Устройство|        MAC-адрес         |
|-----------|--------------------------|
|    S1     |     0060.5CC8.C901       |
|    S2     |     00E0.8F8C.5201       |
|    PC-A   |     0006.2ADB.BC59       |
|    PC-B   |     00D0.BA19.0A74       |



##### Шаг 2. Просмотрите таблицу МАС-адресов коммутатора.

	S2>enable 
	Password:
	S2#show mac address-table
				Mac Address Table
	-------------------------------------------

	Vlan    Mac Address       Type        Ports
	----    -----------       --------    -----

   	1      0060.5cc8.c901    DYNAMIC     Fa0/1



Просмотр таблицы MAC-адресов показал только адрес второго коммутатора.
###### Вопрос:
Если вы не записали МАС-адреса сетевых устройств в шаге 1, как можно определить, каким устройствам принадлежат МАС-адреса, используя только выходные данные команды show mac address-table?

###### Ответ: 

Я думаю, что можно по порту коммутатора, в данном случае это Fa0/1, мы подключали к нему коммутатор, согласно топологии.

###### Вопрос:

Работает ли это решение в любой ситуации?

###### Ответ: 
Нет, на один порт могут быть записаны несколько MAC-адресов, если будет >1 , то тогда не получится. MAC-адреса могут быть динамическими и со временем удаляться из таблицы.


##### Шаг 3. Очистить таблицу МАС-адресов коммутатора S2 и снова отобразите таблицу МАС-адресов.

	S2>enable 
	Password:
	S2#clear mac address-table dynamic
	S2#show mac address-table 
         	 Mac Address Table
	-------------------------------------------

	Vlan    Mac Address       Type        Ports
	----    -----------       --------    -----
	
Ввод через 10 секунд

	
	S2#show mac address-table 
          	Mac Address Table
	-------------------------------------------

	Vlan    Mac Address       Type        Ports
	----    -----------       --------    -----

   	1    0060.5cc8.c901    DYNAMIC     Fa0/1


Снова появился адрес коммутатора.



##### Шаг 4. С компьютера PC-B отправьте эхо-запросы устройствам в сети и просмотрите таблицу МАС-адресов коммутатора.

 

###### a. На компьютере PC-B открываем командную строку и вводим команду arp -a.
 Результат: 

	C:\>arp -a
	No ARP Entries Found

###### b.	Из командной строки PC-B отправляем эхо-запросы на компьютер PC-A, а также коммутаторы S1 и S2.


	C:\>ping 192.168.1.1

	Pinging 192.168.1.1 with 32 bytes of data:
	
	Reply from 192.168.1.1: bytes=32 time<1ms TTL=128
	Reply from 192.168.1.1: bytes=32 time<1ms TTL=128
	Reply from 192.168.1.1: bytes=32 time<1ms TTL=128
	Reply from 192.168.1.1: bytes=32 time<1ms TTL=128

	Ping statistics for 192.168.1.1:
   		Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
	Approximate round trip times in milli-seconds:
    	Minimum = 0ms, Maximum = 0ms, Average = 0ms

	C:\>ping 192.168.1.12

	Pinging 192.168.1.12 with 32 bytes of data:

	Request timed out.
	Reply from 192.168.1.12: bytes=32 time<1ms TTL=255
	Reply from 192.168.1.12: bytes=32 time<1ms TTL=255
	Reply from 192.168.1.12: bytes=32 time<1ms TTL=255

	Ping statistics for 192.168.1.12:
    	Packets: Sent = 4, Received = 3, Lost = 1 (25% loss),
	Approximate round trip times in milli-seconds:
    	Minimum = 0ms, Maximum = 0ms, Average = 0ms
	
	
	C:\>ping 192.168.1.11

	Pinging 192.168.1.11 with 32 bytes of data:

	Request timed out.
	Reply from 192.168.1.11: bytes=32 time<1ms TTL=255

	Ping statistics for 192.168.1.11:
    	Packets: Sent = 2, Received = 1, Lost = 1 (50% loss),
	Approximate round trip times in milli-seconds:
    	Minimum = 0ms, Maximum = 0ms, Average = 0ms



###### c.Подключившись через консоль к коммутатору S2, введите команду show mac address-table.

	S2#show mac address-table 
         	 Mac Address Table
	-------------------------------------------

	Vlan    Mac Address       Type        Ports
	----    -----------       --------    -----

  	 1    0030.f262.338a    DYNAMIC     Fa0/1
   	 1    0060.5cc8.c901    DYNAMIC     Fa0/1
   	 1    00d0.ba19.0a74    DYNAMIC     Fa0/18

Коммутатор добавил в таблицу дополнительные MAC-адрес

На компьютере PC-B открываю командную строку и еще раз ввожу команду arp -a.

	C:\>arp -a
  	Internet Address      Physical Address      Type
  	192.168.1.1           0006.2adb.bc59        dynamic
  	192.168.1.11          0030.f262.338a        dynamic
  	192.168.1.12          0009.7c4d.dd8a        dynamic



		

#### Вопрос для повторения
В сетях Ethernet данные передаются на устройства по соответствующим МАС-адресам. Для этого коммутаторы и компьютеры динамически создают ARP-кэш и таблицы МАС-адресов. Если компьютеров в сети немного, эта процедура выглядит достаточно простой. Какие сложности могут возникнуть в крупных сетях?

#### Ответ.
В крупных Ethernet-сетях динамическое создание ARP-кэша и таблиц MAC-адресов может приводить к увеличению широковещательного трафика, переполнению таблиц коммутаторов, снижению производительности, проблемам безопасности и усложнению администрирования сети.


