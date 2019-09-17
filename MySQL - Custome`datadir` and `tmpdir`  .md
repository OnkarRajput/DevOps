= Step 1 — Moving the MySQL Data Directory=

- Check existing data directory:
```  mysql -u root -p ```
```  mysql>select @@datadir; ```

- Shut down `MySQL` before we actually make changes to the data directory:
```  sudo systemctl stop mysql ```
```  sudo systemctl status mysql ```

- Copy the existing database directory to the new location with `rsync`
```  sudo rsync -av /var/lib/mysql /database/mysqldata ```

- Re-naming it, to avoid confusion that could arise from files in both the new and the old location: 
```  sudo mv /var/lib/mysql /var/lib/mysql.bak ```

= Step 2 — Pointing to the New Data Location =
- The datadir is set to `/var/lib/mysql` in the `/etc/mysql/mysql.conf.d/mysqld.cnf` file. Edit this file to reflect the new data directory:
```  sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf ```
- Find the line that begins with `datadir=` and change the path which follows to reflect the new location.
``` datadir	= /database/mysqldata ```
``` tmpdir= /database/mysqltmp ```
= Step 3 — Configuring AppArmor Access Control Rules =

- Tell AppArmor to let MySQL write to the new directory by creating an alias between the default directory and the new location. To do this, edit the AppArmor alias file:
``` sudo vim /etc/apparmor.d/tunables/alias ```
``` alias /var/lib/mysql/ -> /database/mysqldata  ```
``` sudo systemctl restart apparmor ```

= Step 4 — Restarting MySQL =
```  sudo mkdir /var/lib/mysql/mysql -p ```
```  sudo systemctl start mysql ```
```  sudo systemctl restart mysql ```
```  sudo systemctl status mysql ```

=== Check root data directory:
```  mysql -u root -p ```
```  mysql>select @@datadir; ```


> __Note__: If face any issue for `AVC apparmor="DENIED" operation="open" profile="/usr/sbin/mysqld"`
> ```  sudo vim /etc/apparmor.d/usr.sbin.mysqld ```
> - Allow data dir access
> ``` /database/mysqldata/  r, ```
> ``` /database/mysqldata/ ** rwk, ```


