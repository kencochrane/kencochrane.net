+++
title = "Plesk 8.6 cgi-bin trouble"
date = 2009-02-26T09:07:00Z
updated = 2009-02-26T09:28:49Z
tags = ["plesk", "cgi-bin", "python"]
categories = ["blog"]
topics = ["blog"]
slug = "plesk-8.6-cgi-bin-trouble"
url = "blog/2009/02/plesk-8.6-cgi-bin-trouble/"
+++

I recently worked on a project to quickly create a simple cgi-bin script to capture some data from a form on a mostly static website. The script was simple, take the data from the form, and send it via email. The script was written by a co-worker of mine using python. When we went to deploy the script on the server with Plesk 8.6, it was nothing but a pain in the butt.. I have documented some of the things we did below in order to get it working.<br /><br />Here are the errors that we were getting during the process in no particular order.<br /><ul><li>"target uid/gid (10001/2524 or 2523) mismatch with directory (10001/2524) or program (48/48)"</li><li>"uid: (10001/cashstarftp) gid: (2524/2524) cmd: mail_script.cgi"</li><li>"cannot get docroot information (/var/www/vhosts)"</li><li>"failed to open log file /var/log/httpd/suexec_log"</li><li>"Premature end of script headers: mail_script.py"</li></ul><br />Here are the different steps that we did to resolve the different errors:<br /><br /><blockquote>SSH in and type the following: "chown root:apache /usr/sbin/suexec"</blockquote><br />My permissions were also wrong for the following<br /><blockquote> /var/log/httpd, should be  drwx------    root  root<br /><br />/var/log/httpd/suexec_log, should be -rw-r--r--   root root<br /><br />/home/httpd/vhosts/your-domain-name.com/statistics, should be  dr-xr-x---    root        psaserv<br /><br />/home/httpd/vhosts/your-domain-name.com/cgi-bin should be, drwxr-x--x    ftpuser psaserv<br /><br />As most of you know but just in case scripts in the cgi-bin directory need to be -rwxr-xr-x   ftpuser psacln<br /><br />Replace "ftpuser" with the ftp user name for that domain<br /><br />And don't forget to restart Apache after making these changes.<br /></blockquote>There are probably more things that we ended up trying that I didn't document here, so feel free to post how you fixed your issue, so that others can benefit.
