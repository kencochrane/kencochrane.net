+++
date = "2016-12-15"
tags = ["aws", "docker"]
categories = ["blog"]
topics = ["blog"]
title = "My work on Docker for AWS"
+++

For the past 9 months I have been working on Docker for AWS, which is an easy way to go from nothing, to a Docker Swarm on AWS, in about 10 minutes. The goals of this project were to make it easy for someone with an AWS account to start using Docker Swarm without needing to know anything about Docker and very limited knowledge of AWS.

When I first started, I had no idea what I was going to build, or how I was going to do it. I spent the first 6 weeks, prototyping different ideas, and gave weekly demos to a group of people at Docker. I would take the feedback from those demos and put those towards next weeks prototype. Some of the ideas stuck, some left behind, others were put on list to be added sometime in the future.

Eventually we settled on something that worked well for what we were trying to do, and have a happy balance of form and function. We use CloudFormation to launch the stack, and Docker under the covers to manage the Swarm once it is up and running. It may seem simple, but it is far from it, there is a lot of stuff going on behind the scenes to make it work, and I hope to talk more about that in future posts.

We originally launched our [private beta](https://blog.docker.com/2016/06/azure-aws-beta/) at DockerCon US 2016, and since then, we have updated 13 times. We are now in [Public Beta](https://blog.docker.com/2016/11/docker-aws-public-beta/), and anyone with an AWS account can try it out. We will hopefully graduate out of Beta, and into General Availability (GA) in the near future. Even though Docker for AWS is constantly evolving, the goals remain the same.

If you spin up a 5 node t2.micro cluster and run for under an hour it will cost you less than $0.10. So what are you waiting for, [give it a spin](https://beta.docker.com), and let me know what you think.
