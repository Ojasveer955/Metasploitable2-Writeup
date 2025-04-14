# Metasploitable 2 - Penetration Testing Lab

## Overview
This document provides a comprehensive walkthrough of exploiting **Metasploitable2**, a vulnerable Linux machine intentionally designed for penetration testing. The goal is to practice exploiting known vulnerabilities to gain unauthorized access and, where necessary, perform privilege escalation to gain higher levels of access, such as root.

Throughout the guide, we will cover the exploitation of various services running on the Metasploitable 2 machine, leveraging tools like *Metasploit Framework*, *Hydra*, *Netcat*, and others. Each section includes the identification of vulnerabilities, exploitation steps, and specific techniques for escalating privileges to root when applicable.



---

### Introduction
Metaspolitable2 is an intentially vulnerable Linux machine. It has been designed as such to be an excellent target for penetration testing, allowing users to practice exploiting real-world vuldnerabilties in a controlled environment. This document details the process of exploiting various vulnerabilities within Metasploitable 2 to demonstrate the security flaws present in the system.

---

# Setup

### VM Configuration

- **Metasploitable 2 VM**
- **Host OS**: Kali Linux
- **VM Software**: VirtualBox


### Tools Used
- Kali Linux
- Metasploit Framework
- Nmap
- Enum4linux
- Netcat
---

### Environment Setup
The Metasploitable 2 VM was deployed and configured with default settings. The network was configured on an isolated subnet(Host-only Adapter Network settings) for testing purposes, with the attacker machine on the same subnet.

---

## Scanning and Enumeration

### 1. Initial Recon with Nmap

A full Nmap scan was performed to identify the open ports and services on the Metasploitable2 machine. 

```bash 
sudo nmap -sV -v -T5 -p- <target-ip>
```

The results showed several open ports with vulnerable services, including:


| Port     | Service        | Version                |
|----------|----------------|------------------------|
| 21/tcp   | FTP            | vsftpd 2.3.4           |
| 22/tcp   | SSH            | OpenSSH 4.7p1 Debian   |
| 23/tcp   | Telnet         | Linux telnetd          |
| 25/tcp   | SMTP           | Postfix smtpd          |
| 80/tcp   | HTTP           | Apache 2.2.8           |
| 139/445  | SMB            | Samba smbd 3.0.20      |
| 3306/tcp | MySQL          | MySQL 5.0.51a-3ubuntu5 |
| 5432/tcp | PostgreSQL     | PostgreSQL             |
| 3632/tcp | distccd        | GNU compiler daemon    |

---

## Exploitation

#### 1. vsftpd 2.3.4 Backdoor  -  Remote Code Execution

- **Port:** 21
- **Vulnerability**: A backdoor can be introduced in vsftpd 2.3.4, which triggers a command shell, on port 6200, when a username ending with `:)` is submitted. This vulnerabity is popularly known as the *Smiley Face Backdoor*.

- **Exploitation** Tool: Metasploit
- **Module Used**: `exploit/unix/ftp/vsftpd_234_backdoor`
- **Result**: Root Shell obtained.

- [Read full report](./FTP/README.md)

---

#### 2. OpenSSH  4.7p1 - Remote Code Execution

- **Port:** 22
- **Vulnerability**: OpenSSH 4.7p1 on Metasploitable 2 contains a vulnerability that allows an attacker to exploit weak authentication mechanisms or brute force SSH credentials.
- **Exploitation Tool**: Hydra
- **Result**: Root Shell obtained.

- [Read full report](./SSH/README.md)

---

#### 3. Telnetd  -  Unauthenticated Access

- **Port:** 23
- **Vulnerability**: The Linux telnetd service on Metasploitable 2 has a vulnerability that allows attackers to connect to the machine without authentication, exploiting weak configurations or misconfigurations in the Telnet service.
- **Exploitation Tool**: Telnet/Netcat
- **Result**: Root Shell obtained though default credentials.

- [Read full report](./Telnet/README.md)

---

#### 4. Postfix SMTPD - Enumeration & Potential Exploitation


- **Port:** 25
- Vulnerability: The SMTP service reveals system usernames via VRFY and EXPN commands, allowing user enumeration that can aid in further attacks like brute force or social engineering.
- Exploitation Tool: Metasploit, Netcat
- Module Used: `auxiliary/scanner/smtp/smtp_enum`
- Result: Enumerated valid system users for targeted attacks.

- [Read full report](./SMTP/README.md)

---

#### 5. Apache 2.2.8 - Web Application Vulnerabilities

- **Port:** 80
- Vulnerability: The Apache web server hosts several vulnerable web applications (e.g., DVWA, Mutillidae, phpMyAdmin) that are prone to multiple web-based attacks like SQL Injection, XSS, and command execution.
- Exploitation Tool: Burp Suite, Nikto
- Result: Gained shell access or sensitive data through web vulnerabilities.

- [Read full report](./HTTP/README.md)

---

#### 6. Samba smbd 3.0.20 - Remote Code Execution via Usermap Script


- **Port:** 139, 445
- Vulnerability: Samba smbd 3.0.20 contains a vulnerability in its username map script feature, which allows unauthenticated remote code execution. This vulnerability is commonly exploited using a crafted username.
- Exploitation Tool: Metasploit Framework
- Module Used:`exploit/multi/samba/usermap_script`
- Result: Root shell obtained on the target machine.
- [Read full exploit](./SMB/README.md)

---

#### 7. MySQL 5.0.51a - Unauthorized Access & Credential Dump
- **Port:** 3306
- Vulnerability: The MySQL service on Metasploitable 2 allows login using weak or default credentials. Once authenticated, attackers can enumerate databases, extract user credentials, or escalate privileges using file write features.
- Exploitation Tool: Metasploit Framework
- Module Used:`auxiliary/scanner/mysql/mysql_login`
- Result: Gained access to database with root privileges, extracted user credentials.
- [Read full exploit](./MySQL/README.md)

---

#### 8. PostgreSQL - Remote Code Execution via Trust Authentication


- **Port:** 5432
- Vulnerability: PostgreSQL on Metasploitable 2 is configured with trust authentication, allowing login without a password. Once logged in, an attacker can leverage PostgreSQL’s capability to execute system-level commands via user-defined functions.
- Exploitation Tool: Metasploit Framework
- Module Used:`exploit/multi/postgres/postgres_payload`
- Result: Remote shell access achieved.
- [Read full exploit](./PostgreSQL/README.md)

---

#### 9. distccd - Remote Command Execution via Daemon Misconfiguration

- **Port:** 3632
- Vulnerability: The distccd service is misconfigured to allow unauthenticated remote command execution. This occurs because the daemon executes arbitrary commands passed to it via job requests without proper validation.


- Exploitation Tool: Metasploit Framework
- Module Used:`unix/misc/distcc_exec`
- Result: Remote shell access achieved.
- [Read full exploit](./distccd/README.md)

---