+++
date = "2021-03-28"
tags = ["hiring", "engineering-manager", "managing", "on-boarding", "developers"]
categories = ["blog"]
topics = ["blog"]
title = "On-boarding new developers to the team"
banner = "images/welcome-aboard.png"
+++
When you hire someone new for your development team, one of the primary goals is to ramp up that person so that they can be a fully contributing member as soon as possible.

When dealing with developers one way to decrease this time is to make it as easy as possible for someone to commit code to production on their first day. For a lot of companies, this isn’t possible and I hear horror stories of new employees taking a week or two just to get their local environments configured and up and running.

This could be caused by several different reasons but it usually boils down to installing all of the dependencies that are required to run the development stack. This has gotten even worse over the years as more and more companies are embracing micro-services over monoliths. Now instead of just setting up one environment, you need to set up many. Luckily thanks to tools like Docker, Docker Desktop, and Docker Compose you can easily get all of your services running in containers, and once you have that you can easily script the setup.

An ideal new employee set up for their first day would look something like this.

1. log in to their new laptop
2. Get access to the team source code repository (GitHub, etc)
3. Clone the project to their laptop
4. Run the bootstrap.sh script which will download and install dependencies on to their laptop
5. Read the docs and start up the stack (docker-compose up)
6. Find a minor task to work on. Run unit tests to make sure nothing broke.
7. Make a minor change, commit code, submit Pull Request to get the code reviewed.

If you can get this set up or something similar you are on the right track. Just think how much time and money you are saving by cutting down the Initial Time To commit (ITTC) for new people who join your project. It doesn’t work for just new employees, it could be important for crunch times when you need to pull in folks from other teams to help make a deadline.

If it takes a week to get someone set up before they could help, then it isn’t possible to easily move people around. That means you aren’t as flexible as you could be, and that will translate to either missed deadlines or making current team members work longer hours, which also isn’t ideal. Too many of those and you could burn out your developers.

If you have more than one project/team, you should try and have a similar process for all of them. This will make it easy for people to move around between teams and projects when needed and allows you to build common tooling to make it easier to maintain.

Another advantage to this is that it makes it easier to upgrade developer laptops and replace them if they break. If it takes a while to get the laptop just right you will find developers would rather hold on to an older laptop than upgrade and go through all the hassle of setting it up again. This means your team could be using slower, out-of-date hardware, which might take longer to build and run tests and thus slows the whole team down. If a laptop dies you want to be able to get that developer back up and running as quickly as possible. With the help of the cloud, and SaaS services you shouldn’t need much installed on local machines anymore, so the recovery times should be much quicker if you do things right.

The next time you add a new person to your team have them keep track of how long it takes to get up and running. If it isn't under a day then it might make sense to dedicate some time to improve it. You don't need to solve it all at once, just keep at it and keep improving, over time you should be able to get it down to a reasonable amount of time.

Now that we have talked about the first day of a developer, my next blog post will go a little further and help you [plan out the first 90 days]({{< ref "why-you-should-give-new-employees-a-30-60-90.md" >}}) .
