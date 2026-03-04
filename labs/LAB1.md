# Лабораторна робота №1

## Про програму

Програма реалізує консольну утиліту `ping` мовою програмування Python з використанням бібліотеки [pythonping](https://pypi.org/project/pythonping/).

## Код програми

Також доступний у [цьому репозиторії](https://github.com/nshkkz/python_ping/tree/lab1).

```py
import sys
from pythonping import ping

if __name__ == "__main__":
    # Визначення IP-адреси чи домену для пінгування
    ip = None
    if len(sys.argv) < 2:
        ip = input("Enter IP address (from 0.0.0.0 to 255.255.255.255) or domain (ex. 'google.com'): ")
    else:
        ip = sys.argv[1]
    
    try:
        # Початок пінгування
        stats = ping(ip, verbose=True, count=5)

        # Виведення статистики та результатів
        print(f"RTT (min/avg/max): {stats.rtt_min_ms:.2f}ms/{stats.rtt_avg_ms:.2f}ms/{stats.rtt_max_ms:.2f}ms")
        print(f"Packets sent: {stats.stats_packets_sent}, \
packets returned: {stats.stats_packets_returned} ({(stats.stats_success_ratio * 100):.2f}%), \
packets lost: {stats.packets_lost} ({(stats.packet_loss * 100.0):.2f}%)")
    except Exception as ex:
        print(f"Failure to begin pinging: {ex}")
```

## Приклад виконання програми

> [!NOTE]
> Програмі потрібен підвищений доступ, оскільки він використовує сирі сокети для
> надсилання ICMP-пакетів. Команда `ping` у GNU/Linux цього не потребує, бо або вона
> містить SUID флаг, або є винятком у ядрі (залежить від ОС).

Без адреси в аргументі:

```sh
$ sudo python3 python_ping.py
Password:
Enter IP address (from 0.0.0.0 to 255.255.255.255) or domain (ex. 'google.com'): 192.168.1.1
Reply from 192.168.1.1, 29 bytes in 5.88ms
Reply from 192.168.1.1, 29 bytes in 2.63ms
Reply from 192.168.1.1, 29 bytes in 2.65ms
Reply from 192.168.1.1, 29 bytes in 2.38ms
Reply from 192.168.1.1, 29 bytes in 2.49ms
RTT (min/avg/max): 2.38ms/3.21ms/5.88ms
Packets sent: 5, packets returned: 5 (100.00%), packets lost: 0.0 (0.00%)
```

З адресою як аргумент:

```sh
$ sudo python3 python_ping.py 1.1.1.1
Reply from 1.1.1.1, 29 bytes in 4.52ms
Reply from 1.1.1.1, 29 bytes in 3.95ms
Reply from 1.1.1.1, 29 bytes in 3.55ms
Reply from 1.1.1.1, 29 bytes in 3.75ms
Reply from 1.1.1.1, 29 bytes in 4.06ms
RTT (min/avg/max): 3.55ms/3.97ms/4.52ms
Packets sent: 5, packets returned: 5 (100.00%), packets lost: 0.0 (0.00%)
```

За доменним імʼям:

```sh
$ sudo python3 python_ping.py google.com
Reply from 142.251.98.113, 29 bytes in 17.36ms
Reply from 142.251.98.113, 29 bytes in 17.87ms
Reply from 142.251.98.113, 29 bytes in 17.71ms
Reply from 142.251.98.113, 29 bytes in 17.88ms
Reply from 142.251.98.113, 29 bytes in 18.02ms
RTT (min/avg/max): 17.36ms/17.77ms/18.02ms
Packets sent: 5, packets returned: 5 (100.00%), packets lost: 0.0 (0.00%)
```
