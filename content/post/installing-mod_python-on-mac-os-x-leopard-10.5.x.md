+++
title = "Installing mod_python on Mac OS X Leopard (10.5.x)"
date = 2009-01-23T09:37:00Z
updated = 2009-02-01T13:01:36Z
tags = ["OSX", "django", "mod_python"]
categories = ["blog"]
topics = ["blog"]
slug = "installing-mod_python-on-mac-os-x-leopard-10.5.x"
url = "blog/2009/01/installing-mod_python-on-mac-os-x-leopard-10.5.x/"
+++

Apache2 comes pre installed on Mac OS X Leopard as does Python (2.5). But it doesn't come with mod_python. Here is what you need to do to get it to work.<br /><br />download the latest file from  http://www.apache.org/dist/httpd/modpython/ I downloaded (<a href="http://www.apache.org/dist/httpd/modpython/mod_python-3.3.1.tgz">mod_python-3.3.1.tgz</a>)<br /><blockquote>$ tar xvzf mod_python-3.3.1.tgz<br />$ cd mod_python-3.3.1<br />$ ./configure --with-apxs=/usr/sbin/apxs</blockquote><br /><br />Now we need to patch the make file so that it will work correctly. edit src/MakeFile<br /><br /><ul><br /> <li>Add <strong>-arch x86_64 -arch ppc -arch i386</strong> to the end of the LDFLAGS line.</li><br /> <li>Add <strong>-arch x86_64 -arch ppc -arch i386</strong> to the end of the CFLAGS line.</li><br /> <li>Under the mod_python.so target add the following to the line after the -c  <strong>-Wc,"-arch x86_64" -Wc,"-arch ppc" -Wc,"-arch i386"</strong> so my complete line looks like. <span><em>$(APXS) $(INCLUDES) -c -Wc,"-arch x86_64" -Wc,"-arch ppc" -Wc,"-arch i386" $(SRCS) $(LDFLAGS) $(LIBS)</em></span></li><br /></ul><br /><br />When that is done:<br /><blockquote>$ make<br />$ sudo make install</blockquote><br />and then:<br /><br /><blockquote>$ cd /etc/apache2<br />$ sudo cp httpd.conf httpd.conf.orig<br />$ sudo emacs /etc/apache2/httpd.conf</blockquote><br />Add the following to the httpd.conf file.<br /><br /><blockquote>LoadModule python_module libexec/apache2/mod_python.so</blockquote><br />Restart apache<br /><blockquote>$ sudo /usr/sbin/apachectl restart</blockquote><br /><br />Test configuration<br /><blockquote>sudo /usr/sbin/apachectl -t</blockquote><br /><br />That is all, if there are no errors, it worked!
