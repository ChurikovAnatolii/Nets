# Nets

## Теоритическая часть.
- Первое, что нужно сделать это поменять суммарные подсети между office1 и office2.  
Для office1 это будет 192.168.1.0/24 а для office2 192.168.2.0/24. 

- Сеть office 1
- суммарная сеть 192.168.1.0 / 24
- свободных подсетей нет 
- узлов d каждой подсети по 62 
- адрес 1 подсети  192.168.1.1  бродкаст 192.168.1.63
- адрес 2 подсети  192.168.1.64  бродкаст 192.168.1.127
- адрес 3 подсети  192.168.1.128  бродкаст 192.168.1.191
- адрес 4 подсети  192.168.1.192 бродкаст 192.168.1.253
---
- Сеть Office 2
- суммарная сеть 192.168.2.0 / 24
- свобных подсетей нет 
- узлов в первой подсети 126 в остальных по 62
- адрес 1 подсети  192.168.2.0  бродкаст 192.168.2.127
- адрес 2 подсети  192.168.2.128 бродкаст 192.168.2.191
- адрес 3 подсети  192.168.2.192  бродкаст 192.168.2.253
---
- Сеть Central
- Суммарная сеть 192.168.0.0 / 24
- в первой и второй подсети по 14 хостов в третьей 62
- Для сети office hardware изменить подсеть на 192.168.0.16 /28 чтобы не было прмежутка между сетями directors и office hardware и тогда получится следующие подсети: 
- адрес 1 подсети  192.168.0.0  бродкаст 192.168.0.15 
- адрес 2 подсети  192.168.0.16 бродкаст 192.168.0.31 
- адрес 3 подсети  192.168.0.64  бродкаст 192.168.0.127
- Свободные подсети: 
- 192.168.0.32, 192.168.0.48 c 28 маской и 192.168.0.128, 192.168.0.192 с 26 маской.

## Практическая часть.

- В Vagrantfile прописаны маршруты по умолчанию до своих роутеров
- В плейбук nets.yml прописаны статические маршруты для роутеров 
- Для запуска стенда используем команду vagrant up && ansible-playbook nets.yml
- Запустим стенд, зайдем на office1srv и проверим доступность интернета через centralrouter и далее inet router, далее проверим сетевую доступность до всех серверов.

```
[root@office1srv vagrant]# traceroute 192.168.0.2
traceroute to 192.168.0.2 (192.168.0.2), 30 hops max, 60 byte packets
 1  gateway (192.168.1.1)  0.907 ms  0.745 ms  0.878 ms
 2  192.168.255.5 (192.168.255.5)  1.955 ms  1.589 ms  1.453 ms
 3  192.168.0.2 (192.168.0.2)  2.316 ms  2.203 ms  2.017 ms
 
[root@office1srv vagrant]# traceroute 192.168.2.2
traceroute to 192.168.2.2 (192.168.2.2), 30 hops max, 60 byte packets
 1  gateway (192.168.1.1)  0.871 ms  0.729 ms  0.453 ms
 2  192.168.255.5 (192.168.255.5)  1.439 ms  1.061 ms  0.962 ms
 3  192.168.255.10 (192.168.255.10)  1.795 ms  2.441 ms  2.338 ms
 4  192.168.2.2 (192.168.2.2)  2.512 ms  2.194 ms  2.071 ms

 
[root@office1srv vagrant]# traceroute ya.ru
traceroute to ya.ru (87.250.250.242), 30 hops max, 60 byte packets
 1  gateway (192.168.1.1)  0.958 ms  0.596 ms  0.596 ms
 2  192.168.255.5 (192.168.255.5)  1.681 ms  1.618 ms  1.801 ms
 3  192.168.255.1 (192.168.255.1)  2.592 ms  2.484 ms  2.234 ms
 4  * * *
 5  * * *
 6  * * *
 7  * * *
 8  213.79.127.21 (213.79.127.21)  3.961 ms  3.673 ms  3.812 ms
 9  sas-32z5-ae1.yndx.net (87.250.239.201)  16.867 ms sas-32z3-ae1.yndx.net (87.250.239.183)  21.355 ms sas-32z5-ae2.yndx.net (87.250.239.203)  13.118 ms
10  * ya.ru (87.250.250.242)  7.585 ms *
```

