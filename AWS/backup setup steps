
### changes open_files_limit of MYSQL

1). sudo vim /lib/systemd/system/mysql.service

add following lines to the bottom of the file(mysql.service):

LimitNOFILE=infinity
LimitMEMLOCK=infinity
LimitNOFILE=6000
LimitMEMLOCK=6000 


sudo systemctl daemon-reload

2). vim /etc/mysql/mysql.conf.d/mysql.conf

open_files_limit = 6000

sudo service mysql start

3). verify open open_files_limit


mysql -u root -p

SHOW variables like '%open_files_limit%'




# Creating a backup user with read-only permission for MySQL DB

For taking a backup of mysql we must use a separate user who has read-only permissions. So that we can always back up our database with safety.

CREATE USER 'dbdump'@'localhost' IDENTIFIED BY 'aZ+36*[zazca7p9';
GRANT SELECT, LOCK TABLES, SHOW VIEW, EVENT, TRIGGER ON `*`.* TO 'dbdump'@'localhost';
FLUSH PRIVILEGES;
SHOW GRANTS FOR 'dbdump'@'localhost';

GRANT SELECT, LOCK TABLES, EVENT, TRIGGER, SHOW VIEW, CREATE ROUTINE, EXECUTE ON `bi`.* TO 'dbdump'@'localhost'; 
GRANT SELECT, LOCK TABLES, EVENT, TRIGGER, SHOW VIEW, CREATE ROUTINE, EXECUTE ON `encoredb`.* TO 'dbdump'@'localhost'; 
GRANT SELECT, LOCK TABLES, EVENT, TRIGGER, SHOW VIEW, CREATE ROUTINE, EXECUTE ON `financialForms`.* TO 'dbdump'@'localhost'; 
GRANT SELECT, LOCK TABLES, EVENT, TRIGGER, SHOW VIEW, CREATE ROUTINE, EXECUTE ON `forms\_management`.* TO 'dbdump'@'localhost'; 
GRANT SELECT, LOCK TABLES, EVENT, TRIGGER, SHOW VIEW, CREATE ROUTINE, EXECUTE ON `hp\_wit`.* TO 'dbdump'@'localhost';



########### add  ssh user

adduser dbdumpuser
pass : dbdumpuser@50089


mkdir -p /opt/mount_point_backup
mkdir -p /opt/bin
sudo vim /opt/bin/mysql_dump.sh

sudo chown dbdumpuser:dbdumpuser -R /opt/mount_point_backup
sudo chown dbdumpuser:dbdumpuser -R /opt/bin

######## add ssh user in encore file

vim /opt/mount_point/encoresite/conf/jdbc.properties

# Property that determines which database to use with an AbstractJpaVendorAdapter
jpa.database=MYSQL

db.user=dbbumpuser
db.password=dbbumpuser

ssh.port=25499


#### create IAM user for s3 bucket access and create s3 bucket

S3 bucket name : witfin-production-backup/dbdump

IAM User : witfinproduction
Access key ID : AKIAS65ZA5SJFQUVGHG5
Secret access key : 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


policy name : witfin-production-data-backup


{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:ListAllMyBuckets"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::witfin-production-backup"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:GetObject",
                "s3:DeleteObject"
            ],
            "Resource": [
                "arn:aws:s3:::witfin-production-backup/*"
            ]
        }
    ]
}





##### congigure s3 cli in user 

apt-get update && apt-get install python-pip
pip install awscli
aws --version

aws configure

























DROP USER 'dbdump'@'localhost';




CREATE USER 'backup'@'localhost' IDENTIFIED BY 'secret';
GRANT SELECT, SHOW VIEW, RELOAD, REPLICATION CLIENT, EVENT, TRIGGER ON *.* TO 'backup'@'localhost';
GRANT LOCK TABLES ON *.* TO 'backup'@'localhost';


GRANT ALL PRIVILEGES ON * . * TO 'newuser'@'localhost';

GRANT SELECT, SHOW VIEW, LOCK TABLES, RELOAD,
    REPLICATION CLIENT
    ON *.* TO 'backupuser'@'localhost';




CREATE USER 'MYBACKUPUSER'@'%' IDENTIFIED BY 'MYPASSWORD';
> GRANT SELECT, LOCK TABLES ON 'MYDATABASE'.* TO 'backup'@'%';



GRANT SELECT, LOCK TABLES ON *.* TO 'MYBACKUPUSER'@'%' IDENTIFIED BY 'MYPASSWORD';


