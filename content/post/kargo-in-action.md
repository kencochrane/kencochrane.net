+++
title = "I'm writing another book: Kargo in Action"
date = "2026-09-23"
tags = ["kargo", "argo-cd", "gitops", "kubernetes", "book"]
categories = ["blog"]
topics = ["blog"]
slug = "kargo-in-action"
banner = "images/kargo_in_action.jpg"
description = "I'm writing Kargo in Action for Manning with Jesse Suen and Kent Rancourt. It's available now in early access."
+++

Most teams I talk to can deploy to Kubernetes. That part is mostly solved. What they can't easily answer is a simpler set of questions: *What version is running in staging? How did it get there? Is it safe to promote to production?*

I call that the promotion gap, and it's the reason I'm writing my second book, **[Kargo in Action](https://hubs.la/Q04t76Rc0)**, for [Manning](https://www.manning.com). It's available today through the Manning Early Access Program (MEAP).

## Why this book

I'm the Head of Engineering at [Akuity](https://akuity.io), where we build a GitOps platform on [Argo CD](https://github.com/argoproj/argo-cd) and [Kargo](https://github.com/akuity/kargo). Argo CD is great at making a cluster match what's in Git. But it doesn't decide *what* should be in Git for each environment, or when a change should move from dev to staging to production. Teams fill that gap with CI scripts, Slack approvals, and a lot of tribal knowledge. Kargo was built to replace that duct tape with a real promotion engine.

TODO(ken): A sentence or two on the moment you decided this needed to be a book. For example, a customer conversation, or seeing the same homegrown pipeline over and over.

## Writing with the creators of Kargo

I'm not writing this one alone. My co-authors are **Jesse Suen** and **Kent Rancourt**, the creators of Kargo. Jesse also co-created Argo CD. Writing with the people who designed the tool means the book can explain not only *how* Kargo works, but *why* it works that way.

## A different kind of book project

The last time I wrote a book, it was a sprint. I took over the [Docker Cookbook, Second Edition]({{< ref "i-wrote-a-book-docker-cookbook-second-edition.md" >}}) partway through and had about two weeks to rewrite most of it before a family vacation. It was fun, but it was a whirlwind.

Kargo in Action is the opposite: a book planned from scratch, with co-authors, written chapter by chapter over months. TODO(ken): A line or two on what that's been like so far (the Manning process, the pace, what surprised you).

## What's in the book

The book follows one company as it grows from a single-service pipeline into a platform that serves dozens of teams and services. Along the way you'll learn how to:

- Run Kargo alongside Argo CD and do your first end-to-end promotion
- Design promotion pipelines across environments, release candidates, and hotfix paths
- Automate low-risk promotions, and gate production behind approvals and verification
- Roll back cleanly when a release goes sideways
- Produce the audit trail your compliance reviewers actually want

If you run Argo CD today and your path to production still depends on scripts and manual steps, this book is for you.

## Read it now, in early access

With MEAP you get chapters as we write them, plus the finished book when it ships. It's also the best way to shape the final book: early readers send feedback that goes straight into the next revision.

**[Get Kargo in Action from Manning](https://hubs.la/Q04t76Rc0)**

## Watch the webinar

If you'd like a preview first, watch the Kargo in Action webinar:

{{< youtube t7pAqD2M0aY >}}

If you pick up a copy, let me know what you think. I'd love to hear what's working, what's confusing, and what you want to see covered.
