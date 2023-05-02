## Database Servers

### Backup Database Server.
-------------------------------------------------------------
| Droplet   |      Value   |
|----------|:-------------:|
| Name |  db-server-2-main-api-ubuntu-22-04 |
| VPC  | NY3 |
| RAM |    1GB  |
| CPU |    1 AMD vCPU  |
| Memory |    25GB  |
| OS | Ubuntu 22.04 (LTS) x64 |
| Public IPv4 |    159.203.169.186  |
| Private IPv6 |    10.108.0.7 |

-------------------------------------------------------------
Log to the droplet via ssh. ***Only SSH Authentication is allowed, Password Authentication is turned off for security purposes***).
```bash
ssh root@159.203.169.186
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

#### Configuring a special Role with Replication Permissions
1. Create Role ```replica```
```bash
sudo -u postgres psql
CREATE ROLE replica WITH REPLICATION PASSWORD '8Om@wJZn561!%bzk' LOGIN;
```

2. Append the following line to the end of the pg_hba.conf file
```bash
find / -name "pg_hba.conf"                  #prints postgresql.conf location
sudo nano /dir/to/postgresql/config/file.conf   #use nano
sudo vi /dir/to/postgresql/config/file.conf   #use vim
```

. . .
```
host    replication     test    0.0.0.0/32   md5
```

3. Auto Backup Master Database on Master
```
sudo -u postgres pg_basebackup -h  10.108.0.4 -p 5432 -U replica -D /var/lib/postgresql/14/main/ -Fp -Xs -R
```