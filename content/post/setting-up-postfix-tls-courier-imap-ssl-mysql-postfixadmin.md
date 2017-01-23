+++
title = "Setting up Postfix (TLS) / Courier-IMAP (SSL) / MySQL / Postfixadmin"
date = 2005-02-08T09:37:00Z
updated = 2006-07-07T17:31:14Z
categories = ["blog"]
topics = ["blog"]
slug = "setting-up-postfix-tls-courier-imap-ssl-mysql-postfixadmin"
url = "blog/2005/02/setting-up-postfix-tls-courier-imap-ssl-mysql-postfixadmin/"
+++

This is an install guide for setting up a secure mail server using Postfix, Courier-IMAP, MySQL, and Postfixadmin on Debian Woody.
<br />
<br />Before we begin here are a few notes regarding how things are handled:
<br /><ul>   <li>Virtual domains - All domains including the local domain are handled as virtual domains. This allows a central location for all mail.</li>   <li>All interactions with the server are authenticated and encrypted using libsasl2 and TLS/SSL (Please see the packages section for what to install)
<br /></li> </ul><span style="font-weight: bold;">1. Packages to Install </span>
<br />
<br />This install guide assumes the following packages are already installed:
<br /><ul> <li>mysql-client</li><li>mysql-server</li><li>openssl (to create the certificate)</li><li>apache
<br /></li> </ul> To add Postfix add the following line to your /etc/apt/sources.list
<br />
<br />deb http://www.backports.org/test/postfix/ ./
<br />
<br />Install the following packages:
<br /><ul><li>Postfix 2.1.4-4</li>   <li>Postfix-mysql 2.1.4-4</li>   <li>Postfix-pcre 2.1.4-4</li>   <li>Postfix-tls 2.1.4-4</li><li>libsasl2 (the Cyrus SASL library)</li>   <li>libsasl2-modules</li>   <li>libsasl2-modules-sql</li>  </ul>Note: Make sure you use the source above to install the packages. DON'T use the stable version of backports to install Postfix and libsasl. The stable version of Postfix is linked against libsasl1 which does not allow authentication via the libsasl2 sql module.
<br />
<br />Comment out the previous package line and add the following line in your sources.list file:
<br />
<br />deb http://www.backports.org/debian stable courier
<br />
<br />Install the following packages:
<br /><ul>   <li>courier-authdaemon 0.47-2</li>   <li>courier-authmysql 0.47.2</li>   <li>courier-base 0.47.2</li>   <li>courier-imap 3.0.8-2
<br /></li>   <li>courier-imap-ssl 3.0.8-2</li> </ul>Finally unpack the latest version of Postfixadmin (2.1.0 as of this writing)  into your web directory.
<br /><span style="font-weight: bold;"><span style="font-weight: bold;"><span style="font-weight: bold;"><span style="font-weight: bold;"><span style="font-weight: bold;"></span></span>
<br />
<br /></span></span>2. Create the Tables in MySQL</span>
<br />
<br />Go to the postfixadmin directory that you unpacked and run the DATABASE_MYSQL.TXT in MySQL to setup the complete table structure for Postfix.
<br />
<br />It should create the following 7 tables under a database called 'postfix':
<br /><ul>   <li>admin</li>   <li>alias</li>   <li>domain</li>   <li>domain_admins</li>   <li>log</li>   <li>mailbox</li>   <li>vacation</li> </ul> <span style="font-weight: bold;">3. Create a Mail Directory<span style="font-weight: bold;"><span style="font-weight: bold;">
<br />
<br /></span></span></span>Create a directory to have all your virtual users mail dropped in, this directory needs
<br />to be owned by Postfix.
<br /><pre>% mkdir /usr/local/virtual
<br />% chown -R postfix:postfix /usr/local/virtual
<br />% chmod -R 771 /usr/local/virtual</pre> <span style="font-weight: bold;">4. Postfix Configuration</span>
<br />
<br />Use the following /etc/postfix/main.cf file. The items in <> are values you need to change.
<br />
<br />myhostname= <<span style="font-style: italic;">your.hostname</span>>
<br />mydomain= <<span style="font-style: italic;">your.domain</span>>
<br />mydestination =
<br />myorigin = /etc/mailname
<br />
<br />virtual_maps = mysql:/etc/postfix/mysql_virtual_alias_maps.cf
<br />virtual_alias_maps = $virtual_maps
<br />virtual_gid_maps = static:<<span style="font-style: italic;">postfix group id from /etc/passwd</span>>
<br />virtual_mailbox_base = <<span style="font-style: italic;">/your/mailbox/path</span>>
<br />virtual_mailbox_domains = mysql:/etc/postfix/mysql_virtual_domains_maps.cf
<br />virtual_mailbox_maps = mysql:/etc/postfix/mysql_virtual_mailbox_maps.cf
<br />virtual_minimum_uid =  <<span style="font-style: italic;">postfix user id from /etc/passwd</span>>
<br />virtual_transport = virtual
<br />virtual_uid_maps = static:<<span style="font-style: italic;">postfix user id from /etc/passwd</span>>
<br />
<br /># SMTP Authentication
<br />smtpd_sasl_auth_enable = yes
<br />broken_sasl_auth_clients = yes
<br />smtpd_sasl_security_options = noanonymous
<br />smtpd_recipient_restrictions = permit_mynetworks, permit_sasl_authenticated, reje
<br />ct_unauth_destination
<br />smtpd_use_tls = yes
<br />smtpd_tls_cert_file = /etc/postfix/smtpd.cert
<br />smtpd_tls_key_file = /etc/postfix/smtpd.key
<br />append_dot_mydomain = no
<br />
<br />Notes: Leave mydestination blank if you want your local domain to be considered virtual. If you list your local domain in mydestination, as is commonly the case, you need to have a system user for every email user. Postfix will look for a system user corresponding to the email user and if none is found it will bounce the mail. It will also not deliver to the mailbox path.
<br />
<br />Create the following files in /etc/postfix:
<br />
<br />mysql_virtual_alias_maps.cf
<br />
<br />user = postfix
<br />password = <passwd>
<br />hosts = localhost
<br />dbname = postfix
<br />table = alias
<br />select_field = goto
<br />where_field = address
<br />
<br />mysql_virtual_domains_maps.cf
<br />
<br />user = postfix
<br />password = <passwd>
<br />hosts = localhost
<br />dbname = postfix
<br />table = domain
<br />select_field = description
<br />where_field = domain
<br />
<br />mysql_virtual_mailbox_maps.cf
<br />
<br />user = postfix
<br />password = <passwd>
<br />hosts = localhost
<br />dbname = postfix
<br />table = mailbox
<br />select_field = maildir
<br />where_field = username
<br />
<br />Now go to /etc/postfix/master.cf and change first smtp line to the following:
<br />
<br />smtp      inet  n       -       n       -       -       smtpd
<br />
<br />This prevents postfix from running in a chrooted jail.
<br />
<br /><span style="font-weight: bold;">5. Postfix, Authenticated SMTP  and TLS encryption</span>
<br />
<br />By default postfix only allows communication between users defined in <span class="emphasis"><em>mynetworks</em></span>. This prevents an <span class="emphasis"><em>open relay</em></span> that spammers can abuse to send out spam. This however, also prevents users of your domain to send out email to domains outside your box. So you have to authenticate your users with a username and password to allow them to be part of your circle of trust. Most email cilents such as Thunderbird have this feature included.</passwd></passwd></passwd>
<br />
<br />Use the following from Christoph Haas's tutorial located in http://workaround.org/articles/ispmail-sarge/ to setup authentication:
<br />
<br />As written earlier Postfix uses the Cyrus SASL library for authenticated SMTP. So you need to tell Postfix how to access the data storage that keeps the usernames and passwords. This is easy. Create a directory <span class="emphasis"><em>/etc/postfix/sasl</em></span> to put the SASL config file there: 			 <pre class="screen">mkdir /etc/postfix/sasl
<br /></pre> <p> 				Now you need to create a file <span class="emphasis"><em>smtpd.conf</em></span> 				in that directory like this: 			</p> <pre class="screen">pwcheck_method: auxprop
<br />auxprop_plugin: sql
<br />allowanonymouslogin: no
<br />mech_list: plain login cram-md5 digest-md5
<br />sql_engine: mysql
<br />sql_hostnames: localhost
<br />sql_user: postfix
<br />sql_passwd: <passwd>
<br />sql_database: postfix
<br />sql_select: select password from mailbox where username='%u@%r'
<br /></pre> <p> (If you have trouble with SASL you may consider inserting a line like "log_level: 7" here. It will write more verbose information to the log files and perhaps help to find the cause.) </p> <p> An important step is to encrypt the SMTP session. Otherwise the username and password could be transmitted in a very insecure way if the mail client chose to use one of plaintext authentication methods). So I encourage you to encrypt that communication using TLS. TLS is short for Transport Layer Security (RFC2246) and in short terms uses SSL (Secure Socket Layer) which encrypts the mail connection between the road-warrior and the mail server. </p> <p> First you will need an SSL certificate. If you don't want to pay for one from your favorite trustcenter you can well use a self-signed one. (Personal note: I wonder how paying for something makes it more trusted.) The only drawback: the mail client does not know about your CA (certificate authority) and will spit out a warning to the user. Either tell the users to ignore the warning or let them install the certificate on their computers. </p> <p> 				For a certificate that is valid for one year for the hostname 				smtp.domain.tld you would type this: 			</p> <pre class="screen">openssl req -new -outform PEM -out smtpd.cert -newkey rsa:2048 -nodes -keyout smtpd.key -keyform PEM -days 365 -x509
<br /></pre> <p> You will then be asked a few question about the fields of the certificate. It does not matter what you enter. Just fill the fields. One exception though - the "Common Name" must be the hostname of your mail server. Example session: </p> <pre class="screen">Country Name (2 letter code) [AU]:<span class="emphasis"><em>DE</em></span>
<br />State or Province Name (full name) [Some-State]:<span class="emphasis"><em>Hamburg</em></span>
<br />Locality Name (eg, city) []:<span class="emphasis"><em>Hamburg</em></span>
<br />Organization Name (eg, company) [Internet Widgits Pty Ltd]:<span class="emphasis"><em>workaround.org email services</em></span>
<br />Organizational Unit Name (eg, section) []:<span class="emphasis"><em>Master of Disaster</em></span>
<br />Common Name (eg, YOUR name) []:<span class="emphasis"><em>smtp.domain.tld</em></span>
<br />Email Address []:<span class="emphasis"><em>postmaster@domain.tld</em></span> </pre> <p> After a short moment you will get two files: "smtpd.key" (the private key file) and "smtpd.cert" (the certificate). Move these two files into <span class="emphasis"><em>/etc/postfix</em></span>. 			</p>  				Make sure at least the key file is not readable for the whole wide world:  				<b class="command">
<br />chmod o= /etc/postfix/smtpd.key</b><b class="command">
<br /><span style="font-weight: bold;"></span></b><b class="command">
<br /></b><passwd><passwd><passwd><span style="font-weight: bold;">6. Configuring Courier-IMAP-SSL</span>
<br />
<br /></passwd></passwd></passwd>Edit <span class="emphasis"><em>/etc/courier/authdaemonrc</em></span> and change authmodulelist to <span class="emphasis"><em>"authmysql"</em></span> like this: 		 <pre class="screen"><font><font>authmodulelist="authmysql"
<br /></span></span></pre><font><font><font><font><font><font><font> </span></span></span></span></span></span></span><p><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font>Add or Change  <span style="font-style: italic;">/etc/courier/authmysqlrc </span>to contain the following:
<br /></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></p><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font> </span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span><pre class="screen"><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font>MYSQL_SERVER            localhost
<br />MYSQL_USERNAME          postfix
<br />MYSQL_PASSWORD          <passwd>
<br />MYSQL_SOCKET            </passwd>
<br />MYSQL_PORT              3306
<br />MYSQL_DATABASE          postfix
<br />MYSQL_USER_TABLE        mailbox
<br />#MYSQL_CRYPT_PWFIELD    password #Comment this out
<br />MYSQL_CLEAR_PWFIELD     password
<br />MYSQL_UID_FIELD         '<postfix>'
<br />MYSQL_GID_FIELD         '<postfix>'
<br />MYSQL_LOGIN_FIELD       username
<br />MYSQL_HOME_FIELD        '/location/to/mail/dir'
<br />MYSQL_NAME_FIELD        name
<br />MYSQL_MAILDIR_FIELD     maildir
<br /></postfix></postfix></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></pre><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font><font> Notes: Make sure you have no spaces only tabs. Enclose user id and gid in single quotes.
<br />
<br /><span style="font-weight: bold;">7. Final Comments</span>
<br />
<br />By default libsasl2 only supports clear text passwords so make sure the user passwords in the mysql database are readable. Since our connections are encrypted using TLS/SSL this is not much of an issue. Make sure Postfixadmin is set for clear text passwords. Your config.inc.php in your Postfixadmin directory should have the following:
<br />
<br />$CONF['encrypt'] = 'cleartext';
<br />
<br /><span style="font-weight: bold;">8. Helpful Links and Howto's
<br />
<br /></span>Much of the information for this guide was gathered from the following links:
<br />
<br />http://high5.net/howto/
<br />http://workaround.org/articles/ispmail-sarge/
<br />http://brunny.com/content/view/12/50/<span style="font-weight: bold;">
<br />
<br /></span><span style="font-weight: bold;"><span style="font-weight: bold;"></span>
<br /></span><span style="font-weight: bold;"><span style="font-weight: bold;"><span style="font-weight: bold;"> </span></span></span> </span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span>
