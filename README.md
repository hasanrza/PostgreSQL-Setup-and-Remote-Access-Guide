# PostgreSQL Setup and Remote Access Guide

This guide provides a step-by-step process for setting up PostgreSQL on a DigitalOcean Droplet, importing a database, and enabling remote access using **pgAdmin** or **DBeaver**.

---

## **Step 1: Connect to Your Server**
First, SSH into your DigitalOcean Droplet:
```sh
ssh root@your_server_ip
```
Replace `your_server_ip` with your actual server IP address.

---

## **Step 2: Update and Install PostgreSQL**
### **For Ubuntu/Debian:**
```sh
sudo apt update && sudo apt upgrade -y
sudo apt install postgresql postgresql-contrib -y
```

### **For CentOS/Rocky Linux:**
```sh
sudo yum install -y postgresql-server postgresql-contrib
sudo postgresql-setup initdb
sudo systemctl start postgresql
sudo systemctl enable postgresql
```

---

## **Step 3: Start and Enable PostgreSQL**
After installation, start and enable the PostgreSQL service.
```sh
sudo systemctl start postgresql
sudo systemctl enable postgresql
```

Verify the status:
```sh
sudo systemctl status postgresql
```

---

## **Step 4: Secure and Configure PostgreSQL**
### **1. Switch to the PostgreSQL User**
```sh
sudo -i -u postgres
```

### **2. Access PostgreSQL Shell**
```sh
psql
```

### **3. Create a New User and Database**
```sql
CREATE USER your_user WITH PASSWORD 'your_password';
CREATE DATABASE your_database OWNER your_user;
GRANT ALL PRIVILEGES ON DATABASE your_database TO your_user;
```

### **4. Exit PostgreSQL**
```sh
\q
exit
```

---

## **Step 5: Import `cms.sql` into PostgreSQL**
### **1. Switch to the PostgreSQL User**
```sh
sudo -i -u postgres
```

### **2. Import the SQL File Locally on DigitalOcean**
```sh
psql -U postgres -d cmsdb -f /var/www/html/cms/cms.sql
```

### **3. Import the SQL File Remotely**
```sh
psql -h your_server_ip -U postgres -d cmsdb -f /var/www/html/cms/cms.sql
```

### **4. Verify the Import**
```sh
psql -U your_user -d your_database
\dt
```
If tables appear, the import was successful.

### **5. Exit PostgreSQL**
```sh
\q
exit
```

---

## **Step 6: Configure PostgreSQL for Remote Access**
### **1. Edit `postgresql.conf`**
```sh
sudo nano /etc/postgresql/16/main/postgresql.conf
```
Find the line:
```ini
listen_addresses = 'localhost'
```
Change it to:
```ini
listen_addresses = '*'
```
Save and exit (`CTRL+X`, `Y`, `ENTER`).

### **2. Edit `pg_hba.conf`**
```sh
sudo nano /etc/postgresql/14/main/pg_hba.conf
```
Add this at the end to allow remote connections:
```ini
host    all             all             0.0.0.0/0               md5
host    all             all             ::/0                    md5
```
Save and exit.

### **3. Restart PostgreSQL**
```sh
sudo systemctl restart postgresql
```

### **4. Allow PostgreSQL in Firewall**
```sh
sudo ufw allow 5432/tcp
sudo ufw reload
```

---

## **Step 7: Access PostgreSQL Remotely**
### **pgAdmin Setup**
1. Open **pgAdmin**.
2. Click on **Servers > Create > Server**.
3. Under the **General** tab, enter:
   - **Name:** `My Remote DB`
4. Under the **Connection** tab:
   - **Host name/address:** `your_server_ip`
   - **Port:** `5432`
   - **Username:** `your_user`
   - **Password:** `your_password`
5. Click **Save** and **Connect**.

### **DBeaver Setup**
1. Open **DBeaver**.
2. Click **New Connection > PostgreSQL**.
3. Enter:
   - **Host:** `your_server_ip`
   - **Port:** `5432`
   - **Database:** `your_database`
   - **Username:** `your_user`
   - **Password:** `your_password`
4. Click **Finish**.

---

## **Step 8: Final Verification**
Run a simple query in **pgAdmin** or **DBeaver**:
```sql
SELECT table_name FROM information_schema.tables WHERE table_schema = 'public';
```
If tables appear, your remote access is successful.

---

## **Conclusion**
You have successfully set up PostgreSQL, imported your database, and configured remote access. You can now manage your database efficiently using **pgAdmin** or **DBeaver**.

## Author
Developed by Hasan Raza

📧 Contact: hasanraz562@gmail.com

