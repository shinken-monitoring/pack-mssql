pack-mssql
=======

Shinken configuration pack for Sqlserver databases

Prerequisites
========

Make sure the check_mssql_health from labs.consol.de is installed and properly configured. See http://labs.consol.de/lang/en/nagios/check_mssql_health/

As a shortcut the installation procedure for debian/ubuntu is : 

```
cd /tmp
apt-get install -y freetds-dev freetds-bin libdbi-perl libdbd-sybase-perl
sed -E -i "s/;(\s+tds version = ).*$/\1 8.0/g" /etc/freetds/freetds.conf
wget http://labs.consol.de/download/shinken-nagios-plugins/check_mssql_health-1.5.20.6.tar.gz
tar zxvf check_mssql_health-1.5.20.6.tar.gz
cd check_mssql_health-1.5.20.6
./configure --with-nagios-user=shinken --with-nagios-group=shinken --with-mymodules-dir=/var/lib/shinken/libexec --with-mymodules-dyn-dir=/var/lib/shinken/libexec
make
cp plugins-scripts/check_mssql_health /var/lib/shinken/libexec/
cd /tmp
git clone https://github.com/david-guenault/pack-mssql.git
shinken install --local pack-mssql
```

Macros
====

|MACRO|DEFAULT VALUE|DESCRIPTION|
|_MSSQLINSTANCE| |Instance on which we are going to connect (default empty)|
|_MSSQLUSER|$DOMAINUSER$ (ressource macro)|User name (see 'preparation of database' at http://labs.consol.de/lang/en/nagios/check_mssql_health/)|
|_MSSQLPASSWORD|$DOMAINPASSWORD$ (ressource macro)| User passwrod|
|_MSSQL_CONNECTION_CRIT|2|Time to connect to the server critical threshold|
|_MSSQL_CONNECTION_WARN|1|Time to connect to the server warning threshold|
|_MSSQL_CONNECTED_USERS_CRIT|80|Number of currently connected users|
|_MSSQL_CONNECTED_USERS_WARN|50|Number of currently connected users|
|_MSSQL_CPU_BUSY_CRIT|90|Cpu busy in percent|
|_MSSQL_CPU_BUSY_WARN|80|Cpu busy in percent|
|_MSSQL_IO_BUSY_CRIT|90|IO busy in percent|
|_MSSQL_IO_BUSY_WARN|80|IO busy in percent|
|_MSSQL_FULL_SCANS_CRIT|500|Full table scans per second|
|_MSSQL_FULL_SCANS_WARN|100|Full table scans per second|
|_MSSQL_TRANSACTIONS_CRIT|50000|Transactions per second per database|
|_MSSQL_TRANSACTIONS_WARN|10000|Transactions per second per database|
|_MSSQL_BATCH_REQUESTS_CRIT|200|Batch requests per second|
|_MSSQL_BATCH_REQUESTS_WARN|100|Batch requests per second|
|_MSSQL_LATCHES_WAITS_CRIT|50|Number of latch requests that could not be granted immediately|
|_MSSQL_LATCHES_WAITS_WARN|10|Number of latch requests that could not be granted immediately|
|_MSSQL_LATCHES_WAIT_TIME_CRIT|5|Average time for a latch to wait before the request is met|
|_MSSQL_LATCHES_WAIT_TIME_WARN|1|Average time for a latch to wait before the request is met|
|_MSSQL_LOCKS_WAITS_CRIT|500|The number of locks per second that had to wait|
|_MSSQL_LOCKS_WAITS_WARN|100|The number of locks per second that had to wait|
|_MSSQL_LOCKS_TIMEOUTS_CRIT|5|The number of locks per second that timed out|
|_MSSQL_LOCKS_TIMEOUTS_WARN|1|The number of locks per second that timed out|
|_MSSQL_LOCKS_DEADLOCKS_CRIT|5|The number of deadlocks per second|
|_MSSQL_LOCKS_DEADLOCKS_WARN|1|The number of deadlocks per second|
|_MSSQL_SQL_RECOMPILATIONS_CRIT|10|Re-Compilations per second|
|_MSSQL_SQL_RECOMPILATIONS_WARN|1|Re-Compilations per second|
|_MSSQL_SQL_INITCOMPILATIONS_CRIT|200|Initial compilations per second|
|_MSSQL_SQL_INITCOMPILATIONS_WARN|100|Initial compilations per second|
|_MSSQL_TOTAL_SERVER_MEMORY_CRIT|5000|The amount of memory that SQL Server has allocated to it|
|_MSSQL_TOTAL_SERVER_MEMORY_WARN|1000|The amount of memory that SQL Server has allocated to it|
|_MSSQL_MEM_POOL_DATA_BUFFER_HIT_RATIO_CRIT|80:|Data Buffer Cache Hit Ratio|
|_MSSQL_MEM_POOL_DATA_BUFFER_HIT_RATIO_WARN|90:|Data Buffer Cache Hit Ratio|
|_MSSQL_LAZY_WRITES_CRIT|40|Lazy writes per second|
|_MSSQL_LAZY_WRITES_WARN|20|Lazy writes per second|
|_MSSQL_PAGE_LIFE_EXPECTANCY_CRIT|180:|Seconds a page is kept in memory before being flushed|
|_MSSQL_PAGE_LIFE_EXPECTANCY_WARN|300:|Seconds a page is kept in memory before being flushed|
|_MSSQL_FREE_LIST_STALLS_CRIT|10|Requests per second that had to wait for a free page|
|_MSSQL_FREE_LIST_STALLS_WARN|4|Requests per second that had to wait for a free page|
|_MSSQL_CHECKPOINT_PAGES_CRIT|500|Dirty pages flushed to disk per second. usually by a checkpoint|
|_MSSQL_CHECKPOINT_PAGES_WARN|100|Dirty pages flushed to disk per second. usually by a checkpoint|
|_MSSQL_DATABASE_BACKUP_AGE_CRIT|72|Elapsed time (in hours) since a database was last backed up|
|_MSSQL_DATABASE_BACKUP_AGE_WARN|48|Elapsed time (in hours) since a database was last backed up|
|_MSSQL_DATABASE_BACKUP_AGE_EXCLUDED|^(?!(tempdb))|Database to be excluded from backup age check|

Usage
====

Create a host that inherit from mssql template and specifie the required configuration macros (at least _MSSQLUSER and _MSSQLPASSWORD either by modifying the $DOMAINUSER$ and $DOMAINPASSWORD$ resources macro or by replacing them by a username and a password. For security reason you should modify the resources macros instead of specifying the values in the host macro). In the following example you should replace XXX.XXX.XXX.XXX by the host name or the host ip address. It is really important to specify hostname as the host ip address or hostname (if in DNS) because the checks command use hostname and not the address. 

```
define host {
    name mssql1
    hostname XXX.XXX.XXX.XXX
    address XXX.XXX.XXX.XXX
    use mssql
}
```

Note: if you want to connect to a specific instance you must define the _MSSQLINSTANCE macro in the host definition (in the form of \INSTANCENAME).

```
define host {
    name mssql1
    hostname XXX.XXX.XXX.XXX
    address XXX.XXX.XXX.XXX
    use mssql
    _MSSQLINSTANCE \SQLEXPRESS
}
```

Exclude unwanted checks from pack
======================

If you do not want all of the checks provided by this pack you can use the service_exclude directive to filter unwanted checks (see http://shinken.readthedocs.org/en/latest/08_configobjects/host.html?highlight=service_exclude for more informations). 

```
define host {
    name mssql1
    hostname XXX.XXX.XXX.XXX
    address XXX.XXX.XXX.XXX
    use mssql
    service_exclude Mssql-lazy-writes,Mssql-sql-recompilations
}
```
