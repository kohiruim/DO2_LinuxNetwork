# Сети в Linux

Настройка сетей в Linux на виртуальных машинах.

## Contents

3.1. [Инструмент ipcalc](#part-1-инструмент-ipcalc) \
 3.2. [Статическая маршрутизация между двумя машинами](#part-2-статическая-маршрутизация-между-двумя-машинами) \
 3.3. [Утилита iperf3](#part-3-утилита-iperf3) \
 3.4. [Сетевой экран](#part-4-сетевой-экран) \
 3.5. [Статическая маршрутизация сети](#part-5-статическая-маршрутизация-сети) \
 3.6. [Динамическая настройка IP с помощью DHCP](#part-6-динамическая-настройка-ip-с-помощью-dhcp) \
 3.7. [NAT](#part-7-nat) \
 3.8. [Допополнительно. Знакомство с SSH Tunnels](#part-8-дополнительно-знакомство-с-ssh-tunnels)

## Part 1. Инструмент **ipcalc**

#### 1.1. Сети и маски

##### 1) Адрес сети _192.167.38.54/13_ - _192.160.0.0_

![linux_network](img/part1.1.1.jpg)

##### 2.1) Перевод маски _255.255.255.0_ в префиксную и двоичную запись

-   Префиксная запись: _/24_
-   Двоичная запись: _11111111.11111111.11111111.00000000_

![linux_network](img/part1.1.2.1.jpg)

##### 2.2) Перевод _/15_ в обычную и двоичную запись

-   Обычная запись: _255.254.0.0_
-   Двоичная запись: _11111111.11111110.00000000.00000000_

![linux_network](img/part1.1.2.2.jpg)

##### 2.3) Перевод _11111111.11111111.11111111.11110000_ в обычную и префиксную запись

-   Обычная запись: _255.255.255.240_
-   Префиксная запись: _/28_

![linux_network](img/part1.1.2.3.jpg)

##### 3.1) Минимальный и максимальный хост в сети _12.167.38.4_ при маскe: _/8_

-   HostMin: _12.0.0.1_
-   HostMax: _12.255.255.254_

![linux_network](img/part1.1.3.1.jpg)

##### 3.2) Минимальный и максимальный хост в сети _12.167.38.4_ при маскe: _11111111.11111111.00000000.00000000_

-   HostMin: _12.167.0.1_
-   HostMax: _12.167.255.254_

![linux_network](img/part1.1.3.2.jpg)

##### 3.3) Минимальный и максимальный хост в сети _12.167.38.4_ при маскe: _255.255.254.0_

-   HostMin: _12.167.38.1_
-   HostMax: _12.167.39.254_

![linux_network](img/part1.1.3.3.jpg)

##### 3.4) Минимальный и максимальный хост в сети _12.167.38.4_ при маскe: _/4_

-   HostMin: _0.0.0.1_
-   HostMax: _15.255.255.254_

![linux_network](img/part1.1.3.4.jpg)

#### 1.2. localhost

##### Определить и записать в отчёт, можно ли обратиться к приложению, работающему на localhost, со следующими IP: можно, где есть loopback

-   _194.34.23.100_ - нет
-   _127.0.0.2_ - да
-   _127.1.0.1_ - да
-   _128.0.0.1_ - нет

![linux_network](img/part1.1.2.jpg)

#### 1.3. Диапазоны и сегменты сетей

##### 1) какие из перечисленных IP можно использовать в качестве публичного, а какие только в качестве частных: _10.0.0.45_, _134.43.0.2_, _192.168.4.2_, _172.20.250.4_, _172.0.2.1_, _192.172.0.1_, _172.68.0.2_, _172.16.255.255_, _10.10.10.10_, _192.169.168.1_

-   Публичный) _134.43.0.2_, _172.0.2.1_, _192.172.0.1_, _172.68.0.2_, _192.169.168.1_
-   Частный) _10.0.0.45_, _192.168.4.2_, _172.20.250.4_, _172.16.255.255_, _10.10.10.10_  
    _У частных адресов в ipcalc "Private Internet"_

##### 2) какие из перечисленных IP адресов шлюза возможны у сети _10.10.0.0/18_: _10.0.0.1_, _10.10.0.2_, _10.10.10.10_, _10.10.100.1_, _10.10.1.255_

-   10.10.0.2, 10.10.10.10, 10.10.1.255

## Part 2. Статическая маршрутизация между двумя машинами

##### Поднять две виртуальные машины ws1 и ws2

##### С помощью команды `ip a` посмотреть существующие сетевые интерфейсы

![linux_network](img/part2a.jpg)

##### Описать сетевой интерфейс, соответствующий внутренней сети, на обеих машинах и задать следующие адреса и маски: ws1 - _192.168.100.10_, маска _/16_, ws2 - _172.24.116.8_, маска _/12_

-   В отчёт поместить скрины с содержанием изменённого файла _etc/netplan/00-installer-config.yaml_ для каждой машины.
    ![linux_network](img/part2b1.jpg)
    ![linux_network](img/part2b2.jpg)

##### Выполнить команду `netplan apply` для перезапуска сервиса сети

-   В отчёт поместить скрин с вызовом и выводом использованной команды.
    ![linux_network](img/part2c1.jpg)
    ![linux_network](img/part2c2.jpg)

#### 2.1. Добавление статического маршрута вручную

##### Добавить статический маршрут от одной машины до другой и обратно при помощи команды вида `ip r add` и пропинговать соединение между машинами

![linux_network](img/part2.1.jpg)

#### 2.2. Добавление статического маршрута с сохранением

##### Перезапустить машины. Добавить статический маршрут от одной машины до другой с помощью файла _etc/netplan/00-installer-config.yaml_. Пропинговать соединение между машинами.

![linux_network](img/part2.2.1.jpg)
![linux_network](img/part2.2.2.jpg)

## Part 3. Утилита **iperf3**

#### 3.1. Скорость соединения

##### Перевести и записать в отчёт: 8 Mbps в MB/s, 100 MB/s в Kbps, 1 Gbps в Mbps

8 Mbps = 1MB/s, 100 MB/s = 100000Kbps, 1 Gbps = 1024Mbps

#### 3.2. Утилита **iperf3**

##### Измерить скорость соединения между ws1 и ws2

![linux_network](img/part3.2.jpg)

## Part 4. Сетевой экран

#### 4.1. Утилита **iptables**

##### Создать файл _/etc/firewall.sh_, имитирующий фаерволл, на ws1 и ws2:

##### Нужно добавить в файл подряд следующие правила:

1. на ws1 применить стратегию когда в начале пишется запрещающее правило, а в конце пишется разрешающее правило (это касается пунктов 4 и 5)
2. на ws2 применить стратегию когда в начале пишется разрешающее правило, а в конце пишется запрещающее правило (это касается пунктов 4 и 5)
3. открыть на машинах доступ для порта 22 (ssh) и порта 80 (http)
4. запретить _echo reply_ (машина не должна "пинговаться”, т.е. должна быть блокировка на OUTPUT)
5. разрешить _echo reply_ (машина должна "пинговаться")

-   В отчёт поместить скрины с содержанием файла _/etc/firewall_ для каждой машины.  
    ![linux_network](img/part4.1.1a.jpg)
    ![linux_network](img/part4.1.1b.jpg)

##### Запустить файлы на обеих машинах командами `chmod +x /etc/firewall.sh` и `/etc/firewall.sh`

![linux_network](img/part4.1.2a.jpg)
![linux_network](img/part4.1.2b.jpg)

-   Описать разницу между стратегиями, применёнными в первом и втором файлах:  
    Правила выполняются сверху вниз. Когда запрет находиться выше чем разрешение, срабатывает запрет, машины не пингуются.

#### 4.2. Утилита **nmap**

##### Командой **ping** найти машину, которая не "пингуется", после чего утилитой **nmap** показать, что хост машины запущен _Проверка: в выводе nmap должно быть сказано: `Host is up`_

![linux_network](img/part4.2a.jpg)
![linux_network](img/part4.2b.jpg)

## Part 5. Статическая маршрутизация сети

#### 5.1. Настройка адресов машин

##### Настроить конфигурации машин в _etc/netplan/00-installer-config.yaml_ согласно сети на рисунке.

##### Перезапустить сервис сети. Если ошибок нет, то командой `ip -4 a` проверить, что адрес машины задан верно. Также пропинговать ws22 с ws21. Аналогично пропинговать r1 с ws11.

![linux_network](img/example.png)

-   файлы etc/netplan/00-installer-config.yaml для каждой машины:  
    ![linux_network](img/part5_netplanR1.jpg)
    ![linux_network](img/part5_netplanR2.jpg)
    ![linux_network](img/part5_netplanWS21.jpg)
    ![linux_network](img/part5_netplanWS22.jpg)
    ![linux_network](img/part5_netplanWS11.jpg)

-   `ip -4 a`:  
    ![linux_network](img/part5_ip4aR1.jpg)
    ![linux_network](img/part5_ip4aR2.jpg)
    ![linux_network](img/part5_ip4aWS11.jpg)
    ![linux_network](img/part5_ip4aWS21.jpg)
    ![linux_network](img/part5_ip4aWS22.jpg)

-   Пропинговать ws22 с ws21.  
    ![linux_network](img/part5_WS22-WS21.jpg)
-   Пропинговать r1 с ws11.  
    ![linux_network](img/part5_R1-WS11.jpg)

#### 5.2. Включение переадресации IP-адресов.

##### Для включения переадресации IP, выполните команду на роутерах:

![linux_network](img/part5.2R1.jpg)
![linux_network](img/part5.2R2.jpg)

##### Откройте файл _/etc/sysctl.conf_ и добавьте в него следующую строку: `net.ipv4.ip_forward = 1`

![linux_network](img/part5.2_sysctl.jpg)

#### 5.3. Установка маршрута по-умолчанию

##### Настроить маршрут по-умолчанию (шлюз) для рабочих станций. Для этого добавить gateway4 \[ip роутера\] в файле конфигураций

-   В отчёт поместить скрин с содержанием файла _etc/netplan/00-installer-config.yaml_.  
    ![linux_network](img/part5.3WS11.jpg)
    ![linux_network](img/part5.3WS21.jpg)
    ![linux_network](img/part5.3WS22.jpg)
    ![linux_network](img/part5.3R1.jpg)
    ![linux_network](img/part5.3R2.jpg)

##### Вызвать `ip r` и показать, что добавился маршрут в таблицу маршрутизации

![linux_network](img/part5.3_ipWS.jpg)

##### Пропинговать с ws11 роутер r2 и показать на r2, что пинг доходит. Для этого использовать команду: `tcpdump -tn -i eth1`

![linux_network](img/part5.3_ping.jpg)

#### 5.4. Добавление статических маршрутов

##### Добавить в роутеры r1 и r2 статические маршруты в файле конфигураций.

![linux_network](img/part5.4_netplanR.jpg)

##### Вызвать `ip r` и показать таблицы с маршрутами на обоих роутерах. Пример таблицы на r1:

![linux_network](img/part5.4_ipr.jpg)

##### Запустить команды на ws11:

![linux_network](img/part5.4_iprWS11.jpg)

-   В отчёте объяснить, почему для адреса 10.10.0.0/\[порт сети\] был выбран маршрут, отличный от 0.0.0.0/0, хотя он попадает под маршрут по-умолчанию: он является адресом сети и доступен без шлюза, 0.0.0.0 - это 127.0.0.1

#### 5.5. Построение списка маршрутизаторов

##### Запустить на r1 команду дампа: `tcpdump -tnv -i eth0`

![linux_network](img/part5.5_tcpdumpR1.jpg)

##### При помощи утилиты **traceroute** построить список маршрутизаторов на пути от ws11 до ws21

-   В отчёт поместить скрины с вызовом и выводом использованных команд (tcpdump и traceroute).  
    ![linux_network](img/part5.5_tracerouteWS11.jpg)
-   В отчёте, опираясь на вывод, полученный из дампа на r1, объяснить принцип работы построения пути при помощи **traceroute**.

Для определения промежуточных маршрутизаторов traceroute отправляет серию пакетов данных целевому узлу, при этом каждый раз увеличивая на 1 значение поля TTL («время жизни»). Это поле обычно указывает максимальное количество маршрутизаторов, которое может быть пройдено пакетом. Traceroute фиксирует адрес маршрутизатора, а также время между отправкой пакета и получением ответа (эти сведения выводятся на монитор компьютера). Затем traceroute повторяет отправку пакета, но уже с TTL, равным 2, что позволяет первому маршрутизатору пропустить пакет дальше.

Процесс повторяется до тех пор, пока при определённом значении TTL пакет не достигнет целевого узла. При получении ответа от этого узла процесс трассировки считается завершённым.

Достижение пункта назначения определяется: отсылаемые traceroute дейтаграммы содержат UDP-пакет с таким номером UDP-порта адресата (превышающим 30000), что он заведомо не используется на адресуемом хосте. В пункте назначения UDP-модуль, получая подобные дейтаграммы, возвращает ICMP-сообщения об ошибке «порт недоступен». Таким образом, чтобы узнать о завершении работы, программе traceroute достаточно обнаружить, что поступило ICMP-сообщение об ошибке этого типа.

#### 5.6. Использование протокола **ICMP** при маршрутизации

##### Запустить на r1 перехват сетевого трафика, проходящего через eth0 с помощью команды: `tcpdump -n -i eth0 icmp`. Пропинговать с ws11 несуществующий IP (например, _10.30.0.111_) с помощью команды: `ping -c 1 10.30.0.111`

![linux_network](img/part5.6.jpg)

## Part 6. Динамическая настройка IP с помощью **DHCP**

##### 1) Для r2 настроить в файле _/etc/dhcp/dhcpd.conf_ конфигурацию службы **DHCP**: указать адрес маршрутизатора по-умолчанию, DNS-сервер и адрес внутренней сети.

![linux_network](img/part6.1.jpg)

##### 2) в файле _resolv.conf_ прописать `nameserver 8.8.8.8.`

![linux_network](img/part6.2.jpg)

##### Перезагрузить службу **DHCP** командой `systemctl restart isc-dhcp-server`.

![linux_network](img/part6_restart.jpg)

-   Машину ws21 перезагрузить при помощи `reboot` и через `ip a` показать, что она получила адрес.  
    ![linux_network](img/part6_restartWS21.jpg)
-   Пропинговать ws22 с ws21.  
    ![linux_network](img/part6_pingWS22-WS21.jpg)

##### Указать MAC адрес у ws11, для этого в _etc/netplan/00-installer-config.yaml_ надо добавить строки: `macaddress: 10:10:10:10:10:BA`, `dhcp4: true`

![linux_network](img/part6.2_mac.jpg)

##### Для r1 настроить аналогично r2, но сделать выдачу адресов с жесткой привязкой к MAC-адресу (ws11). Провести аналогичные тесты

![linux_network](img/part6_resolvR1.jpg)
![linux_network](img/part6_dhcpR1.jpg)

-   Перезагрузить службу **DHCP** командой `systemctl restart isc-dhcp-server`. Пропинговать ws22 с ws21.
    ![linux_network](img/part6_pingWS22-WS21.jpg)

##### Запросить с ws21 обновление ip адреса

-   В отчёте поместить скрины ip до и после обновления.  
    ![linux_network](img/part6_ipaWS21.jpg)
-   В отчёте описать, какими опциями **DHCP** сервера пользовались в данном пункте.  
    dhclient -r enp0s3 освобождает текущий адрес интерфейса enp0s3.  
    dhclient enp0s3 задает новый адрес указанному интерфейсу.

## Part 7. **NAT**

##### В файле _/etc/apache2/ports.conf_ на ws22 и r2 изменить строку `Listen 80` на `Listen 0.0.0.0:80`, то есть сделать сервер Apache2 общедоступным

![linux_network](img/part7_portsR1.jpg)
![linux_network](img/part7_portsWS22.jpg)

##### Запустить веб-сервер Apache командой `service apache2 start` на ws22 и r1

![linux_network](img/part7_apacheStart.jpg)

##### Добавить в фаервол, созданный по аналогии с фаерволом из Части 4, на r2 следующие правила:

1. Удаление правил в таблице filter - `iptables -F`
2. Удаление правил в таблице "NAT" - `iptables -F -t nat`
3. Отбрасывать все маршрутизируемые пакеты - `iptables --policy FORWARD DROP`  
   ![linux_network](img/part7_firewall1.jpg)

##### Проверить соединение между ws22 и r1 командой `ping`

![linux_network](img/part7_pingWS22-R1.jpg)

##### Добавить в файл ещё одно правило:

4. Разрешить маршрутизацию всех пакетов протокола **ICMP**  
   ![linux_network](img/part7_icmpAC.jpg)

##### Проверить соединение между ws22 и r1 командой `ping`

_При запуске файла с этими правилами, ws22 должна "пинговаться" с r1_
![linux_network](img/part7_pingWS22-R1icmpA.jpg)

##### Добавить в файл ещё два правила:

5. Включить **SNAT**, а именно маскирование всех локальных ip из локальной сети, находящейся за r2 (по обозначениям из Части 5 - сеть 10.20.0.0)
6. Включить **DNAT** на 8080 порт машины r2 и добавить к веб-серверу Apache, запущенному на ws22, доступ извне сети
   ![linux_network](img/part7_SNAT.jpg)

##### Запускать файл также, как в Части 4

![linux_network](img/part7_chmodSH.jpg)

##### Проверить соединение по TCP для **SNAT**, для этого с ws22 подключиться к серверу Apache на r1 командой: `telnet [адрес] [порт]`. Проверить соединение по TCP для **DNAT**, для этого с r1 подключиться к серверу Apache на ws22 командой `telnet` (обращаться по адресу r2 и порту 8080)

![linux_network](img/part7_telnet.jpg)

## Part 8. Дополнительно. Знакомство с **SSH Tunnels**

##### Запустить веб-сервер **Apache** на ws22 только на localhost (то есть не изменять файл _/etc/apache2/ports.conf_ или, если был изменен ранее, вернуть строку `Listen 80`)

![linux_network](img/part8_localhost.jpg)

##### Воспользоваться _Local TCP forwarding_ с ws21 до ws22, чтобы получить доступ к веб-серверу на ws22 с ws21

![linux_network](img/part8_sshL.jpg)
![linux_network](img/part8_sshLTest.jpg)

##### Воспользоваться _Remote TCP forwarding_ c ws11 до ws22, чтобы получить доступ к веб-серверу на ws22 с ws11

![linux_network](img/part8_sshR.jpg)
![linux_network](img/part8_sshRTest.jpg)

-   В отчёте описать команды, необходимые для выполнения этих четырёх пунктов, а также приложить скриншоты с их вызовом и выводом.
    -   Local TCP forwarding применяется команда ssh -L local_port:destination:destination_port ssh_server_ip
    -   Remote TCP forwarding: команда ssh -R remote_port:destination:destination_port ssh_server_ip
