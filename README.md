# **Zabbix Server & Client Configuration on Single VM (Ubuntu 22.04)**

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

![1](https://github.com/user-attachments/assets/626ab861-a6d1-45fc-abbf-c286f9551c14)







<br>
<br>



![2](https://github.com/user-attachments/assets/e88787af-b188-4e7e-ba08-3b410c96dbe0)



<br>
<br>




![3](https://github.com/user-attachments/assets/8e939ef7-3fb8-4237-afe6-1f1e11893673)




<br>
<br>



![4](https://github.com/user-attachments/assets/05ba0fda-38b3-4a7b-967e-cd0cbca95c8d)



<br>
<br>




![1 interface](https://github.com/user-attachments/assets/0124d493-4d4d-4642-bcc4-f6d7c7176a51)


<br>
<br>




![host 1](https://github.com/user-attachments/assets/a56e1b78-65e4-4d8c-8a97-d57985861829)


<br>
<br>


![host added 2](https://github.com/user-attachments/assets/03596c28-7f72-4094-b7be-fe8a20a036af)



<br>
<br>


![info](https://github.com/user-attachments/assets/b3847307-4251-4920-ab6b-7f8f6900635b)


<br>
<br>





![final](https://github.com/user-attachments/assets/1403e4cf-3bd2-4fcb-a19d-575708bd2ee3)
