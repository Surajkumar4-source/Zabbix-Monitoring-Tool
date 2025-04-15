
# **Zabbix Server & Client Configuration on Single VM (Ubuntu 22.04)**


<br>

## **Introduction to Zabbix**  
**Zabbix** is an open-source enterprise-level monitoring solution designed for real-time monitoring of networks, servers, applications, cloud environments, and services. It provides a centralized platform to collect, analyze, and visualize system performance and health data.  

### **Key Features of Zabbix**  
1. **Agent-Based & Agentless Monitoring** – Supports monitoring through installed agents or via protocols like SNMP, ICMP, and JMX.  
2. **Data Collection & Visualization** – Collects system metrics and presents them through customizable dashboards and reports.  
3. **Alerts & Notifications** – Sends alerts via email, SMS, or other integrations when issues arise.  
4. **Scalability** – Suitable for small setups to large enterprise infrastructures.  
5. **Auto Discovery & Auto Registration** – Detects and adds new devices automatically.  
6. **Template-Based Configuration** – Simplifies monitoring by applying predefined monitoring rules.  
7. **API Support** – Allows integration with other systems for automation.  

---

## **Zabbix Architecture**  
Zabbix follows a **client-server model**, consisting of:  
1. **Zabbix Server** – The central component that stores, processes, and analyzes collected data.  
2. **Zabbix Agents** – Installed on monitored hosts to collect performance metrics.  
3. **Database (MySQL/MariaDB/PostgreSQL)** – Stores configuration and collected monitoring data.  
4. **Web Interface (Apache/Nginx + PHP)** – Provides an easy-to-use GUI for managing and visualizing data.  
5. **Proxies (Optional)** – Act as intermediaries for distributed monitoring.  

---

## **Installation & Configuration of Zabbix**  
### **1. Setting Up Zabbix Server**  
To deploy Zabbix, install the server along with its dependencies, including a database, a web server, and the Zabbix agent.  
- **Database Configuration:** Zabbix requires a database (MySQL, PostgreSQL) to store collected data.  
- **Web Interface Setup:** The Zabbix frontend is configured using Apache/Nginx and PHP.  
- **Zabbix Server & Agent Installation:** The agent collects data from the local machine.  



---

## **Zabbix Monitoring Capabilities**  
### **1. Server & Network Monitoring**  
- CPU, RAM, disk usage, and network bandwidth.  
- Service availability (HTTP, FTP, SSH, etc.).  
- Logs and system events tracking.  

### **2. Cloud & Container Monitoring**  
- Supports AWS, Azure, GCP integration.  
- Kubernetes and Docker container monitoring.  

### **3. Application Performance Monitoring (APM)**  
- Tracks application response times and resource usage.  
- Detects service failures and performance degradation.  

---

## **Zabbix Alerts & Notifications**  
- Alerts triggered when a metric crosses predefined thresholds.  
- Notifications sent via **email, SMS, Telegram, Slack, etc.**  
- Customizable escalation policies for issue resolution.  

---

## **Advanced Features in Zabbix**  
### **1. Auto Discovery**  
- Automatically detects new devices in the network.  
- Assigns monitoring rules based on predefined templates.  

### **2. Zabbix API**  
- Allows automation of monitoring tasks.  
- Integrates with third-party tools like Grafana for visualization.  

### **3. High Availability & Scalability**  
- Supports distributed monitoring using Zabbix proxies.  
- Can handle thousands of hosts and millions of metrics.  

---


<br>


## **Implementaion Steps**  




<br>
<br>




### Step 1: Update the System
Update and upgrade system packages.
```yml
sudo apt update && sudo apt upgrade -y
```

### Step 2: Install Zabbix Repository
Add the Zabbix repository and update package lists.
```yml
wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.0-4+ubuntu22.04_all.deb
sudo dpkg -i zabbix-release_6.0-4+ubuntu22.04_all.deb
sudo apt update
```

### Step 3: Install Zabbix Server, Frontend, Agent, and Database
```yml
sudo apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf \
  zabbix-sql-scripts zabbix-agent mariadb-server -y
```