- Повторим процедуру для office2srv


```
[root@office2srv vagrant]# tracroute 192.168.0.2
bash: tracroute: command not found
[root@office2srv vagrant]# traceroute 192.168.0.2
traceroute to 192.168.0.2 (192.168.0.2), 30 hops max, 60 byte packets
 1  gateway (192.168.2.1)  0.937 ms  0.793 ms  0.495 ms
 2  192.168.255.9 (192.168.255.9)  1.060 ms  1.211 ms  1.659 ms
 3  192.168.0.2 (192.168.0.2)  2.045 ms  1.694 ms  1.556 ms

[root@office2srv vagrant]# traceroute 192.168.1.2
traceroute to 192.168.1.2 (192.168.1.2), 30 hops max, 60 byte packets
 1  gateway (192.168.2.1)  0.887 ms  0.735 ms  0.557 ms
 2  192.168.255.9 (192.168.255.9)  1.595 ms  1.473 ms  1.357 ms
 3  192.168.255.6 (192.168.255.6)  2.912 ms  2.801 ms  2.546 ms
 4  192.168.1.2 (192.168.1.2)  2.934 ms  2.802 ms  2.662 ms

[root@office2srv vagrant]# traceroute ya.ru
traceroute to ya.ru (87.250.250.242), 30 hops max, 60 byte packets
 1  gateway (192.168.2.1)  0.601 ms  0.693 ms  0.694 ms
 2  192.168.255.9 (192.168.255.9)  1.738 ms  1.504 ms  1.500 ms
 3  192.168.255.1 (192.168.255.1)  2.533 ms  2.729 ms  2.562 ms
 4  * * *
 5  * * *
 6  * * *
 7  * * *
 8  213.79.127.21 (213.79.127.21)  4.110 ms  4.015 ms  3.680 ms
 9  vla-32z1-ae2.yndx.net (93.158.172.19)  17.775 ms * *
10  ya.ru (87.250.250.242)  7.466 ms * *
```

Вывод : маршрутизация работает согдасно схеме.

---

### ДЗ фильтрация трафика


**- 1. Реализовать knocking port**

Для этого на inetrouter устанавливаем knokd-сервис, на centralRouter скрипт, который стучится по следущим tcp-портам: 2222,3333,4444. Проверяем работу. Для этого зайдем на inetRouter и включим DROP политику на INPUT, проверим возможность доступа с centralrouter после запуска скрипта:

```console

[vagrant@inetRouter ~]$ sudo iptables --policy INPUT DROP

[vagrant@centralRouter ~]$ ping 192.168.255.1
PING 192.168.255.1 (192.168.255.1) 56(84) bytes of data.
[vagrant@centralRouter ~]$ ./knock.sh 192.168.255.1 2222 3333 4444
[vagrant@centralRouter ~]$ ssh 192.168.255.1
vagrant@192.168.255.1's password: 
Last login: Sat Dec 10 09:10:14 2022 from 10.0.2.2
[vagrant@inetRouter ~]$ sudo iptables -nvL
Chain INPUT (policy DROP 5 packets, 1884 bytes)
 pkts bytes target     prot opt in     out     source               destination         
    6   360 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            tcp dpts:2222:4444
  161 13345 ACCEPT     tcp  --  *      *       192.168.255.2        0.0.0.0/0            tcp dpt:22

```
---

**- 2. Реализация пунктов 2-5 ДЗ**

В вагрант файл добавлен inetRouter2, на его интерфейсах со стороны хоста настроен DNAT c порта 8080 в сторону centralRouter на порт 80. На centralRouter поднят nginx, настроен обратный статический маршрут с centralRouter в сторону хоста. Проверим работу схемы.

```console
[root@ATVVO Nets]# curl 192.168.56.100:8080
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
<head>
  <title>Welcome to CentOS</title>
  <style rel="stylesheet" type="text/css"> 

        html {
        background-image:url(img/html-background.png);
        background-color: white;
        font-family: "DejaVu Sans", "Liberation Sans", sans-serif;
        font-size: 0.85em;
        line-height: 1.25em;
        margin: 0 4% 0 4%;
        }

        body {
        border: 10px solid #fff;
        margin:0;
        padding:0;
        background: #fff;
        }
```
Реализация через ansible playbook.

