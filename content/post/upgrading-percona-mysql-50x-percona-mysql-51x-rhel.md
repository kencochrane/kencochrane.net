+++
date = "2011-02-15"
tags = ["percona","mysql","upgrade","centos5","rhel5","linux"]
categories = ["blog"]
topics = ["blog"]
slug = "upgrading-percona-mysql-50x-percona-mysql-51x-rhel"
url = "blog/2011/02/upgrading-percona-mysql-50x-percona-mysql-51x-rhel/"
title = "Upgrading Percona MySQL 5.0.x to Percona MySQL 5.1.x RHEL5 or CENTOS 5"
+++

I have been using the Percona build of MySQL 5.0.x for a little while
now, and I have been impressed. Now it is time to upgrade to the new
stable MySQL 5.1.x version. Here are my notes on upgrading on Red Hat
Enterprise Linux 5 (RHEL5) or CentOS5.

First we need to stop mysql

``` {.sourceCode .bash}
service mysql stop;
```

I'm assuming that you have the percona repo installed on your machine,
if not see follow this link on how to set it up. [Setting up Percona RPM
Repo](http://www.percona.com/docs/wiki/percona-server:release:start)

Now we need to remove the old MySQL 5.0 binaries. (Don't worry this will
leave your data.)

``` {.sourceCode .bash}
yum remove -y Percona-SQL*
```

Now install the new Percona MySQL 5.1.x binaries. (depending on your
connection this might take a few minutes)

``` {.sourceCode .bash}
yum install -y Percona-Server-devel-51 Percona-Server-shared-51 Percona-Server-test-51 Percona-Server-client-51 Percona-Server-server-51
```

During the install it will throw out some warnings, make sure you write
those down, and make the changes that it recommends. Here are a few of
mine.

``` {.sourceCode .bash}
110208 16:44:52 [Warning] '--log_slow_queries' is deprecated and will be removed in a future release. Please use ''--slow_query_log'/'--slow_query_log_file'' instead.
110208 16:44:52 [Warning] '--log' is deprecated and will be removed in a future release. Please use ''--general_log'/'--general_log_file'' instead.
```

Assuming the install went well, now we need to upgrade the data to the
new format. (replace &lt;mysql admin user&gt; with your mysql admin
username, and enter password when prompted)

``` {.sourceCode .bash}
mysql_upgrade -u <mysql admin username> -p
```

Now if your upgrade was anything like mine it had a bunch of errors like
this.

``` {.sourceCode .bash}
db_name.table_name
error    : Table upgrade required. Please do "REPAIR TABLE table_name" or dump/reload to fix it!
```

When you try to repair the table you get this.

``` {.sourceCode .bash}
db_name.table_name
note     : The storage engine for the table doesn't support repair
```

Thanks to Peter Zaitsev [mysql\_upgrade and Innodb
Tables](http://www.mysqlperformanceblog.com/2010/05/14/mysql_upgrade-and-innodb-tables/)
we know that we can do the following to fix it.

``` {.sourceCode .sql}
ALTER TABLE db_name.table_name ENGINE=INNODB;
```

We just need to run this command for each table that threw an error and
it should fix it. (If you have lots of data this might take a while, I
had some tables that took over 2 hours)

Once you think that you have all of the tables fixed, you can run this
command and it will check all of your tables and let you know if you are
good or not. If you still have errors, repeat the steps above until all
tables pass.

``` {.sourceCode .bash}
mysqlcheck -A --check-upgrade
```

Hopefully everything passed for you, and you now have a fully upgraded
database. It is important to note that there is another option besides
running the table alter. You could have dumped all of the data prior to
the upgraded and then restored it once the database was upgraded. This
process would most likely take a lot longer.
