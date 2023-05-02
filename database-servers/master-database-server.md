## Database Servers

### Primary Database Server.
-------------------------------------------------------------
| Droplet   |      Value   |
|----------|:-------------:|
| Name |  db-server-1-main-api-ubuntu-22-04 |
| VPC  | NY3 |
| RAM |    1GB  |
| CPU |    1 AMD vCPU  |
| Memory |    25GB  |
| OS | Ubuntu 22.04 (LTS) x64 |
| Public IPv4 |    159.203.105.28  |
| Private IPv6 |    10.108.0.4 |

-------------------------------------------------------------
Log to the droplet via ssh. ***Only SSH Authentication is allowed, Password Authentication is turned off for security purposes***).
```bash
ssh root@159.203.105.28
```
-------------------------------------------------------------


### Configuration
#### Setup PostgreSQL
1. Install PostgreSQL
```bash
sudo apt update
sudo apt install postgresql postgresql-contrib
sudo systemctl start postgresql.service
```

2. Create ```admin``` superuser.
```bash
sudo adduser admin
sudo -u postgres createuser --interactive
# Output
# Enter name of role to add: admin
# Shall the new role be a superuser? (y/n) y
```

3. Create database.
```bash
sudo -i -u admin
psql -d postgres
CREATE DATABASE admin_db;
```

#### Configure Remote Connections
By default PostgreSQL is configured to be bound to "localhost". So we need to bind to our IP address to allow remote connection for the backend.
##### Configuring ```postgresql.conf```
1. Find the configuration file.
```bash
find / -name "postgresql.conf"                  #prints postgresql.conf location
sudo nano /dir/to/postgresql/config/file.conf   #use nano
sudo vi /dir/to/postgresql/config/file.conf   #use vim
```

2. In the ```postgresql.conf``` file and replace line
```bash
listen_addresses = 'localhost'
#to
listen_addresses = '*'
```

##### Configuring ```pg_hba.conf```
1. Find the configuration file.
```bash
find / -name "pg_hba.conf"                  #prints postgresql.conf location
sudo nano /dir/to/postgresql/config/file.conf   #use nano
sudo vi /dir/to/postgresql/config/file.conf   #use vim
```
2. Add following entry at the very end.
```
host    all             all              0.0.0.0/0                       md5
host    all             all              ::/0                            md5
```
Now we are able to connect to our postgresql server remotely.