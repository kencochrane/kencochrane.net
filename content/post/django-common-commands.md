+++
title = "django common commands"
date = 2008-05-24T22:09:00Z
updated = 2008-06-21T11:10:43Z
categories = ["blog"]
topics = ["blog"]
slug = "django-common-commands"
url = "blog/2008/05/django-common-commands/"
+++

I keep forgetting all of the different django commands so I am creating this cheat sheet for myself.<br /><br /># start a new project<br /><br />>>> django-admin.py startproject $mysite<br /><br /># create a new site inside of a project<br /><br />>>> python manage.py startapp $siteName<br /><br /># start dev server<br /><br />>>> python manage.py runserver<br /><br /># sync the database with recent model changes.<br /><br />>>> python manage.py syncdb
