+++
title = "Connecting to MySQL 4.1 with Old Libraries"
date = 2005-02-13T20:49:00Z
updated = 2005-02-13T20:57:39Z
categories = ["blog"]
topics = ["blog"]
slug = "connecting-to-mysql-4.1-with-old-libraries"
url = "blog/2005/02/connecting-to-mysql-4.1-with-old-libraries/"
+++

If you need to connect to a MySQL 4.1 Database using an old mysql library, you need to change the password for the user account that you want to connect with, so that it uses an old password. The new version of the database uses a different password algorithm which is much longer then the old version, which causes problems with the old library. To fix this you have to set the user password with the Old_password( ) function.<br /><span style="font-family:monospace;"><br /></span><span style="font-size:85%;">SET PASSWORD FOR '<em class="replaceable"><code>some_user</code></em>'@'<em class="replaceable"><code>some_host</code></em>' = OLD_PASSWORD('mypass'); </span><br /><br /><a href="http://dev.mysql.com/doc/mysql/en/password-hashing.html">http://dev.mysql.com/doc/mysql/en/password-hashing.html</a>