### Step 4: Configure MySQL for Zabbix
Secure the database installation:
```yml
sudo mysql_secure_installation
```
Create Zabbix database and user:
```sql
CREATE DATABASE zabbix CHARACTER SET utf8mb4 COLLATE utf8mb4_bin;
CREATE USER 'zabbix'@'localhost' IDENTIFIED BY 'zabbix_pass';
GRANT ALL PRIVILEGES ON zabbix.* TO 'zabbix'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

### Step 5: Import Initial Schema
```yml
zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql -uzabbix -pzabbix_pass zabbix
```

### Step 6: Configure Zabbix Server
Edit configuration file and set database password.
```yml
sudo nano /etc/zabbix/zabbix_server.conf
```
Set:
```yml
DBPassword=zabbix_pass
```
Save and exit.

### Step 7: Set PHP Timezone
Edit Apache configuration file:
```yml
sudo nano /etc/zabbix/apache.conf
```
Set timezone (e.g., India):
```yml
php_value date.timezone Asia/Kolkata
```
Save and exit.

### Step 8: Start & Enable Services
```yml
sudo systemctl restart zabbix-server zabbix-agent apache2
sudo systemctl enable zabbix-server zabbix-agent apache2 mariadb
```

### Step 9: Access Zabbix Web Interface
Open browser:
```yml
http://<your_server_ip>/zabbix
```

### Step 10: Login to Zabbix
- **Username:** Admin  
- **Password:** zabbix  

<br>

# Adding a New Client to Monitor

### Step 1: Prepare the New Ubuntu Machine
```yml
sudo apt update && sudo apt upgrade -y
```

### Step 2: Install Zabbix Agent
```yml
wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.0-4+ubuntu22.04_all.deb
sudo dpkg -i zabbix-release_6.0-4+ubuntu22.04_all.deb
sudo apt update
sudo apt install zabbix-agent -y
```

### Step 3: Configure Zabbix Agent
Edit configuration file:
```yml
sudo nano /etc/zabbix/zabbix_agentd.conf
```
Set:
```yml
Server=<Zabbix_Server_IP>
ServerActive=<Zabbix_Server_IP>
Hostname=<Your_Client_Hostname>
```
Save and exit.

### Step 4: Start & Enable Zabbix Agent
```yml
sudo systemctl start zabbix-agent
sudo systemctl enable zabbix-agent
```

### Step 5: Add the New Host in Zabbix
1. Log in to Zabbix Web Interface.
2. Go to **Configuration > Hosts**.
3. Click **Create Host**.
4. Set:
   - **Host name:** Same as in `zabbix_agentd.conf`
   - **Groups:** Linux Servers
   - **Interfaces:** Add new client IP
5. Link **Template OS Linux**.
6. Save.

### Step 6: Verify Connection
Check on **Monitoring > Hosts** in the Zabbix dashboard.

Check agent status on the client:
```yml
systemctl status zabbix-agent
```

**Troubleshooting:**
- Ensure Zabbix Agent is running.
- Check firewall (port **10050** must be open).
- Verify network connectivity between Zabbix server and client.

<br>

✅ **Done! Your new host is now being monitored in Zabbix.**



<br>
<br>
<br>
<br>



# ***** Implementation Screenshots *****



<br>
<br>

# 1

![1](https://github.com/user-attachments/assets/626ab861-a6d1-45fc-abbf-c286f9551c14)







<br>
<br>


# 2

![2](https://github.com/user-attachments/assets/e88787af-b188-4e7e-ba08-3b410c96dbe0)



<br>
<br>


# 3

![3](https://github.com/user-attachments/assets/8e939ef7-3fb8-4237-afe6-1f1e11893673)




<br>
<br>


# 4

![4](https://github.com/user-attachments/assets/05ba0fda-38b3-4a7b-967e-cd0cbca95c8d)



<br>
<br>


# 5

![1 interface](https://github.com/user-attachments/assets/0124d493-4d4d-4642-bcc4-f6d7c7176a51)


<br>
<br>


# 6

![host 1](https://github.com/user-attachments/assets/a56e1b78-65e4-4d8c-8a97-d57985861829)


<br>
<br>

# 7

![host added 2](https://github.com/user-attachments/assets/03596c28-7f72-4094-b7be-fe8a20a036af)



<br>
<br>

# 8

![info](https://github.com/user-attachments/assets/b3847307-4251-4920-ab6b-7f8f6900635b)


<br>
<br>



# 9

![final](https://github.com/user-attachments/assets/1403e4cf-3bd2-4fcb-a19d-575708bd2ee3)




<br>
<br>

# 10


![Screenshot from 2025-04-05 03-39-02](https://github.com/user-attachments/assets/2a354f8f-eeca-46a4-9f1a-679d510fd6a5)









