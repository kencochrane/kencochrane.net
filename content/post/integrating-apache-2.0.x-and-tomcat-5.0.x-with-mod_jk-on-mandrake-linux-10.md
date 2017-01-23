+++
title = "Integrating Apache 2.0.x and Tomcat 5.0.x with mod_jk on Mandrake Linux 10"
date = 2005-02-08T09:21:00Z
updated = 2005-02-08T11:56:00Z
categories = ["blog"]
topics = ["blog"]
slug = "integrating-apache-2.0.x-and-tomcat-5.0.x-with-mod_jk-on-mandrake-linux-10"
url = "blog/2005/02/integrating-apache-2.0.x-and-tomcat-5.0.x-with-mod_jk-on-mandrake-linux-10/"
+++


 When it comes to Linux, nothing comes easy for me, but I get it done. Tonight I tried to install the connector between Apache and Tomcat. I had basic instructions for that, and he even e-mailed me the required mod_jk.so file. Unfortunately, Mandrake 10 didn't like it and I had to manually build it from the source. What a pain, but the instruction below helped quite a bit, I just had to install "autoconf" to get it to work. Mandrake asked for the installation CD to be inserted. I already had the other components (gcc).

 Then another late night saga began. It's all in the details. The generic instructions didn't work for me, as usual. So, below you'll see my comments and the little I had to modify to get this to work. FYI, it took me again 4 hours to get this thing going. There are dozens of different configurations available out there, the trick is to find one that works. Finding the problem was also hard, but now it looks so trivial when one knows what is wrong.

 Moral of the story: no pain, no gain.


 Integrating Apache 2.0.x and Tomcat 5.0.x with mod_jk on Linux
 (Using <a href="http://www.galatea.com/flashguides/apache20-tomcat5-modjk-linux">FlashGuide</a> as a base.)

 This <a href="http://www.galatea.com/flashguides/apache20-tomcat5-modjk-linux">FlashGuideTM</a> covers integrating Apache 2.0.x and Tomcat 5.0.x on Unix via mod_jk. These instructions have been tested on SuSE 9.0, Red Hat 6.2 and Fedora Core 2. There are two ways to integrate Apache and Tomcat: mod_jk and mod_jk2. Mod_jk is the older but more stable version, which supports load balancing and non-standard web application locations. Mod_jk2 is newer, has bugs, and, as of 11/15/04, is no longer being actively developed.

 These instructions are for the current version of Tomcat 5.0.28, but have worked the same for all previous versions of Tomcat.

 1. Building Apache 2.0.x on Linux

 Unless you can find a binary distribution of Apache with DSO support enabled, you will have to follow these instructions to build it yourself.

   1. Check your prerequisites:

         1. You will need GCC installed
         2. You will need /usr/ccs/bin and the gcc executables in your $PATH

   2. Download the latest Apache source from http://httpd.apache.org/download.cgi - currently, the latest is 2.0.52.
   3. Unpack the distribution into a development directory (I used /usr/local)

      The distribution directory will be something like apache_2.0.52
   4. Cd into the distribution directory (e.g. /usr/local/apache_2.0.52)
   5. Configure the makefile:

 ./configure --with-layout=Apache --prefix=/usr/local/apache2 --enable-module=most --enable-mods-shared=most


      If you have to run this configuration again, note that configure saves the latest configure command in config.status
   6. Build Apache:

 make


   7. Install Apache:

 make install


   8. Note the location of your Apache installation - we will refer to this as $APACHE2_HOME

 2. Installing Tomcat 5 on Linux

 1. Download the latest Tomcat binary from the Tomcat 5 section of http://jakarta.apache.org/site/binindex.cgi. Currently, Tomcat 5.0.28 is the latest.
   2. Install Tomcat by unzipping/untaring the download file and placing in the desired directory (I used /usr/local)

 cd /usr/local
 tar zxf ./jakarta-tomcat-5.0.28.tar.gz


   3. Note the location of your Tomcat installation - we will refer to this as $CATALINA_HOME
   4. Optionally, save time on typing by creating a symbolic link like this:

 ln -s jakarta-tomcat-5.0.28 tomcat5  //RK I used just “tomcat”


 3. Installing mod_jk on Linux

 Follow these steps to install a binary copy of mod_jk.

 1. Go to the JK 1.2 section of http://jakarta.apache.org/site/binindex.cgi and look in the folder for your OS. If you find one, download it. If you don't, skip to the next section.
   2. Rename the downloaded file to mod_jk.so
   3. Copy mod_jk.so to $APACHE_HOME/libexec


 4. Building mod_jk on Linux

 Follow these steps if you can't find a mod_jk binary or just want to build your own.

   1. Check your prerequisites:

 1. Type 'which libtool' to see if libtool in your $PATH. If it is not, and it is really not on your system, you will need the latest libtools from GNU:
                * Download the latest libtools from ftp://ftp.gnu.org/gnu/libtool/.
                * Unpack the distribution into the desired directory
                * Cd into the distribution directory and type the following:

 ./configure
 make
 make install

    Mandrake 10: use software update/install from the KDE to get this done. Will need CD’s 1 & 4

 * Now type 'which libtool' to make sure that libtool is in your ``$PATH``. If its not, make sure you find out where 'make install' put libtool and libtoolize and add that location to your ``$PATH``.
 2. Type 'which autoconf' to see if autoconf in your $PATH. If it is not, and it is really not on your system, you will need the latest autoconf from GNU:

            On my system, I had to de-install older rpms for autoconf first.
                * Download the latest autoconf from http://ftp.gnu.org/gnu/autoconf/.
                * Unpack the distribution into the desired directory
                * Cd into the distribution directory and type the following:

 ./configure
 make
 make install


 * Now type 'which autoconf' to make sure that autoconf is in your ``$PATH``. If its not, make sure you find out where 'make install' put autoconf and add that location to your $PATH.

