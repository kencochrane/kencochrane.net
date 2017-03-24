+++
title = "Managing Django settings across multiple environments"
date = 2009-01-17T14:43:00Z
updated = 2009-01-17T15:09:34Z
tags = ["django settings"]
categories = ["blog"]
topics = ["blog"]
slug = "managing-django-settings-across-multiple-environments"
url = "blog/2009/01/managing-django-settings-across-multiple-environments/"
+++

I don't know about you, but when I typically work on a Django application I have at least two environments (Local and Production), and a lot of times I am working on these projects with at least one other person.<br /><br />The way that django currently supports project settings is to have all settings in a settings.py file. This works fine if you are working by yourself in one environment, but once you add something else, it makes managing the settings a pain. One solution, which isn't very flexible is to make all environments have the same settings. That solution doesn't usually fly, so I prefer another approach.<br /><br />I take my settings file and only put settings that will be the same for every install in there. Then I create a  local_settings.py file in the same directory and put only settings that are environment specific in that file. Each environment is responsible for providing their own file and setting their own settings.<br /><br />One important thing to remember, NEVER put the local_settings.py file in source control or else it will just be a matter of time before someone accidentally checks in their file and breaks everyone else. I typically do a Subversion ignore on the file.<br /><br />I also create a file called local_settings.py.template that has all required settings and examples, and stick that one in source control. When you are setting up a new environment all you need to do is copy the local_settings.py.template and change the values for your ENV. Make sure you keep the local_settings.py.template file up to date.<br /><br />short example of settings.py and local_settings.py<br /><br /><blockquote><br />>> settings.py<br /><br />import local_settings<br /><br /># there is more above that I removed<br /><br />DATABASE_ENGINE = local_settings.DATABASE_ENGINE         <br />DATABASE_NAME = local_settings.DATABASE_NAME          <br />DATABASE_USER = local_settings.DATABASE_USER          <br />DATABASE_PASSWORD = local_settings.DATABASE_PASSWORD       <br />DATABASE_HOST = local_settings.DATABASE_HOST        <br />DATABASE_PORT = local_settings.DATABASE_PORT  <br />   <br /># there is more below that I removed<br /></blockquote><br /><br /><blockquote><br />>> local_settings.py<br /><br /># there is more above that I removed<br /><br />DATABASE_ENGINE = 'mysql'<br />DATABASE_NAME = 'dbname'         <br />DATABASE_USER = 'dbusername'      <br />DATABASE_PASSWORD = 'dbpassword'     <br />DATABASE_HOST = ''        <br />DATABASE_PORT = ''<br /><br /># there is more below that I removed<br /></blockquote>