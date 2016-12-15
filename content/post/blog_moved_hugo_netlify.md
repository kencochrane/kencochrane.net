+++
date = "2016-11-20"
tags = ["hugo","netlify", "hosting", "blog"]
categories = ["blog"]
topics = ["blog"]
title = "I rebuilt my blog with hugo, and moved to netlify"
banner = "banners/hugo.png"
+++

About a year ago, I was frustrated with my lack of new posts on my blog and decided I needed to fix that. After a little bit of soul searching, I decided to reboot the blog, and start fresh with a new platform. Here are a few of the reasons why I decided to change my blog.

1. I didn't like the look of my blog, and there were no good themes available on my old platform. I'm not a designer, so the best I'm going to get is a theme that is built into the blog platform.

2. My current workflow for writing a post, wasn't very straightforward, and I always forgot the steps needed to publish a new post, and caused me to relearn how to do it every time. This little bit of friction caused my to not to write as much.

3. I was hosting on github, which is great, but prevented me from serving over HTTPS.

## Blog platform

The first thing I decided to do, was look at the blogging tool I was using. I did a lot of research on all of the available static site generators that are available, and I settled on [hugo](https://gohugo.io). Here are some of the reason why I picked Hugo, over the other available options.

1. Hugo is written by [Steve Francia] (https://github.com/spf13) who used to work at [Docker] (https://www.docker.com).

2. It is a simple [Golang](https://golang.org/) application, and I have been writing more and more Golang recently, so I figured it would give me another way of learning Go.

3. It is really fast, and simple to use. It has a single binary, with a few commands, that are easy to pick up. It also include a dev server, so that you can see what your blog looks like before you publish.

4. It generates a fully static site, so I can easily deploy to any web host that I want.

5. It has a nice collection of [themes](http://themes.gohugo.io/).

## Blog hosting

Now that I had my blogging tool, I needed a place to host my blog. I wanted something that was cheap, FREE if possible, that allowed, me to also host over HTTPS. I originally looked at hosting on AWS S3, with Cloudfront, but there didn't look like a good way to expire the cache when I made a new post.

I then looked at a bunch of other platforms, and I decided on using [netlify.com](https://www.netlify,com) to host my blog. Here are some of the reasons why I picked netlify.com:

1. They offered exactly what I was looking for, feature wise.
    - CDN
    - Continuous Deployment from github commits
    - HTTPS support via Let's Encrypt.

2. They offered a FREE plan, with a nice set of paid plans that were reasonably priced.

3. Their CTO is [David Calavera] (https://github.com/calavera) who I worked with, when he worked at Docker, and is a super nice guy, who is also super smart, so if he is the CTO, I have full confidence in their platform.

4. They are using Docker to power their platform, and since I love Docker, I do anything I can to support companies in the Docker ecosystem.

5. [They love open source](https://www.netlify.com/open-source/), and they have a bunch of cool open source projects.

## Conversion
Once I had my new blog platform and hosting figured out, I needed to convert from my old system to my new system. This what took the longest time. My old blog was using the ReStructuredText format, and Hugo supported Markdown. I needed to convert all of the blog posts from one format to the other. Pandoc to the rescue. After a little bit of playing around, I was able to bulk convert about 90% of the documents using Pandoc. The last 10% I had to do by hand, which wasn't too bad.

Once the formats were converted, I still needed to move over the metadata since they were different, and I also needed to make sure my urls didn't break when I moved them over. This is what took my a while, and thus the large delay. When I got up enough energy I would work on it, usually when I was bored, and stuck on a cross country flight, with nothing else to do.

Now with everything converted, I needed to pick a theme. I decided on [hugo-icarus](https://github.com/digitalcraftsman/hugo-icarus-theme). I had to make a few changes, and got everything up and running, and it looked nice locally. Next step, moving it online.

I signed up at [netlify](https://www.netlify,com) and created a new site that linked to my new [git repo on github](https://github.com/kencochrane/kencochrane.net) that had my converted blog files in it. This automatically started a build, which was cool, but it failed. It failed because of the way I had linked my hugo themes, they were setup as submodules, and I guess I did it wrong. I followed their [using Hugo on netlify](https://www-redirects.netlify.com/blog/2015/10/06/a-step-by-step-guide-hugo-on-netlify) blog post, and this helped me fix the issue. Once that was fixed, I was able to get it to build and deploy quickly, and BAM, my new blog post was live on their temp URL. I was able to play around with it, and make sure everything worked before I made any DNS changes.

Next step was to change my DNS settings to point to their servers, and enable HTTPS support.

All and all, it was super easy, and I'm really impressed, Netlify has done a great job, and I highly recommend them, if you are looking for a static website host.

## New workflow
Now that I have everything setup, here is my new workflow for writing a blog post.

1. Create a new markdown file, add the appropriate meta tags, and write the post.

2. Run ```$ hugo server ``` and preview the post to make sure it looks good.

3. git add/commit the new blog post, and push to github.

4. Netlify, takes over from there. They get a webhook from github saying I made a change. They pull down my changes, build the site, push it up to the server, and serve it. They handle any CDN expiration, etc.

As you can see, I have reduced all of the friction that was stopping me from writing, in the past. Now I just need to write the post, and commit it, the rest is automated. This will hopefully get me to write more blog posts.
