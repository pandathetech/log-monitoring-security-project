# Log Monitoring Security Project
This project is inspired by my project in my CR350E course at Polytechnique Montréal, where I used network logging tools, such as Syslog, Wireshark and Fail2Ban, to develop my understanding in network logging.

---

## Table of Contents
- [1. My Virtual Infrastructure](#1-my-virtual-infrastructure)
- [2. Network Logging Setup](#2-network-logging-setup)
  - [2.1. Syslog](#21-syslog)
  - [2.2. Ubuntu01](#22-ubuntu01)
  - [2.3. Ubuntu02](#23-ubuntu02)
- [3. Logging Tests](#3-logging-tests)
  - [3.1. Monitoring SSH Connections with Wireshark](#31-monitoring-ssh-connections-with-wireshark)
  - [3.2. Apache2 Logging](#32-apache2-logging)
- [4. Fail2Ban](#4-fail2ban)
  - [4.1. Installation](#41-installation)
  - [4.2. Configuration](#42-configuration)
  - [4.3. Failed Connection Attempts](#43-failed-connection-attempts)
  - [4.4. Display Active Firewall Rules](#44-display-active-firewall-rules)
  - [4.5. Disable Active Firewall Rules and Reconnection](#45-disable-active-firewall-rules-and-reconnection)
- [5. References](#5-references)

---

## 1. My Virtual Infrastructure
In VirtualBox, I created and configured three virtual machines with the following names:

- Syslog
- Ubuntu01
- Ubuntu02

Each VM is configured with two network adapters in bridged mode. The first network adapter is manually configured to be in the same network (192.168.10.0/24) for my three VMs, while the second one is configured to use DHCP and provides internet access and downloading software via the command line).

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-30%20212405.png)

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-30%20212742.png)

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-30%20213135.png)

---

## 2. Network Logging Setup
I installed, enabled and started the rsyslog logging tool on all three of my VMs. After configuring rsyslog on each host, I restarted the service to apply the changes.

### 2.1. Syslog

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-30%20213653.png)

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-30%20213750.png)

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-30%20214146.png)

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-30%20214344.png)

To only authorize the syslog traffic from my **Ubuntu01** VM to my **Syslog** VM, I added the **$AllowedSender UDP** and **$AllowedSender TCP** lines in my Syslog VM’s Rsyslog configuration file. This ensures that other machines, such as my Ubuntu02 VM, cannot send logs to Syslog.

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20135534.png)

### 2.2. Ubuntu01

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-30%20214950.png)

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-30%20215000.png)

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-30%20215731.png)

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-30%20215945.png)

## 2.3. Ubuntu02

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-30%20220451.png)

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-30%20220652.png)

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-30%20221459.png)

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-30%20221551.png)

---

## 3. Logging Tests
After checking the active firewalls for iptables on **Syslog** and **Ubuntu01**, I confirmed that there were no active firewalls on either system.

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20135322.png)

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-30%20222026.png)

On Ubuntu01, I installed the OpenSSH server.

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-30%20222220.png)

I enabled and started the SSH service at system startup, then I checked its status to make sure it was actively running.

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-30%20222327.png)

To prove that my SSH server was working correctly, I attempted to connect to Ubuntu01 from Ubuntu02 using the SSH command.

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20150306.png)

### 3.1. Monitoring SSH Connections with Wireshark
On my Ubuntu01 VM, I installed Wireshark. This software is used to monitor network traffic, such as SSH packets.

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20141346.png)

During the Wireshark installation, I was asked if I should allow non-superusers to capture network packets. It is recommended to decline by entering `No`.

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20141947.png)

After launching Wireshark, I captured SSH packets on the `enpos3` network interface, which is the network interface I used to assign my static IP addresses to my VMs.

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20142121.png)

During the execution of a network traffic capture, I ran some connection attempts (successful and unsuccessful ones).

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20150723.png)

After completing my SSH connection attempts, I stopped the Wireshark capture and analyzed the SSH packets.

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20150820.png)

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20151142.png)

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20151304.png)

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20151443.png)

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20151548.png)

> The syslog service uses the 514 port and the UDP protocol.

### 3.2. Apache2 Logging
On my Ubuntu01 VM, I installed Apache2.

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20142352.png)

After its installation, I enabled and started Apache2 at system startup. I also checked its status to make sure it was actively running.

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20142745.png)

To make sure it’s working properly, I accessed its default page locally on Ubuntu01 by opening a web browser and entering `http://192.168.10.11/` in the search bar.

