
Goal: create a Jupyter notebook server that is available from the public web and can connect to a PostgreSQL database (on EC2) in a private subnet.

* VPC
* Two subnets - private and public
* Internet Gateway
  * attach it to VPC
* Route Table
  * name: public-jupyter-pg-rt
  * add route to Internet Gateway
  * create routes for 0.0.0.0/0 and IPv6
  * associate with public subnet
    * only associate with jupyter subnet, not db subnet

* Security Groups 
  * Create security groups for the jupyter and db instances
  * for jupyter-sg, allow inbound traffic on port 22 and 8888 from anywhere
  * for db-sg, allow inbound traffic on port 5432 from the jupyter-sg security group
  * for both, allow outbound traffic on all ports

Next steps:

* Create an EC2 instance for the jupyter server
* Create an EC2 instance for the PostgreSQL database
* Connect the two instances


## Troubleshooting

* Use Ubuntu Linux instead of Amazon Linux 2023 for pg instance
  * latest version of PostgreSQL is not available in Amazon Linux 2023
  * Easier to install PG on Ubuntu


## Install PG on Ubuntu

```bash
sudo apt update && sudo apt upgrade -y
curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/postgresql.gpg
echo "deb [signed-by=/etc/apt/trusted.gpg.d/postgresql.gpg] https://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" | sudo tee /etc/apt/sources.list.d/pgdg.list
sudo apt update

sudo apt install -y postgresql-17 postgresql-client-17
sudo apt install -y postgresql-client-17
```

Start the service:

```bash
sudo pg_ctlcluster 17 main start
sudo systemctl start postgresql
sudo systemctl enable postgresql
sudo systemctl status postgresql
```

Create the user and database

```bash
sudo -i -u postgres
psql
```

```sql
CREATE DATABASE jupyterdb;
CREATE USER jupyter_user WITH ENCRYPTED PASSWORD 'passw0rd';
GRANT ALL PRIVILEGES ON DATABASE jupyterdb TO jupyter_user;
```

Access the database as the new user

```bash
psql -U <username> -d <database>
```

### Config Postgress for remote access

```bash
sudo nano /etc/postgresql/17/main/postgresql.conf
```

Change the listen_addresses to `'*'` or the IP address/range of the jupyter server

```conf
listen_addresses = '*'
```

```bash
sudo nano /etc/postgresql/17/main/pg_hba.conf
```

```conf
host    all             all             0.0.0.0/0               md5
```

```bash
sudo systemctl restart postgresql
```

Access DB remotely

```bash
psql -h <ip> -U <username> -d <database>
```

## Install Jupyter

Troubleshooting:

Need to allow jupyter to listen on all IP addresses in the jupyter config file.

```bash
sudo vim /etc/jupyter/jupyter_notebook_config.py
```

in the config file, add the following lines:

```python
c.NotebookApp.ip = '0.0.0.0'          # Allow connections from any IP
c.NotebookApp.port = 8888             # Port for Jupyter Notebook
c.NotebookApp.open_browser = False    # Prevent auto browser launch
c.NotebookApp.allow_root = True       # Allow running Jupyter as root (if needed)
c.NotebookApp.password = 'sha1:your_hashed_password_here'  # For security
```
