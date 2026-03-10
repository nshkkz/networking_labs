# Лабораторна робота №2

## Про програму

Програма реалізує консольну утиліту `ping` мовою програмування Python з використанням бібліотеки [pythonping](https://pypi.org/project/pythonping/) з доданим функціоналом для пінгування переліку IP-адрес та range-у IP-адрес.

## Код програми

Також доступний у [цьому репозиторії](https://github.com/nshkkz/python_ping/tree/lab2).

```py
import sys
from ipaddress import IPv4Address
from pythonping import ping


def expand_targets(raw_input):
    targets = []
    seen = set()

    for part in raw_input.split(","):
        token = part.strip()
        if not token:
            continue

        if "-" in token:
            start_text, end_text = [p.strip() for p in token.split("-", 1)]
            try:
                start_ip = IPv4Address(start_text)
                end_ip = IPv4Address(end_text)
            except ValueError:
                # Not an IPv4 range, treat as a normal hostname/target.
                if token not in seen:
                    seen.add(token)
                    targets.append(token)
                continue

            if int(start_ip) > int(end_ip):
                raise ValueError(f"Invalid range '{token}': start IP is greater than end IP.")

            for ip_int in range(int(start_ip), int(end_ip) + 1):
                ip_text = str(IPv4Address(ip_int))
                if ip_text not in seen:
                    seen.add(ip_text)
                    targets.append(ip_text)
            continue

        if token not in seen:
            seen.add(token)
            targets.append(token)

    if not targets:
        raise ValueError("No valid targets were provided.")

    return targets


def ping_single_target(target):
    stats = ping(target, verbose=True, count=5)

    print(
        f"RTT (min/avg/max): "
        f"{stats.rtt_min_ms:.2f}ms/{stats.rtt_avg_ms:.2f}ms/{stats.rtt_max_ms:.2f}ms"
    )
    print(
        f"Packets sent: {stats.stats_packets_sent}, "
        f"packets returned: {stats.stats_packets_returned} ({(stats.stats_success_ratio * 100):.2f}%), "
        f"packets lost: {stats.packets_lost} ({(stats.packet_loss * 100.0):.2f}%)"
    )


def ping_multiple_targets(targets):
    single_probe_mode = len(targets) > 5
    ping_count = 1 if single_probe_mode else 5

    print(f"Pinging {len(targets)} targets (count={ping_count} each)...")

    responded = []
    no_response = []
    failures = []

    for target in targets:
        try:
            stats = ping(target, verbose=True, count=ping_count, timeout=1)
            if stats.stats_packets_returned > 0:
                responded.append(target)
            else:
                no_response.append(target)

            if not single_probe_mode:
                if stats.stats_packets_returned > 0:
                    print(
                        f"{target}: RTT(min/avg/max) "
                        f"{stats.rtt_min_ms:.2f}ms/{stats.rtt_avg_ms:.2f}ms/{stats.rtt_max_ms:.2f}ms "
                        f"| success {(stats.stats_success_ratio * 100):.2f}%"
                    )
                else:
                    print(f"{target}: no response")
        except Exception as ex:
            failures.append((target, str(ex)))

    if single_probe_mode:
        print("\nResponse summary:")
        print(f"Responded ({len(responded)}): {', '.join(responded) if responded else 'None'}")
        print(f"No response ({len(no_response)}): {', '.join(no_response) if no_response else 'None'}")

    if failures:
        print("\nErrors:")
        for target, error_msg in failures:
            print(f"{target}: {error_msg}")

if __name__ == "__main__":
    raw_target = None
    if len(sys.argv) < 2:
        raw_target = input(
            "Enter IP address/domain, comma-separated targets, or IPv4 ranges (ex. '192.168.1.1-192.168.1.255,1.1.1.1,google.com'): "
        )
    else:
        raw_target = " ".join(sys.argv[1:])

    try:
        targets = expand_targets(raw_target)

        if len(targets) == 1:
            ping_single_target(targets[0])
        else:
            ping_multiple_targets(targets)
    except Exception as ex:
        print(f"Failure to begin pinging: {ex}")
import sys
from ipaddress import IPv4Address
from pythonping import ping


def expand_targets(raw_input):
    targets = []
    seen = set()

    for part in raw_input.split(","):
        token = part.strip()
        if not token:
            continue

        if "-" in token:
            start_text, end_text = [p.strip() for p in token.split("-", 1)]
            try:
                start_ip = IPv4Address(start_text)
                end_ip = IPv4Address(end_text)
            except ValueError:
                # Not an IPv4 range, treat as a normal hostname/target.
                if token not in seen:
                    seen.add(token)
                    targets.append(token)
                continue

            if int(start_ip) > int(end_ip):
                raise ValueError(f"Invalid range '{token}': start IP is greater than end IP.")

            for ip_int in range(int(start_ip), int(end_ip) + 1):
                ip_text = str(IPv4Address(ip_int))
                if ip_text not in seen:
                    seen.add(ip_text)
                    targets.append(ip_text)
            continue

        if token not in seen:
            seen.add(token)
            targets.append(token)

    if not targets:
        raise ValueError("No valid targets were provided.")

    return targets


def ping_single_target(target):
    stats = ping(target, verbose=True, count=5)

    print(
        f"RTT (min/avg/max): "
        f"{stats.rtt_min_ms:.2f}ms/{stats.rtt_avg_ms:.2f}ms/{stats.rtt_max_ms:.2f}ms"
    )
    print(
        f"Packets sent: {stats.stats_packets_sent}, "
        f"packets returned: {stats.stats_packets_returned} ({(stats.stats_success_ratio * 100):.2f}%), "
        f"packets lost: {stats.packets_lost} ({(stats.packet_loss * 100.0):.2f}%)"
    )


def ping_multiple_targets(targets):
    single_probe_mode = len(targets) > 5
    ping_count = 1 if single_probe_mode else 5

    print(f"Pinging {len(targets)} targets (count={ping_count} each)...")

    responded = []
    no_response = []
    failures = []

    for target in targets:
        try:
            stats = ping(target, verbose=True, count=ping_count, timeout=1)
            if stats.stats_packets_returned > 0:
                responded.append(target)
            else:
                no_response.append(target)

            if not single_probe_mode:
                if stats.stats_packets_returned > 0:
                    print(
                        f"{target}: RTT(min/avg/max) "
                        f"{stats.rtt_min_ms:.2f}ms/{stats.rtt_avg_ms:.2f}ms/{stats.rtt_max_ms:.2f}ms "
                        f"| success {(stats.stats_success_ratio * 100):.2f}%"
                    )
                else:
                    print(f"{target}: no response")
        except Exception as ex:
            failures.append((target, str(ex)))

    if single_probe_mode:
        print("\nResponse summary:")
        print(f"Responded ({len(responded)}): {', '.join(responded) if responded else 'None'}")
        print(f"No response ({len(no_response)}): {', '.join(no_response) if no_response else 'None'}")

    if failures:
        print("\nErrors:")
        for target, error_msg in failures:
            print(f"{target}: {error_msg}")

if __name__ == "__main__":
    raw_target = None
    if len(sys.argv) < 2:
        raw_target = input(
            "Enter IP address/domain, comma-separated targets, or IPv4 ranges (ex. '192.168.1.1-192.168.1.255,1.1.1.1,google.com'): "
        )
    else:
        raw_target = " ".join(sys.argv[1:])

    try:
        targets = expand_targets(raw_target)

        if len(targets) == 1:
            ping_single_target(targets[0])
        else:
            ping_multiple_targets(targets)
    except Exception as ex:
        print(f"Failure to begin pinging: {ex}")
```

## Приклад виконання програми

> [!NOTE]
> Програмі потрібен підвищений доступ, оскільки він використовує сирі сокети для
> надсилання ICMP-пакетів. Команда `ping` у GNU/Linux цього не потребує, бо або вона
> містить SUID флаг, або є винятком у ядрі (залежить від ОС).

```sh
$ sudo python3 python_ping.py 192.168.1.1-192.168.1.255,1.1.1.1,google.com
Pinging 257 targets (count=1 each)...
Reply from 192.168.1.1, 29 bytes in 3.61ms
Reply from 192.168.1.2, 29 bytes in 2.77ms
Reply from 192.168.1.3, 29 bytes in 2.57ms
Request timed out
[...]
Request timed out
Reply from 192.168.1.26, 29 bytes in 9.91ms
Request timed out
[...]
Request timed out
Reply from 192.168.1.42, 29 bytes in 46.16ms
Request timed out
[...]
Request timed out
Reply from 192.168.1.116, 29 bytes in 0.15ms
Request timed out
[...]
Request timed out
Reply from 192.168.1.166, 29 bytes in 41.67ms
Request timed out
[...]
Request timed out
Reply from 192.168.1.205, 29 bytes in 824.28ms
Request timed out
[...]
Request timed out
Reply from 192.168.1.220, 29 bytes in 57.62ms
Request timed out
[...]
Request timed out
Reply from 192.168.1.116, 29 bytes in 0.01ms
Reply from 1.1.1.1, 29 bytes in 5.91ms
Reply from 142.250.120.101, 29 bytes in 19.72ms

Response summary:
Responded (12): 192.168.1.1, 192.168.1.2, 192.168.1.3, 192.168.1.26, 192.168.1.42, 192.168.1.116, 192.168.1.166, 192.168.1.205, 192.168.1.220, 192.168.1.255, 1.1.1.1, google.com
No response (245): 192.168.1.4, 192.168.1.5, 192.168.1.6, 192.168.1.7, 192.168.1.8, 192.168.1.9, 192.168.1.10, 192.168.1.11, 192.168.1.12, 192.168.1.13, 192.168.1.14, 192.168.1.15, 192.168.1.16, 192.168.1.17, 192.168.1.18, 192.168.1.19, 192.168.1.20, 192.168.1.21, 192.168.1.22, 192.168.1.23, 192.168.1.24, 192.168.1.25, 192.168.1.27, 192.168.1.28, 192.168.1.29, 192.168.1.30, 192.168.1.31, 192.168.1.32, 192.168.1.33, 192.168.1.34, 192.168.1.35, 192.168.1.36, 192.168.1.37, 192.168.1.38, 192.168.1.39, 192.168.1.40, 192.168.1.41, 192.168.1.43, 192.168.1.44, 192.168.1.45, 192.168.1.46, 192.168.1.47, 192.168.1.48, 192.168.1.49, 192.168.1.50, 192.168.1.51, 192.168.1.52, 192.168.1.53, 192.168.1.54, 192.168.1.55, 192.168.1.56, 192.168.1.57, 192.168.1.58, 192.168.1.59, 192.168.1.60, 192.168.1.61, 192.168.1.62, 192.168.1.63, 192.168.1.64, 192.168.1.65, 192.168.1.66, 192.168.1.67, 192.168.1.68, 192.168.1.69, 192.168.1.70, 192.168.1.71, 192.168.1.72, 192.168.1.73, 192.168.1.74, 192.168.1.75, 192.168.1.76, 192.168.1.77, 192.168.1.78, 192.168.1.79, 192.168.1.80, 192.168.1.81, 192.168.1.82, 192.168.1.83, 192.168.1.84, 192.168.1.85, 192.168.1.86, 192.168.1.87, 192.168.1.88, 192.168.1.89, 192.168.1.90, 192.168.1.91, 192.168.1.92, 192.168.1.93, 192.168.1.94, 192.168.1.95, 192.168.1.96, 192.168.1.97, 192.168.1.98, 192.168.1.99, 192.168.1.100, 192.168.1.101, 192.168.1.102, 192.168.1.103, 192.168.1.104, 192.168.1.105, 192.168.1.106, 192.168.1.107, 192.168.1.108, 192.168.1.109, 192.168.1.110, 192.168.1.111, 192.168.1.112, 192.168.1.113, 192.168.1.114, 192.168.1.115, 192.168.1.117, 192.168.1.118, 192.168.1.119, 192.168.1.120, 192.168.1.121, 192.168.1.122, 192.168.1.123, 192.168.1.124, 192.168.1.125, 192.168.1.126, 192.168.1.127, 192.168.1.128, 192.168.1.129, 192.168.1.130, 192.168.1.131, 192.168.1.132, 192.168.1.133, 192.168.1.134, 192.168.1.135, 192.168.1.136, 192.168.1.137, 192.168.1.138, 192.168.1.139, 192.168.1.140, 192.168.1.141, 192.168.1.142, 192.168.1.143, 192.168.1.144, 192.168.1.145, 192.168.1.146, 192.168.1.147, 192.168.1.148, 192.168.1.149, 192.168.1.150, 192.168.1.151, 192.168.1.152, 192.168.1.153, 192.168.1.154, 192.168.1.155, 192.168.1.156, 192.168.1.157, 192.168.1.158, 192.168.1.159, 192.168.1.160, 192.168.1.161, 192.168.1.162, 192.168.1.163, 192.168.1.164, 192.168.1.165, 192.168.1.167, 192.168.1.168, 192.168.1.169, 192.168.1.170, 192.168.1.171, 192.168.1.172, 192.168.1.173, 192.168.1.174, 192.168.1.175, 192.168.1.176, 192.168.1.177, 192.168.1.178, 192.168.1.179, 192.168.1.180, 192.168.1.181, 192.168.1.182, 192.168.1.183, 192.168.1.184, 192.168.1.185, 192.168.1.186, 192.168.1.187, 192.168.1.188, 192.168.1.189, 192.168.1.190, 192.168.1.191, 192.168.1.192, 192.168.1.193, 192.168.1.194, 192.168.1.195, 192.168.1.196, 192.168.1.197, 192.168.1.198, 192.168.1.199, 192.168.1.200, 192.168.1.201, 192.168.1.202, 192.168.1.203, 192.168.1.204, 192.168.1.206, 192.168.1.207, 192.168.1.208, 192.168.1.209, 192.168.1.210, 192.168.1.211, 192.168.1.212, 192.168.1.213, 192.168.1.214, 192.168.1.215, 192.168.1.216, 192.168.1.217, 192.168.1.218, 192.168.1.219, 192.168.1.221, 192.168.1.222, 192.168.1.223, 192.168.1.224, 192.168.1.225, 192.168.1.226, 192.168.1.227, 192.168.1.228, 192.168.1.229, 192.168.1.230, 192.168.1.231, 192.168.1.232, 192.168.1.233, 192.168.1.234, 192.168.1.235, 192.168.1.236, 192.168.1.237, 192.168.1.238, 192.168.1.239, 192.168.1.240, 192.168.1.241, 192.168.1.242, 192.168.1.243, 192.168.1.244, 192.168.1.245, 192.168.1.246, 192.168.1.247, 192.168.1.248, 192.168.1.249, 192.168.1.250, 192.168.1.251, 192.168.1.252, 192.168.1.253, 192.168.1.254
```
