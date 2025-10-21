# Домашнее задание к занятию 2 «Кластеризация и балансировка нагрузки»
**Студент:** Лариса Романова

## Задание 1

### Запуск серверов и настройка HAProxy

#### 1. Запуск двух Python серверов
```bash
# Сервер 1 на порту 8888
cd ~/http1
python3 -m http.server 8888

# Сервер 2 на порту 9999  
cd ~/http2
python3 -m http.server 9999
```bash

global
    log /dev/log    local0
    log /dev/log    local1 notice
    chroot /var/lib/haproxy
    stats socket /run/haproxy/admin.sock mode 660 level admin expose-fd listeners
    stats timeout 30s
    user haproxy
    group haproxy
    daemon

defaults
    log     global
    mode    tcp
    option  tcplog
    option  dontlognull
    timeout connect 5000
    timeout client  50000
    timeout server  50000

# Статистика
listen stats
    bind    :888
    mode    http
    stats   enable
    stats   uri /stats
    stats   refresh 5s
    stats   realm Haproxy\ Statistics

# Балансировка на 4 уровне (TCP)
listen web_tcp
    mode    tcp
    bind    :1325
    balance roundrobin
    server  s1 127.0.0.1:8888 check
    server  s2 127.0.0.1:9999 check
```