RK: I don't believe Ant is necessary. I already had Ant 1.6.2 installed.

         3. Make sure you have Ant 1.5.1 or greater installed and $ANT_HOME set. If not, follow these steps:
                * Download the latest Ant (currently 1.6.2) from http://ant.apache.org/bindownload.cgi
                * Unpack the distribution in the desired directory (I used /usr/local)
                * Optionally, save time on typing by creating a symbolic link like this:

 ln -s apache-ant-1.6.2 ant


 * ``Set $ANT_HOME`` in your environment (or in /etc/profile so you don't forget it next time) to point to the ant installation
                * Make sure $ANT_HOME/bin is in your $PATH

 2. Download the latest JK 1.2 Tomcat Web Server Connectors from http://jakarta.apache.org/site/sourceindex.cgi. As of this writing, JK 1.2.6 is the latest.
 RK: correction: JK 1.2.8 is now the latest version and it was available for download on 2/7/2005. It wasn't on the prior day (I even sent an e-mail to the Jakarta webmaster).

 As of December, the download link for JK 1.2 doesn't seem to be working. In the meantime, therefore, you can find JK 1.2.6 on my site.
   3. Unpack the distribution in the desired directory

      Currently, the distribution directory is jakarta-tomcat-connectors-jk-1.2.6-src
   4. Cd into the distribution directory
   5. Configure the makefile:

    cd jk/native
    ./configure --with-apxs=/usr/local/apache2/bin/apxs --enable-EAPI


      Replace the values of --with-apxs with the appropriate path to the apxs executable in your Apache installation directory.
   6. Now do the build:

    make


   7. Finally, install

    cp ./apache-2.0/mod_jk.so $APACHE2_HOME/modules


 $APACHE2_HOME refers to the directory in which you installed the compiled Apache 2.x - in my case, it is /usr/local/apache2.


 5. Configuring mod_jk for Apache 2.0.x

   1. Create workers.properties in $APACHE_HOME/conf with the following contents:

        workers.tomcat_home=/usr/local/tomcat
        workers.java_home=$JAVA_HOME
        ps=/
        worker.list=default    //rk: this is the worker name to be used in Apache’s httpd.conf

        worker.default.port=8009
        worker.default.host=localhost
        worker.default.type=ajp13
        worker.default.lbfactor=1


      Naturally, you will take care to set workers.tomcat_home and workers.java_home as approriate for your environment.

 Often, people put the workers.properties file in ``$CATALINA_HOME/conf/jk``, as my previous FlashGuides have also recommended. However, putting it in $APACHE_HOME/conf is more appropriate since its purpose is to tell Apache about one or more local or remote Tomcat installations.
   2. For Apache 1.3.x, edit ``$APACHE_HOME/conf/httpd.conf`` and add the following to the LoadModules section:

    LoadModule jk_module libexec/mod_jk.so


   3. For Apache 2.0.x, edit ``$APACHE_HOME/conf/httpd.conf`` and add the following to the LoadModules section:

    LoadModule jk_module modules/mod_jk.so


 4. Edit ``$APACHE_HOME/conf/httpd.conf`` and add the following to the AddModules section (if you have it - Apache 2 and later versions of Apache 1.3.x don't):

        AddModule mod_jk.c   RK: don’t need for Apache2


   5. Edit ``$APACHE_HOME/conf/httpd.conf`` and add the following before Section 3 in httpd.conf:

 #
 # Mod_jk settings
 #


    JkWorkersFile "conf/workers.properties"
    JkLogFile "logs/mod_jk.log"

    JkLogLevel error

   JkMount /jsp-examples default   //RK: default is the worker name
   JkMount /jsp-examples/* default

 # End of mod_jk settings


 The location of the Jk workers.properties and log files are relative to $APACHE_HOME. If you prefer other locations, make sure you fully qualify the paths.

 In this test, we are directing all URIs that begin with "/jsp-examples" to Tomcat. The name "default" corresponds to the JK worker that we defined in `$APACHE_HOME/conf/workers.properties`. We define two JK Mounts in order to match the URI of "/jsp-examples" as well as any that start with "/jsp-examples". In a real world situation, we might only direct JSPs or servlets to the JK worker.
 6. Find the line in $APACHE_HOME/httpd.conf that starts with "Port" - make sure you have it pointing to a port that is not in use. Lately, Apache has been coming with the "Port" directive set to "8080", which is the same port that Tomcat will listen on. If you don't have any other web server running, you can set it to "80" instead.
   7. Test httpd.conf by typing the following:

 cd $APACHE_HOME/bin
 apachectl configtest

 new item inserted by RK:
 5a server.xml

 The connector will work only if it’s entered like this. Tomcat 5 has a default that doesn’t seem to work. Comment it out and paste the line below. Debuh=”1” should be “0” for production mode.

 <connector port="8009" minprocessors="5" maxprocessors="75" acceptcount="10" debug="1" protocol="AJP/1.3">&lt;Connector port="8009" minProcessors="5" maxProcessors="75" acceptCount="10" debug="1" protocol="AJP/1.3"/&gt;

 end of inserted item by RK

 6. Testing

 1. Make sure no other server is running on the default Tomcat ports of 8005, 8009 and 8080. Make sure no other server is running on the Apache port, which is normally 8080 or 80.
   2. Start Tomcat first:

 cd $CATALINA_HOME/bin
 ./catalina.sh start


 Always start Tomcat first and then start Apache. If you have to bounce Tomcat, remember to take down Apache first and restart it after Tomcat restarts. mod_webapp is very sensitive that way. In fact, some users have reported that it is necessary to put in 10 second delay between starting Tomcat and Apache.
   3. Start Apache:

 cd $APACHE2_HOME/bin
 ./apachectl start


   4. Point your browser to http://localhost and verify that you get the default Apache page

      Substitute "localhost" for the actual machine name/ip if necessary.
   5. Point your browser to http://localhost:8080 and verify that you get the default Tomcat page
 6. Point your browser to http://localhost/jsp-examples/ and verify that you get the index page for the Tomcat examples. This will be served by Apache and will indicate that you have completed your integration of Apache and Tomcat successfully - congratulations!