> 192.168.10.11 is my Ubuntu01 IP address.

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20142908.png)

From my Ubuntu02 VM, I accessed the default page of my Apache web server hosted on Ubuntu01 on the Mozilla Firefox web browser.

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20151822.png)

I also accessed the URL of my Apache web page using `curl` after installing it on Ubuntu02.

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20151850.png)

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20151928.png)

As root on Ubuntu01, I checked what the Apache web server logs are in the `/var/log/apache2/` directory. The most important ones to redirect are `access.log` and `error.log`.

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20143051.png)

To redirect my Apache web server logs to my Rsyslog server, I loaded and configured the `imfile` module, which allows me to read and monitor log files, by modifying the `/etc/rsyslog.d/50-default.conf` file of my Ubuntu01 host.

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20143149.png)

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20143751.png)

After restarting the Rsyslog service on my Ubuntu01, I accessed my Apache web page several times using the web browser and the curl methods while running the `sudo tail -f /var/log/syslog` command in the background to monitor the logs being sent from Ubuntu01 to Syslog.

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20152117.png)

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20152353.png)

To find my Ubuntu02 IP address from my Syslog logs (found in /var/log/syslog), I used the `grep "192.168.10.12" /var/log/syslog` command.

> 192.168.10.12 is my Ubuntu02 IP address.

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20152452.png)

---

## 4. Fail2Ban
Fail2Ban works by checking log files (for example, `/var/log/auth.log`) for suspicious user activity, such as repeated failed login attempts due to incorrect passwords. It also bans the IP addresses of the hosts that try to connect to the host that has Fail2Ban installed by adding firewall rules to block new connection attempts from those hosts for a specified amount of time.

### 4.1. Installation
I installed Fail2Ban on my Ubuntu01 virtual machine.

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20144119.png)

I enabled and started the Fail2Ban service at system startup. I also checked its service status.

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20144236.png)

### 4.2. Configuration
After navigating to the `/etc/fail2ban/` directory, I made a copy of the `jail.conf` file named `jail.local`, then I added the required configurations in the sshd section of the jail.local file.

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20144340.png)

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20152907.png)

After restarting the Fail2Ban service, I checked its status to make sure it was still running.

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20153007.png)

### 4.3. Failed Connection Attempts
Using the `sudo fail2ban-client status sshd` command, I can check the number of failed SSH connection attempts and the list of banned hosts. There are currently none.

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20153047.png)

After two failed SSH connection attempts to Ubuntu01 from Ubuntu02, my Ubuntu02 IP address got banned on the third attempt.

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20153240.png)

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20153309.png)

### 4.4. Display Active Firewall Rules
I displayed the active firewall rules on Ubuntu01 using the `sudo iptables -L` command.

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20153424.png)

### 4.5. Disable Active Firewall Rules and Reconnection
I disabled all firewall rules using the `sudo iptables -F` command, then I redisplayed the output of iptables with the `sudo iptables -L` command to verify the changes.

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20153510.png)

I attempted to connect to Ubuntu01 from Ubuntu02 again, and this time, the connection refusal error message did not appear anymore when I attempted an unsuccessful SSH connection and a successful SSH connection.

![](https://github.com/pandathetech/log-monitoring-security-project/blob/main/Assets/Screenshot%202025-12-31%20153646.png)

---

## 5. References
- [Apache2](https://www.digitalocean.com/community/tutorials/how-to-install-the-apache-web-server-on-ubuntu-22-04)
- [curl](https://curl.se/docs/tutorial.html)
- [Fail2Ban](https://www.howtoforge.com/how-to-install-fail2ban-on-ubuntu-22-04/)
- [grep](https://www.gnu.org/software/grep/manual/grep.html)
- [iptables](https://www.howtogeek.com/177621/the-beginners-guide-to-iptables-the-linux-firewall/)
- [Rsyslog](https://computingpost.medium.com/configure-rsyslog-centralized-log-server-on-ubuntu-22-04-20-04-18-04-b5222129b3f3)
- [Rsyslog - imfile module](https://www.rsyslog.com/doc/configuration/modules/imfile.html)
- [SSH](https://hostman.com/tutorials/how-to-install-and-configure-ssh-on-ubuntu-22-04/)
- [Tail](https://www.geeksforgeeks.org/linux-unix/tail-command-linux-examples/)
- [Wireshark](https://www.cherryservers.com/blog/install-wireshark-ubuntu)
