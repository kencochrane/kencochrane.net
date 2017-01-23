+++
title = "Django Query using value from another field in same table"
date = 2009-01-21T13:19:00Z
updated = 2009-02-01T13:01:52Z
tags = ["django", "python", "models", "query"]
categories = ["blog"]
topics = ["blog"]
slug = "django-query-using-value-from-another-field-in-same-table"
url = "blog/2009/01/django-query-using-value-from-another-field-in-same-table/"
+++

I came across a query that seemed like it should be pretty easy, but it wasn't.<br /><br />Suppose you have the following Model Class.<br /><blockquote><pre># pseudo code.<br />class Person(models.Model):<br />  first_name = models.CharField()<br />  last_name = models.CharField()<br /></pre></blockquote>I would like the query to return all Person's who have the same first_name and last_name<br /><br />My first guess was to do the following.<br /><br /><blockquote>person_list = Person.objects.filter(first_name=last_name)</blockquote><br />but that doesn't work because it wants to have last_name as a variable.<br /><br />This is what I ended up doing.<br /><br /><blockquote>person_list = Person.objects.extra(where=['first_name=last_name']<br /><br /></blockquote>You could also do it using a models.Manager, if that is what you prefer.<br /><br />Even though this is an edge case, I'm not sure why Django doesn't support this out of the box, but I am pretty sure there is a pretty good reason.
