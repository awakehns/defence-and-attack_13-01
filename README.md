# Домашнее задание к занятию "`Уязвимости и атаки на информационные системы`" - `Демин Герман`

### Задание 1

Vbox у меня уже установлен, скачал и поставил образ виртуального диска ubuntu metasploitable, ввел в ту же сеть что и хост:

Metasploitable:
![meta](/img/meta.png)

Установка nmap и сканирование виртуальной машины:

```
sudo dnf install -y nmap

sudo nmap -sS -sV -O 192.168.1.101
```

Результат проверки:

![scan-1](/img/scan-1.png)

Активные службы:

    FTP, SSH, Telnet

    HTTP (Apache), SMTP (Postfix)

    Samba

    MySQL

Найденные уязвимости:

1. vsftpd 2.3.4 — Backdoor Command Execution

    Exploit: https://www.exploit-db.com/exploits/49757

2. Samba smbd 3.X — Remote Code Execution

    Exploit: https://www.exploit-db.com/exploits/16320

    CVE: CVE-2007-2447

3. MySQL 5.0.51a — Privilege Escalation via User-Defined Function

    Exploit: https://www.exploit-db.com/exploits/1518

### Задание 2

Установка wireshark, сканирование в разных режимах и запись пакетов:

```
sudo dnf install wireshark -y

sudo usermod -aG wireshark drvidjet

newgrp wireshark

wireshark

sudo nmap -sS 192.168.1.101       # SYN

sudo nmap -sF 192.168.1.101       # FIN

sudo nmap -sX 192.168.1.101       # Xmas

sudo nmap -sU 192.168.1.101       # UDP
```

Пакеты SYN:
![scan-SYN](/img/scan-SYN.png)

Пакеты FIN:
![scan-FIN](/img/scan-FIN.png)

Пакеты Xmas:
![scan-Xmas](/img/scan-Xmas.png)

Пакеты UDP:
![scan-UDP](/img/scan-UDP.png)

Отличия режимов:

    SYN (-sS) - Полуоткрытое соединение (SYN) - SYN-ACK (открыт) или RST (закрыт)
    FIN (-sF) - FIN-пакеты без установки TCP - RST (закрыт), игнор (открыт)
    Xmas (-sX) - Установлены флаги FIN, URG, PSH - Как FIN: тишина/ RST
    UDP (-sU) - Без установки соединения - ICMP unreachable или игнор

Как отвечает сервер:

SYN Scan (-sS)

    Используется TCP 3-way handshake.

    При открытом порте сервер отвечает SYN-ACK.

    nmap завершает сессию, отправляя RST, чтобы не устанавливать соединение.
    
    В моем случае порт открыт

FIN Scan (-sF)

    TCP-сессия не инициализируется — отправляется FIN без SYN.

    Если порт открыт, сервер по стандарту RFC 793 должен промолчать.

    Если порт закрыт, сервер возвращает RST.
    
    В моем случае порт закрыт

Xmas Scan (-sX)

    Пакет с установленными флагами FIN, URG, PSH — как «рождественская ёлка».

    Реакция та же, что и у FIN: тишина = порт открыт, RST = порт закрыт.
    
    В моем случае также порт закрыт

UDP Scan (-sU)

    Отправляется UDP-пакет (иногда пустой).

    Если порт закрыт — сервер (или ОС) возвращает:

    ICMP Destination Unreachable (Port Unreachable)

    Если порт открыт — сервер либо отвечает приложением, либо молчит.

    В моем случае также порт закрыт
