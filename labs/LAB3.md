# Лабораторна робота №2

## Про програму

Програма реалізує консольну утиліту `ping` мовою програмування Python з використанням бібліотеки [pythonping](https://pypi.org/project/pythonping/) з доданим функціоналом для пінгування переліку IP-адрес та range-у IP-адрес.

## Код програми

Також доступний у [цьому репозиторії](https://github.com/nshkkz/python_ping/tree/lab3).

```py

```

## Приклад виконання програми

> [!NOTE]
> Програмі потрібен підвищений доступ, оскільки він використовує сирі сокети для
> надсилання ICMP-пакетів. Команда `ping` у GNU/Linux цього не потребує, бо або вона
> містить SUID флаг, або є винятком у ядрі (залежить від ОС).

```sh
$ python3 python_ping.py --help
usage: python_ping.py [-h] [--responded-file RESPONDED_FILE] [--no-response-file NO_RESPONSE_FILE] [target]

Ping one or more targets. Targets can be a single host/IP, comma-separated targets, and/or IPv4 ranges such as
192.168.1.1-192.168.1.255.

positional arguments:
  target                Target expression to ping. If omitted, an interactive prompt is shown.

options:
  -h, --help            show this help message and exit
  --responded-file RESPONDED_FILE
                        Output file path for targets that responded.
  --no-response-file NO_RESPONSE_FILE
                        Output file path for targets that did not respond.
$ sudo python3 python_ping.py "192.168.1.1-192.168.1.10,1.1.1.1,google.com" --responded-file responded.txt --no-response-file no_response.txt
Password:
Pinging 12 targets (count=1 each)...
Reply from 192.168.1.1, 29 bytes in 5.37ms
Reply from 192.168.1.2, 29 bytes in 4.94ms
Reply from 192.168.1.3, 29 bytes in 4.33ms
Request timed out
Request timed out
Request timed out
Request timed out
Request timed out
Request timed out
Request timed out
Reply from 1.1.1.1, 29 bytes in 7.3ms
Reply from 142.250.109.100, 29 bytes in 18.26ms

Response summary:
Responded (5): 192.168.1.1, 192.168.1.2, 192.168.1.3, 1.1.1.1, google.com
No response (7): 192.168.1.4, 192.168.1.5, 192.168.1.6, 192.168.1.7, 192.168.1.8, 192.168.1.9, 192.168.1.10
```

**responded.txt:**

```txt
192.168.1.1
192.168.1.2
192.168.1.3
1.1.1.1
google.com
```

**no_response.txt:**

```txt
192.168.1.4
192.168.1.5
192.168.1.6
192.168.1.7
192.168.1.8
192.168.1.9
192.168.1.10
```
