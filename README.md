# MariaDB Replicate master to master

Master to Master Replicate MariaDB

Servers Scenario 

192.168.2.131 Centos 7

192.168.2.59 Windows10 XAMPP

<b>First configuration server</b>

<b>Centos 7</b>

<b>Edit </b>

$ sudo vim /etc/my.cnf.d/server.cnf

<b>Buscar [mysqld] y agreagar:</b>

server_id=1

# Master Replication
log-basename=master1

binlog-format=row

log_bin=mysql-bin

log_error=mysql-bin.err

binlog_do_db=gcasmo

report_host = centos-server

relay-log=mariadb-relay-bin


replicate-ignore-table=phpmyadmin.pma__recent

replicate-ignore-table=phpmyadmin.pma__userconfig

replicate-ignore-table=phpmyadmin.pma__bookmark

replicate-ignore-table=phpmyadmin.pma__history

replicate-ignore-table=phpmyadmin.pma__favorite

replicate-ignore-table=phpmyadmin.pma__table_uiprefs

replicate-ignore-table=phpmyadmin.pma__users

replicate-ignore-table=phpmyadmin.pma__navigationhiding

replicate-ignore-table=phpmyadmin.pma__savedsearches

replicate-ignore-table=phpmyadmin.pma__central_columns

replicate-ignore-table=phpmyadmin.pma__column_info

replicate-ignore-table=phpmyadmin.pma__designer_settings

replicate-ignore-table=phpmyadmin.pma__export_templates

replicate-ignore-table=phpmyadmin.pma__pdf_pages

replicate-ignore-table=phpmyadmin.pma__relation

replicate-ignore-table=phpmyadmin.pma__table_coords

replicate-ignore-table=phpmyadmin.pma__table_info

replicate-ignore-table=phpmyadmin.pma__tracking

replicate-ignore-table=phpmyadmin.pma__usergroups


$ systemctl restart mariadb.service

Abrir 192.168.2.131/phpmyadmin dirigirse a:

replication -> Add slave replication user

User name: replicabd

Host: 192.168.2.59

Password: P4ssw0rd.c0m2022

Re-type: P4ssw0rd.c0m2022


Or

$ mysql -u root -p

> STOP SLAVE;
> 
> GRANT REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'replicabd'@'192.168.2.59' IDENTIFIED BY 'P4ssw0rd.c0m2022';
> 
> FLUSH PRIVILEGES;
> 
> SHOW MASTER STATUS;

MariaDB [(none)]> SHOW MASTER STATUS;

+------------------+----------+--------------+------------------+

| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |

+------------------+----------+--------------+------------------+

| mysql-bin.000019 |      245 | gcasmo       |                  |

+------------------+----------+--------------+------------------+

1 row in set (0.00 sec)


MariaDB [(none)]>


Configuracion Maestro del Servidor Windows con XAMPP


Editar el archivo my.cnf


Descomentar

log-bin=mysql-bin

Agregar

binlog_do_db=gcasmo


Cambiar el server-id

server-id	=2


Abrir 192.168.2.59/phpmyadmin dirigirse a:

replication -> Add slave replication user

User name: replicabd

Host: 192.168.2.131

Password: P4ssw0rd.c0m2022

Re-type: P4ssw0rd.c0m2022


Or

Se agrega un usuario mediante SQL o interfaz de phpmyadmin

SQL

GRANT REPLICATION SLAVE, REPLICATION CLIENT  ON *.* TO 'replicabd'@'192.168.2.131' IDENTIFIED BY 'P4ssw0rd.c0m2022';



Configurar el servicio de esclavo en los servidores.


Iniciamos con el servidor Windows virtual que tiene la ip 192.168.2.59


En phpmyadmin ingresar a SQL insertar las siguientes lineas:


change master to

	master_host = '192.168.2.131',

	master_user = 'replicabd',

	master_password = 'P4ssw0rd.c0m2022',

	master_log_file = 'mysql-bin.000019',

	master_log_pos = 245


Ingresar al otro servidor con la ip 192.168.2.131 Centos(mac) a phpmyadmin

change master to

	master_host = '192.168.2.59',	

	master_user = 'replicabd',

	master_password = 'P4ssw0rd.c0m2022',

	master_log_file = 'mysql-bin.000001',

	master_log_pos = 564


Iniciar el servicio de replicacion se realiza de la siguiente manera:

Desde la interfaz de phpmyadmin

Replication -> Control slave -> Full start

