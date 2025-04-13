# Metasploitable 2 - Penetration Testing Lab

## Overview
This is a complete walkthrough of exploiting **Metasploitable 2**, a vulnerable Linux machine, to practice penetration testing and privelige escalation techniques.

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

### 2. Exploitation

#### 2.1 vsftpd 2.3.4 Backdoor  -  Remote Code Execution

- **Port:** 21
- Vulnerability: A backdoor can be introduced in vsftpd 2.3.4, which triggers a command shell, on port 6200, when a username ending with `:)` is submitted. This vulnerabity is popularly known as the *Smiley Face Backdoor*.

- Exploitation Tool: Metasploit
- Module Used: `exploit/unix/ftp/vsftpd_234_backdoor`
- Result: Root Shell obtained.

- [Read full exploit] (./FTP/README.md)
