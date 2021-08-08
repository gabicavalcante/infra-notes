# Set Up Logical Replication with PostgreSQL 12 on Google Compute Engine using Ubuntu 20 

DBaaS services are great (like Google Cloud SQL, Azure Database for PostgreSQL, Amazon RDS, etc), but sometimes I need to make a full setup, like use a custom extension or choose the OS. For this case, I need a virtual machine. 

Now I want to learn how I can configure logical replication using postgreSQL on Google Compute Engine, because setting up a SQL database is an unfamiliar topic for me. 

## postgreSQL Replication

### physical replication
  
  Replicate the entire database cluster to another server. everything will be replicated, so you can't replicate only certain databases or tables, but all changes to the primary node are replicated. You also can't use different platforms, as windows and linux.

### logical replication

Logical replication is independent from specific architecture or version. It follows a publish/subscribe model. The primary database will be the publisher and will send the changes to all subscribers databases. 

  The common use case I have found are:
  - managing access to replicated data to different users
  - for replicas for read-only operation (analytics scenario). if you need the replica for failover, the docs suggest pd_dump. 

Limitations:
large objects are not replicated using postgreSQL logical replication
it does not replicate the schema
tables must have a primary key or unique key


# setup VM

Firstly I created two VM and installed postgres.

1. go to the VM instance pages and Create a new instance. 
2. set name to db-main. 
3. choose ubuntu for OS, and for the version, choose ubuntu 20.04 LTS
4. in the firewall section, enter `db-main-postgres` for the Network tags field.
5. click to create the instance

Now you can go to the list of vm instances, and click the SSH button.

```
sudo apt update
sudo apt -y install postgresql postgresql-client postgresql-contrib
```

Set the password for `postgres` users.
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
- [How To Set Up Physical Streaming Replication with PostgreSQL 12 on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-set-up-physical-streaming-replication-with-postgresql-12-on-ubuntu-20-04)
- [2] https://medium.com/google-cloud/gcp-postgresql-compute-engine-snapshot-based-replication-eg-production-to-development-d90eb0477e90 
- [Azure PostgreSQL: Managed or Self-Managed?](https://cloud.netapp.com/blog/azure-cvo-blg-azure-postgresql-managed-or-self-managed)
- [Run a Postgres instance for cheap in Google Cloud](https://medium.com/joncloudgeek/run-a-postgres-instance-for-cheap-in-google-cloud-d435a6deac8b)
- https://severalnines.com/database-blog/postgresql-streaming-replication-vs-logical-replication
- https://www.postgresql.org/docs/10/logical-replication.html
- https://www.postgresql.org/docs/current/wal-intro.html
 - https://hevodata.com/learn/postgresql-logical-replication/
- https://www.digitalocean.com/community/tutorials/how-to-set-up-logical-replication-with-postgresql-10-on-ubuntu-18-04


# to read
- [ ] https://www.youtube.com/watch?v=NaPnYQBBdyU


