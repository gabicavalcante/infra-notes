# Set Up Logical Replication with PostgreSQL 12 on Google Compute Engine using Ubuntu 20 

DBaaS services are great (like Google Cloud SQL, Azure Database for PostgreSQL, Amazon RDS, etc), but sometimes I need to make a full setup, like use a custom extension or choose the OS. For this case, I need to a virtual machine. 

Now I want to learn how I can configure logical replication using postgreSQL on Google Compute Engine. 

## postgreSQL Replication

- physical replication:: replicate the entire database cluster to another server. everything will be replicated, so you cannot replicate only certain databases or tables.
- logical replication: 

# setup VM

Firstly I created two VM and installed the postgres.

1. go to the VM instance pages and Create a new instance. 
2. set name to db-main. 
3. choose ubuntu for OS, and for the version, choose ubuntu 20.04 LTS
4. in the firewall secion, enter `db-main-postgres` for the Network tags field.
5. click to create the instance

Now you can go to the list of vm instance, and click the SSH button.

```
sudo apt update
sudo apt -y install postgresql postgresql-client postgresql-contrib
```

Set the password for `postgres` user.
```
sudo -u postgres psql postgres
\password postgres
```

# setup firewal
TODO

# configure postgresql for logical replication

### db-main

- open `/etc/postgresql/12/main/postgresql.conf`
```
$ sudo nano /etc/postgresql/12/main/postgresql.conf
```
...
```
listen_addresses = 'localhost, db_main_private_ip_address'
```
...
```
wal_level = logical
```
edit 
```
sudo nano /etc/postgresql/12/main/pg_hba.conf
```
```
...
# TYPE      DATABASE        USER            ADDRESS                               METHOD
...
host         all            all             db_replica_private_ip_address/32      md5
```
...
```
sudo ufw allow from db_replica_private_ip_address to any port 5432
```

now
```
sudo systemctl restart postgresql
```

# references

- [1] https://cloud.google.com/community/tutorials/setting-up-postgres
- [3] https://www.digitalocean.com/community/tutorials/how-to-set-up-logical-replication-with-postgresql-10-on-ubuntu-18-04 
- [2] https://medium.com/google-cloud/gcp-postgresql-compute-engine-snapshot-based-replication-eg-production-to-development-d90eb0477e90 
- [Azure PostgreSQL: Managed or Self-Managed?](https://cloud.netapp.com/blog/azure-cvo-blg-azure-postgresql-managed-or-self-managed)
- [Run a Postgres instance for cheap in Google Cloud](https://medium.com/joncloudgeek/run-a-postgres-instance-for-cheap-in-google-cloud-d435a6deac8b)


# to read
- [ ] https://www.youtube.com/watch?v=NaPnYQBBdyU

