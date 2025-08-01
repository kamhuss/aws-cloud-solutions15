# AWS Cloud Solutions Project
- The aim of this project was to establish a production environment for 2 company websites using AWS cloud solutions incorporating a reverse proxy technology.
- In this project, we will built a secure infrastructure inside AWS VPC (Virtual Private Cloud) network for a fictitious company that uses WordPress CMS for its main business website, and a Tooling Website (https://github.com//tooling) for their DevOps team.
- As part of the company’s desire for improved security and performance, a decision was made to use a reverse proxy technology from NGINX to achieve this.
- Cost, Security, and Scalability are the major requirements for this project. Hence, implementing the architecture designed below, ensure that infrastructure for both websites, WordPress and Tooling, is resilient to Web Server’s failures, can accomodate to increased traffic and, at the same time, has reasonable cost.

### Project Design Architecture Diagram

![](./Images/architecture%20diagram.PNG)

### Installation instructions

### Installation Commands for setting up Webserver, NGINX and Bation AMIs

- create security group first
- create certificate
- create ALB
- create Launch template
- create Austocaling



### Bastion ami installation

yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm

yum install -y dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm

yum install wget vim python3 telnet htop git mysql net-tools chrony -y

systemctl start chronyd

systemctl enable chronyd

### Nginx ami installation 
```
yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm

yum install -y dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm

yum install wget vim python3 telnet htop git mysql net-tools chrony -y

systemctl start chronyd

systemctl enable chronyd
```
### Configure selinux policies for the webservers and nginx servers
```
setsebool -P httpd_can_network_connect=1
setsebool -P httpd_can_network_connect_db=1
setsebool -P httpd_execmem=1
setsebool -P httpd_use_nfs 1
```
### This section will install amazon efs utils for mounting the target on the Elastic file system
```
git clone https://github.com/aws/efs-utils

cd efs-utils

yum install -y make

yum install -y rpm-build

make rpm 

yum install -y  ./build/amazon-efs-utils*rpm
```
### Setting up self-signed certificate for the nginx instance
```
sudo mkdir /etc/ssl/private

sudo chmod 700 /etc/ssl/private

openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/ACS.key -out /etc/ssl/certs/ACS.crt

sudo openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048
```

### Webserver ami installation 
```
yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm

yum install -y dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm

yum install wget vim python3 telnet htop git mysql net-tools chrony -y

systemctl start chronyd

systemctl enable chronyd
```
### Configure selinux policies for the webservers and nginx servers
```
setsebool -P httpd_can_network_connect=1
setsebool -P httpd_can_network_connect_db=1
setsebool -P httpd_execmem=1
setsebool -P httpd_use_nfs 1
```
### This section will install amazon efs utils for mounting the target on the Elastic file system
```
git clone https://github.com/aws/efs-utils

cd efs-utils

yum install -y make

yum install -y rpm-build

make rpm 

yum install -y  ./build/amazon-efs-utils*rpm
```

### Setting up self-signed certificate for the apache webserver instance
```
yum install -y mod_ssl

openssl req -newkey rsa:2048 -nodes -keyout /etc/pki/tls/private/ACS.key -x509 -days 365 -out /etc/pki/tls/certs/ACS.crt

vi /etc/httpd/conf.d/ssl.conf
```


### Logging into the RDS instance and create database for wordpress and tooling wordpress and tooling database
mysql -h acs-database.cdqpbjkethv0.us-east-1.rds.amazonaws.com -u ACSadmin -p 

CREATE DATABASE toolingdb;
CREATE DATABASE wordpressdb;

### References
[IP ranges](https://ipinfo.io/ips)

[ssh-gent forwarding on mobaxterm](http://docs.gcc.rug.nl/hyperchicken/ssh-agent-forwarding-mobaxterm/)

[Nginx reverse proxy server](https://www.nginx.com/resources/glossary/reverse-proxy-server/)

[Underdstanding ec2 user data](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html)

[Manually installing the Amazon EFS client](https://docs.aws.amazon.com/efs/latest/ug/installing-amazon-efs-utils.html#installing-other-distro)

[creating target groups for AWS Loadbalancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html)

[Self-Signed SSL Certificate for Apache](https://www.digitalocean.com/community/tutorials/how-to-create-a-self-signed-ssl-certificate-for-apache-on-centos-8)

[Create a Self-Signed SSL Certificate for Nginx](https://www.digitalocean.com/community/tutorials/how-to-create-a-self-signed-ssl-certificate-for-nginx-on-centos-7)

# Tasks completed
- Used AiCore provided subcription, created a Virtual Machine (VM) on Azure portal.
- Used the RDP protocol and Microsoft Remote Desktop to establish a secure connection to the VM.
- Installed SQL Server and SQL Server Management Studio (SSMS) on the VM.
- Created the production database by restoring it from provided backup file. The backup file corresponds to the renowned Microsoft SQL Server database known as AdventureWorks.
- Created an Azure SQL Database, which served as the target for migrating our on-premise database. We ensured that the associated SQL Server uses SQL login as the chosen authentication method. Additionally, we confirmed that the SQL Server has the appropriate firewall rules, specifically with our IP address added to the firewall settings.
- We installed and configured Azure Data Studio on the production Windows VM. We then established a connection to the existing on-premise database.
- Using Azure Data Studio, we established a connection to the newly created Azure SQL Database. This connection served as the conduit for schema and data migration between the two databases.
- After establishing connections to both databases, we installed the SQL Server Schema Compare extension within Azure Data Studio. Then used this extension to compare and subsequently migrate the schema from the on-premise database to the Azure SQL Database.
- For data migration phase, we installed the Azure SQL Migration extension within Azure Data Studio. We also had to install integration runtime. This facilitated the smooth transfer of data from our on-premise database to the Azure SQL Database.
- To confirm the success of the database migration process, we carried out a comprehensive validation by inspecting the data, schema, and any configurations of the migrated database, ensuring that the migration has been executed successfully and adheres to principles of data integrity.
- Generated a full backup of the On-Premise production database hosted on the Windows VM. This backup duplicates the database, providing a safety net in the event of unforeseen issues. The resultant backup file was stored in a designated location on the on premise computer.
- Azure Blob Storage account was created to serve as a secure online repository for your database backups. The backup file was uploaded to the Blob Storage container. This provided an extra layer of backup protection by making a redundant copy stored remotely.
- A development environment was created by provisioning a new Windows VM that mirrors the production environment including installing the releveant Install SQL Server and software on this VM to mimic the necessary database infrastructure. The databse backup was restored into this environment. The development environemnt can be used as a controlled and isolated environment where applications and software can be tested, developed, and experimented with, all without impacting the production systems.
- On your development Windows VM, SSMS was used to establish a management task that automates regular backups of the development database. The maintenance plan was configured for automated weekly backup schedule to ensure consistent protection for the evolving work and to simplify recovery for the development environment.
- In the production database we mimicked data loss. We deliberately removed critical data from your production database to replicate a scenario where data integrity is compromised. We confirmed its success by examining the Azure SQL Database using the connection already established in Azure Data Studio. Before the data loss, the SQL query was carried out: SELECT * FROM Person.EmailAddress. The results were 19972 entries. For data loss, the intention deletion SQL query was carried out: DELETE TOP (100) FROM Person.EmailAddress. To confirm the success of the deletion, the SQL query was repeated: SELECT * FROM Person.EmailAddress. The results were 19872 entries, which is 100 less than the original production database.
- We used Azure SQL Database Backup to restore the production database to a point just before the simulated data loss occurred, approximately 2 hours before. The success of the restored data was validated by examining the restored data through the connection in Azure Data Studio using the SQL query previously used to check that the entries were 19972.
-  A geo-replication for the production Azure SQL Database was set up with the new location at US east compared to the original primation location at UK South.
-  A planned failover to the secondary region was orchestrated. This act transitions operations to the secondary copy. After verifying the availability and data consistency of the failover database, a failback was performed to the primary region.
-  We configured Microsoft Entra ID for Azure SQL databse by enabling Microsoft Entra ID authentication for the primary SQL Server that hosts the Azure SQL production database. This step integrates Microsoft Entra ID as a trusted identity provider, allowing users to authenticate using the Microsoft Entra credentials. A Microsoft Entra admin was created permissions within the Azure SQL Database environment. This admin will have authority over user management and access control. To ensure the admin user had controls, we established a connection to the production database using Microsoft Entra credentials within Azure Data Studio.
- A new user account wascreated in Microsoft Entra ID, which will served as the DB Reader user. Using Azure Data Studio, admin user was connected to the production database using the Microsoft Entra admin credentials. The db_datareader role was assigned to the previously created DB Reader user using the SQL Query: CREATE USER [db_reader@users.theaicore.com] FROM EXTERNAL PROVIDER; ALTER ROLE db_datareader ADD MEMBER [db_reader@users.theaicore.com]; This role provides the user with read-only privileges. To test the permissions of the user to ensure the correct role has been assigned to this user, we reconnected to the production database using Azure Data Studio and the credentials of the new DB Reader user. The SQL Query: SELECT * FROM person.EmailAddress; was used to check users permission to read. Then the SQL Query: DELETE TOP (1) From person.EmailAddress;. The outcome showed a message "The DELETE permission was denied on the object 'EmailAddress', database 'my-database-restored', schema 'Person'". This verified that no writabilty permissions were available for the db_datareader user.

# File structure of the project
- README.md file contains all the updates

# License information
- Non required. Public repository.
