---
title: "Zabbix Monitoring Tool"
datePublished: Wed Aug 02 2023 06:19:53 GMT+0000 (Coordinated Universal Time)
cuid: clktc9552000209mn9kdl8qby
slug: zabbix-monitoring-tool
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/qwtCeJ5cLYs/upload/38b9ee04b1e2d0d388f1bed28f096a64.jpeg
tags: automation, networking

---

# **What is Zabbix?**

Zabbix is an open-source monitoring software tool that helps organizations track and monitor their IT infrastructure and networks in real-time. It is designed to provide comprehensive monitoring of networks, servers, applications, and services, and to alert administrators when problems occur.

Zabbix can monitor various aspects of IT infrastructure such as CPU, memory, disk usage, network bandwidth, and more. It provides a centralized platform for monitoring and reporting, allowing administrators to view the status of all devices and services on their network from a single dashboard.

Zabbix offers a wide range of features, including data collection via SNMP, JMX, IPMI, and other protocols, real-time alerting, flexible reporting and analysis, and the ability to automate remediation actions. It also supports the visualization of data through charts, graphs, and maps.

# **Zabbix Operation**

Zabbix collects information from Network Devices and Servers using two methods, agentless and agent installed.

## **Agentless**

It allows administrators to monitor devices without the need to install an agent on each device.

Agentless monitoring in Zabbix works by using protocols such as ICMP, SNMP, and SSH to gather data from network devices and servers.

For example, ICMP can be used to check the availability of network devices, SSH can be used to execute commands on remote servers and retrieve data.

However, agentless monitoring may not be as comprehensive as agent-based monitoring, as it relies on standard protocols that may not provide access to all of the performance data available on a device.

Additionally, agentless monitoring may be less secure than agent-based monitoring, as it requires the use of credentials to access devices over the network.

## **Zabbix Agent**

Agent installation: Zabbix requires an agent to be installed on each monitored device. This agent collects data on the device's performance and sends it to the Zabbix server for processing.

* Data collection: Zabbix can collect data from a variety of sources, including SNMP, JMX, IPMI, and other protocols. The collected data is stored in a database for analysis and reporting.
    
* Threshold monitoring: Zabbix allows administrators to set thresholds for the collected data. When a value exceeds a threshold, Zabbix generates an alert, which can be sent to one or more designated recipients via email, SMS, or other channels.
    
* Alerting: Zabbix can send alerts to multiple recipients based on user-defined rules. Administrators can define different levels of severity for different types of alerts and set up escalation policies to ensure timely responses to critical issues.
    
* Reporting: Zabbix provides a range of reporting options, including real-time dashboards, scheduled reports, and ad-hoc queries. Reports can be customized to provide the specific information required by administrators.
    
* Remediation: Zabbix also supports the ability to automate remediation actions, such as restarting a service or executing a script, based on user-defined rules.
    

## **Zabbix Structure**

The web front-end is what we normally open in our web browser and is used as a centralized visualization and configuration platform.

The Zabbix server is the brain of the entire system, which is responsible for gathering data from the hosts and proxies, analyzing it, and acting based on the triggers that we have created.

The database is where everything is stored, what we configure in our front end: items, hosts, proxies, triggers, actions, or whatever else. We collect those data from our hosts and store them in the database for the period we need.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1691978701035/79771512-f5d7-4e56-b63f-cbe21013b8bf.png align="center")

# **Zabbix Installation**

## **System Requirement**

OS: Ubuntu 22.04 LTS (can be changed to lower or other distros)

Zabbix version: 6.0 LTS

Database version: MariaDB 10.6 (you can use PostgreSQL or MySQL)

Firewall setting: TCP 80 & 443

## **Zabbix Server Installation**

Download and install the repo for Zabbix's latest stable version 6.0 LTS.

```bash
wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.0-4+ubuntu22.04_all.deb
sudo dpkg -i zabbix-release_6.0-4+ubuntu22.04_all.deb
sudo apt update
```

Install the necessary packages for the Zabbix server.

```bash
sudo apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent -y
```

## **Database Installation**

Download and install the repo for MariaDB.

```bash
curl -LsS -O https://downloads.mariadb.com/MariaDB/mariadb_repo_setup
sudo bash mariadb_repo_setup --mariadb-server-version=10.6
sudo apt update
```

Install the necessary packages and enable MariaDB.

```bash
sudo apt -y install mariadb-common mariadb-server-10.6 mariadb-client-10.6 -y
sudo systemctl start mariadb
sudo systemctl enable mariadb
sudo mysql_secure_installation
```

Create an initial user for the database.

```bash
sudo mysql_secure_installation

Remove anonymous users? Yes
Disallow root login remotely? Yes
Remove test database and access to it? Yes
```

Create a user and data table for Zabbix

```bash
#Access SQL with user root
sudo mysql -u root
mysql > create database zabbix character set utf8mb4 collate utf8mb4_bin;
mysql > grant all privileges on zabbix.* to zabbix@localhost identified by 'password';
mysql > set global log_bin_trust_function_creators = 1;
mysql > flush privileges;
mysql > quit

# Set default SQL user
sudo zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p'password' zabbix
```

## **Set Up Zabbix**

Change the Zabbix setting file.

```bash
sudo nano /etc/zabbix/zabbix_server.conf
#edit below part which matches with your info.
DBName=zabbix
DBUser=zabbix
DBPassword=password
```

Restart Zabbix services.

```bash
sudo systemctl restart zabbix-server zabbix-agent
sudo systemctl enable zabbix-server zabbix-agent
```

You can now access the Zabbix web interface and complete the setup

[your-ip-add/zabbix](http://your-ip-add/zabbix)

Input the database information that you create above, and complete the setup.