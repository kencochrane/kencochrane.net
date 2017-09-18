+++
title = "CashStar the early years"
date = "2017-09-17"
tags = ["cashstar", "work", "history"]
categories = ["blog"]
topics = ["blog"]
slug = "cashstar-the-early-years"
url = "2017/09/17/cashstar-the-early-years"
banner = "banners/cashstar.png"
+++

On August 30th, 2017 [BlackHawk Network](https://blackhawknetwork.com/) [acquired](https://www.cashstar.com/press-release/blackhawk-network-acquires-cashstar/) [CashStar](https://www.cashstar.com) for $175 Million dollars in cash. This is good news for everyone involved, the CashStar employees, share holders, and for black hawk. This is a great ending, but how did they get where they are today, and how did it all start? I was lucky enough to be one of the first employees at CashStar, so I have some first hand knowledge of the early years. I have been meaning to write about this for a while, and with the recent sale, it looks like this is a good time to sit down and write it down, before I forget.

### How it all started
Most people don't know, that the CashStar that they know today, wasn't the first CashStar. The first CashStar was originally based in California and was incubated at Coupons inc, and there was a separate CashStar Australia that was doing coupons in Australia and the U.K. The company had investors and funding, but it didn't make it. It wasn't doing well, so they decided to close up shop and return the rest of the money to the investors. I don't know too much about this first company, if you know anything, please let me know and I'll update the post.

CashStar the company still had [$4 million dollars](http://nevcfunding.blogspot.com/2009/03/cashstar-lands-4m-launches-gift-card.html), but it had no employees or a product. The investors decided to reboot the company with a new founder and product. [Steven Boal](https://www.linkedin.com/in/sboal/) talked with [David Stone](https://www.linkedin.com/in/david-stone-a8a19/) about his idea, and the two decided to move forward with a company based on a new product called Virtual Gift Cards (VGCs). David would be CEO and run the company in Maine.

David started in December 2007, and by February 2008 he had hired [David Wood](https://www.linkedin.com/in/david-wood-a098998/) as the first employee, and [Glenn Snyder](https://www.linkedin.com/in/glennasnyder/) as the VP of Engineering. Glenn was a friend of David's and agreed to come on board as a contractor to help him hire and build the engineering team. Glenn's first hire was [Phelps Peeler](https://www.linkedin.com/in/phelpspeeler/) as the director of backend engineering. Phelps started in June, and worked on the gift card authorizer that CashStar would use to authorize it's gift cards.

I had previously worked with Phelps at Wright Express, and a few weeks after he started at CashStar we had lunch at [Ricettas](https://ricettas.com/), a pizza place around the corner from his new office. He was telling me about all of the cool things they were doing, and mentioned that they needed someone to build out the web side of the business, so they can take orders online. At this point in my career, I had many years of experience building out web applications including ones with an e-commerce component. It interested me enough that he convinced me to interview for a job. A couple of weeks later I was meeting with Glenn and David and they offered me a job. I accepted the job offer and I gave my notice, and started in August 2008.

One of my requirements for accepting the job at CashStar was that I wanted an Apple MacBook Pro. They agreed to this, and on my first day when Phelps and David saw that I had a MacBook, they were a little jealous. It was only a matter of time, before they also switched over to using a MacBook. When we started hiring people, the fact that we allowed them to pick which laptop they wanted, was seen as a big plus for us. I'm not sure if it single handedly convinced people to accept a job, but I'm sure it was one of the factors.

In August 2008 we also hired [Phil Moran](https://www.linkedin.com/in/phil-moran-0653248/) our first sales person and [Laura Cannon](https://www.linkedin.com/in/lauracannonmaine/) our first client services / marketing person. Now that we had enough people, we could get started building CashStar.

### The Castle

{{< figure src="/cashstar/thecastle.jpg" title="The Castle our first office" >}}

Our first real office was in an office building they called ["The Castle"](http://www.portlandlandmarks.org/brick-hill/). The building used to be a youth detention center and then a while ago they remodeled it and turned it into offices. We were subletting our office from another company and it was pretty small. The office was less than 800 square feet, with access to a shared kitchen with a fridge, microwave, coffee machine and a table.

When you walked in, there was a small conference room table on the left. Dave Wood had his desk, and then there were a few tables built into the walls, enough for 4-6 people to work. There were two offices, the one on the left was David Stone's, and Phelps and I shared the one on the right. This was a good office size for about a year, and then we started to outgrow it, and we expanded into other office spaces on the same floor.

One of the great things about the Castle, was this big open field right next to it. On nice days, when we needed a break, we would go out and play kickball or bocce. It was a good way to relieve some stress.

In the kitchen we had our own fridge, and like all startups, we stocked it will some adult beverages.

We eventually outgrew this space, and moved to a much larger office on middle street in the old port section of Portland.


### Building CashStar

When I started working at CashStar, I was told I had six weeks to get my web application up and running in order for us to sign a customer and get them live in time for the all important Christmas shopping season. If we missed this deadline, we would lose our chance at making money that year.

The first thing I did, on my first day, was take an inventory for what what they currently had. There was Phelps's backend code for handling gift card authorizations written in Java. A badly broken .Net demo application, written by a consulting company. The website was a single static page hosted on a shared hosting service. We had no servers, or infrastructure of any kind. This was truly greenfield development, and it's what I liked most about the project. There was no legacy code or production customers to hold us back, we could do what ever we needed in order to make our deadline.

The way I saw it, I had two options:

1. Keep the .Net application and the consulting company who wrote it, and help them fix all the bugs and get it production ready.
2. Throw everything away and start from scratch.

After talking it over with Phelps and Glenn, I decided to pick option two, for the following reasons.

- I didn't know .Net, and if we decided on going this route, I knew that we would stick with .Net going forward. That point forward we would be a windows shop.
- If I started from scratch, I would be able to build the site from the ground up, using the technologies that I wanted, and was the most comfortable with, and I would know everything about it. This was important incase I need to fix a bug in the middle of the night. There would be no learning curve, or dependencies on an external vendor.
- The demo application was only partially completed and it had lots of bugs, and it would have needed a lot of work to get it production ready. By throwing it out, we wouldn't be losing much.
- By removing the external consulting company, we saved money on development costs, and as a startup every dollar counts.

Once I decided to start over, we fired the consulting company, and I spent the next day, picking out the tech stack.

#### Tech stack
Over the years people have asked me a few times why I picked the tech stack that I did. It has been a while, but here are the reasons to the best of my memory.

At that point, I had 10 years experience building Java web application, and it was long enough time to know that Java and it's many web frameworks were not great at building web apps, so I decided to use something else. After looking at all of the different options available (java, php, perl, python, ruby, etc) I narrowed it down to Python with the Django framework and Ruby with Ruby on Rails. I then went through the pros and cons of each language and framework, and I finally decided on using Python with Django, for the following reasons:

- I had used Python/Django for about a year at that point and had built a few web apps using it, and it was really nice. I was able to build things really quickly, and the code was really clean and easy to read.
- At that point in time Ruby on Rails 2.0 was released, but there wasn't many books or gems that supported it. I didn't want to write something in Rails 1.x when I knew I would need to upgrade to 2.x in the near future.
- Django had a really nice templating engine that allowed me to handle the customer branding the way I wanted, Ruby didn't offer a comparable templating system at the time.
- Django's documentation was really good, and it was fairly easy for developers to pick it up.

One gotcha for Django was that it wasn't 1.0 yet, but they were on track to be 1.0 before we were going to be live in production. So I developed on Django's master branch, and updated every day, and fixed any issues that came up. Eventually they released 1.0, right before we went live, and it was pretty stable after that. Developing off of the ever changing Django master branch was a challenge. Whenever you would update to use the latest changes, you had to spend time trying to figure out what was going to break. Targeting 1.0 was the right move due to some of the features that were included in that version.

We made a few decisions on the tech stack, mostly because we knew the tools, and we didn't have time to learn something new, and time was the utmost importance. We decided to use MySql over PostgreSQL, and subversion instead of git for these reasons. We picked Mysql because I had more experience administering MySql compared to PostgreSQL. Git was really new, and we didn't want to risk losing time or code with a new versioning system.

Now that I had the tech stack decided, I needed to build the application. I'm not really good when it comes to front end (css, UI/UX, etc), so to help me with that, we hired [Brian McLaughlin](https://www.linkedin.com/in/brianmclaughlindesign/) as a part time contractor. I had worked with Brian previously so we were able to hit the ground running. He designed the pages, and I wrote all the code to make them work. After a few weeks we had a fully functional demo, that we were able to show to people and do some usability testing.

Now that we had a functioning demo, I needed to work on getting everything working for real. I needed to buy some servers to host our application. If I was doing this today, I would normally spin up servers in Amazon Web Services (AWS), but back then, AWS wasn't what it is today, and there was questions on whether it was PCI compliant or not. So instead I found a data center company that leased us five physical linux servers, a firewall and a hardware load balancer. There were two production servers connected to the load balancer, one dev server, one database server, and one backend server.

One mistake that I made when I bought the servers was that I didn't realize that they had CPanel installed. [CPanel](https://en.wikipedia.org/wiki/CPanel) is a web hosting control panel. It is suppose to make running a linux server easier if you don't know what you are doing. If you know what you are doing, it is a real pain in the butt because it expects things a certain way, and if you change those things, it breaks. I spent a couple of sleepless nights trying to configure those servers the way we needed. This could have been prevented if I had paid more attention to the server order form. Lesson learned I guess.

After a long six weeks, we were able to finish the first version of our gift card platform. It was very much an MVP (minimal viable product) but it worked. The front end would serve up a website where the customer would fill out an order form and enter their credit card information. It would connect to Authorize.net for credit card authorization and if it succeeded, it would connect to the backend system to get a gift card number. The front end would then build out the digital gift card, and send emails to the buyer (receipt) and the recipient (gift card). Now that we had a product to sell, we just needed clients.

### The chicken and the egg
Once we had the product finished, we needed to sign customers. We had a hard time finding our first customer, it wasn't because they didn't like the solution, they just didn't want to be first. They wanted to see someone else using it, to prove it wasn't vaporware. We had a chicken and the egg problem, we had people who wanted to sign, but they didn't want to be first.

To get around this, we faked it until we made it. [Jeff Dixon](https://www.linkedin.com/in/jtdixon/) was consulting with us, and he was friends with the owner of Gorham Bike and Ski (GBS). Gorham bike and ski was a local bike and ski dealer in the Portland area with three stores. We struck a deal with the owner to use our gift cards at no cost, in exchange for being our first customer, and we also agreed to give him free marketing to promote his stores.

After we signed the deal, we had to actually get them setup. Since this was our first customer, we had no process for on boarding them to our platform. We had to figure everything out on the fly. We made a custom branded website that matched what their look and feel. This was important, because we were hosting those pages, and we didn't want people to know they were leaving the other website to make a sale, this helped people trust us enough to make the purchase. The only way they would know they left the website, was to look at the URL in the browser.

We also needed to integrate with the POS's at the three GBS stores. This allowed the people who were making purchases with their gift cards to scan the gift card. This was a real pain in the butt, and it required a lot of little hacks to get it up and running. After a stressful couple of weeks, we finally got it up and running and were able to do a real life test. We drove over to one of their stores and we bought some stuff with our new gift card. When it worked, we celebrated.

{{< figure src="/cashstar/gbs_buy_3.jpg" title="David first GBS purchase" >}}

With GBS now online we were able to let some of our potential customers know, so they could try it out. Now that it was no longer vaporware, we quickly signed [Unos Pizzeria](https://www.unos.com/) and Travelocity. Now the challenge was getting them live before the holiday rush.

### Bumps in the road
One of the problems you have when you are trying to be the first person to do something, is that there is very little help when something goes wrong. It is always great to be first, but most people don't understand how painful the path to getting there was.

We were building the first online gift card platform. People could go to our website, enter in their credit card, and send the gift card to someone over email. This sounds easy, but there is a lot to it.

First we needed to find a credit card processor that would allow us to be their customer. Because of the high risk of fraud a lot of the companies didn't want to touch us. You have to remember this was before Stripe, and the other credit card processors of today. We finally found a company that took a chance on it, and it wasn't until after David called in a lot of favors.

Once we had the processor in place, we needed to connect to Authorize.net and also make sure our site was secure and PCI complaint. This was before tokenization services existed, so the credit card numbers actually hit our servers and that meant we had a higher level of PCI we needed to deal with. That meant audits, server scanning, and a lot of paperwork.

Even after we got the credit card number, we don't even know if that credit card number was stolen or not. Since everything is online, and we are sending gift cards via email, it make it easy for someone to get a list of stolen credit cards, place a bunch of orders and then check their email for the gift cards. They could then sell those gift cards, or use them to buy something. This put us at risk for both credit card fraud and money laundering.

If we accept the credit card transition, then we need to deliver the gift card via email. This sounds easy as well, but you wouldn't believe how much trouble we had sending emails that didn't get marked as SPAM. It took us a while, but we finally built up an reputation, and fixed the issues that were getting us marked as SPAM.

If everything worked perfectly and the recipient got their gift card in their email, they still needed to use it. They needed to print it out, and so we needed to make sure that our gift cards printed out correctly on all types of computers, and printers. Once again, it seems easy, but it is surprising, how much work went into making sure the gift cards looked good when they were printed out. If it didn't fit on one page, it would waste paper. If they were blurry, then the barcode might not scan. If they printed in black and white vs color, does it still look good, and is it readable/usable?

If they were able to print it out and bring it to the store, does the clerk at the store know what to do? Are the barcode scanners at the POS able to scan the barcode? At the beginning, a lot of times it was a no, to both of these questions. With anything new, you had to train people how to use it, and that is a lot of work. If you do it wrong you will frustrate customers and they will not use it again. As you can see, there is a lot more to this problem then most people realize, and if it wasn't for the great team that we had, we wouldn't have made it.

### The Pivot
When we first started building CashStar the goal was to build an open loop gift card network. Open loop is basically what you have with a prepaid Visa debit card. Anywhere that Visa is accepted, you can use the debit card. We wanted to do the same thing for CashStar gift cards. They could be branded for a particular company, or you could use our generic CashStar gift card, and use it at any of the companies that uses our gift card system.

This was a nice goal to have, but it was a lot harder to get up and running then we imagined. In order for you to have your gift cards accepted at a business, you need to integrate your gift card authorizer into that business's Point of Sale (POS) systems. This means touching every POS for a business, and in some cases businesses had different POS versions. The number of POS systems on the market is fairly large, and it would require custom code and certifications for each one. We bought a POS and had it in our office to make it easier for us to test our system. Companies don't make changes to these systems that often, because they are a pain to deal with. When we tried to sell to these companies, this was a non-starter for a lot of them.

On top of the POS problem, most companies already had gift card systems in place, and they didn't want to have two separate gift card systems. One for physical cards, and one for digital cards.

Given those two barriers to entry, we decided to change our goal, and instead of providing an open loop gift card, we would instead integrate with their existing gift card system. This approach had it's pros and cons. The pros were that we didn't have to integrate with their POS systems, and there would only be one gift card system for both digital and physical gift cards. The con, was that we now needed to integrate with a 3rd party to use their gift card system.

Integrating with a gift card system for our client introduced another set of issues we needed to overcome. In many cases we were the first company to request this access from these gift card processors. Many of the companies didn't have any policies in place for giving a 3rd party access to their clients account. We had to do a lot of work to get access to these systems, and to gain their trust. This included certifications and audits, to make sure we knew what we were doing. This makes sense, if we had a bug in our code we could cost our clients lots of money. The good news was that once we had one customer on a processor, it was a lot easier to get another, since we already had a relationship.

In order to handle these processor integrations we converted the backend code from a gift card authorizer into a broker of sort. The backend exposed a generic API to the front end, so that it could request gift card information for a given brand from the backend in a generic way. The frontend didn't care which processor the brand had, and the backend dealt with keeping track of which processor each brand used, and knew how to interact with that processor's API to generate new cards, get balances, etc.

This separation between the front end and the backend was great, and it let us develop each one independently without slowing each other down. Over time we eventually integrated with all the major gift card processors, and this made on-boarding a new customer much quicker and easier.

### First Customers

When we signed Uno's and Travelocity they didn't want an open loop gift card, they wanted us to integrate with their existing gift card platform. They were the primary cause of the pivot (described above), and to be honest if it wasn't for them, we wouldn't have signed other larger clients. Once we signed them, it wasn't long before we signed [O'Charley's](https://en.wikipedia.org/wiki/O%27Charley%27s) which lead us to [CVS](https://www.cvs.com), which then lead us to [Home Depot](https://www.homedepot.com). Landing Home Depot was huge, and it took a lot of work from a lot of people, to close that deal. But once it did close, we knew we were on to something.

### Fraud
As mentioned above, one of the things we needed to worry about was Fraud. Potential fraud scared a lot of businesses from selling gift cards online. To overcome this issue, we decided to cover all fraud. That means if someone used a stolen credit card to buy a gift card, we would eat the cost of that gift card, and the business wouldn't have any liability. This resinated well with customers, and was a differentiator between us and our competitors.

This was all well and good, but unless we had a way to prevent fraud, we would lose a lot of money, and wouldn't be in business very long. When you add fraud prevention tools, you have to be careful. If they are too strict you will be rejecting good orders, and if not strict enough you will be allowing bad orders through.

To help us build our fraud prevention tools we hired [Pete Kledaras](https://www.linkedin.com/in/petekledaras/). Pete and I worked together to build the first set of fraud tools. When we compared it to the other tools available from our credit card processor and others on the market, it easily outperformed them. We switched over to using our custom tool, and our fraud rates dropped.

The problem with a lot of other tools on the market, is that they were generic, and were too aggressive. They rejected too many good orders. Our tools were built specific to our use case, and because of this we could tune them the way we needed. If we noticed a new way scammers were trying to get around our controls, we were able to easily adjust our tools to stop it quickly.

As we got more customers and sold more cards, our tools had more data to analyze and got smarter and thus better. Because we pooled our datasets between our customers it allowed us to make all of our customers safer at the same time. If a scammer attacked customer A, and we put in a fix to stop it, it would automatically prevent that scammer from using the same trick on all of our other customers.

#### Crime stoppers
After we launched Uno's, we didn't have much fraud prevention in place yet. Most of the checks were the ones we got from the payment processor, and manual checks on our part. We didn't have a lot of orders, so it was pretty easy for us to check them when they came in.

Our first case of fraud was with our Uno's account. Someone had bought a gift card with a stolen credit card and then used it almost right away at an Uno's in Florida. A week later, we were sitting at the office on a Friday night when we noticed that someone with the same email address bought another gift card, but this time they used a different credit card. We assumed the credit card was stolen, so [Laura Cannon](https://www.linkedin.com/in/lauracannonmaine/) sprung into action. She reached out to the owner of the credit card to confirm if they placed the order. Just as we thought, it was stolen.

Thinking that the person who bought the card would stick with the same MO [Modus Operandi (MO)](https://en.wikipedia.org/wiki/Modus_operandi) using it at the same restaurant in the very near future, we knew we had to act quick. Laura called the Uno's that the person used last time, and explained what was happening to the manager. The manager then called the police to have them waiting incase the person came in to use the gift card. We all waited at the office hoping that the person would show up. Laura's phone rings, and it is the manager of the restaurant and she tells us that the person was there, and trying to use the gift card. We confirmed that the gift card he was using was the one that was stolen and she passed the information over to the police officer who was there, and he arrested the suspect. When that happened we all cheered so loud, it was awesome, it felt like we were actual detectives for a night. Before we left we cancelled the gift card, and refunded the money back onto the credit card. We also told the person who owned the credit card, to call their credit card company and report it stolen.

### Tragedy
CashStar's first CFO was [Mitchell Schecter](https://www.linkedin.com/in/mitchell-schecter-10768/). Since we were pretty small, and didn't have a lot of work for a CFO, he was a part time person, who worked out of his house in Massachusetts, where he was a CFO for a few other companies. Mitchell handled a lot of things for CashStar, everything from hiring, HR, payroll, and paying all of the bills. If it wasn't for him, we wouldn't get paid, and the lights would shut off.

Unfortunately he died when he was on bike trip vacationing in Europe. This hit us hard, we were still a small company so to lose someone you worked with closely, really sucked. Because he was the holder of all of the keys for the company, we were both emotionally and financially stuck at the same time. It took us a while to get over his loss, and also get our financial affairs in order. If it wasn't for Mitchell I don't know if CashStar would have done as well as it did, and it is important to remember everything that he did for the company.

### Early Employee Hire TimeLine:
- December 2007 David Stone starts CashStar with Steven Boal
- Feb 2008 David Wood is hired.
- June 2008 Phelps is hired
- May 2008: [Jeff Dixon](https://www.linkedin.com/in/jtdixon/) consultant brought in
- August 2008 Ken Cochrane, Phil Moran, and Laura Cannon are hired. [Brian McLaughlin](https://www.linkedin.com/in/brianmclaughlindesign/) hired part time as contractor.
- September 2008 [Sheri Hall](https://www.linkedin.com/in/sheri-hall-miller-10b83/) and [Miklos Van Halen](https://www.linkedin.com/in/miklos-van-halen-07931/) hired as contractors.
- Fall 2008: Pete Kledaras hired
- January 2009: [Brian McLaughlin](https://www.linkedin.com/in/brianmclaughlindesign/) hired full time.
- Feb 2009 [David Krauth](https://www.linkedin.com/in/dakrauth/) hired
- April 2009 [Brandon Tarbet](https://www.linkedin.com/in/brandon-tarbet-a33a9314/) hired
- Jun 2009 [Richard Franks](https://www.linkedin.com/in/richardfranksjr/) hired
- Sep 2009 [John Costa](https://www.linkedin.com/in/johnmcosta/) and [Brian Rigney](https://www.linkedin.com/in/brianrigney/) hired

### Caveat
A majority of the things that I wrote about here, happened over 8 years ago, so I wouldn't be surprised if I'm misremembering something, or missed someone important. If I did that, I'm sorry, please let me know and I'll do my best to correct it.

### Conclusion
As you can see there was a lot of things that we needed to do, and problems to overcome in order to successfully sign our first customers and get through our first couple of years. We wouldn't have been able to do it if it wasn't for the great team that we put together. It just proves that if you have the right people, you can do anything.

### Bonus: More photos
Here are some more photos that I found.

{{< figure src="/cashstar/alex.jpg" title="Alex about to erase a whiteboard with Janet in background" >}}
{{< figure src="/cashstar/bowling.jpg" title="Bowling outing" >}}
{{< figure src="/cashstar/dave_wood.jpg" title="Dave Wood" >}}
{{< figure src="/cashstar/david_press.jpg" title="David Stone press photo" >}}
{{< figure src="/cashstar/fire.jpg" title="Fire down the street" >}}
{{< figure src="/cashstar/middlest_1.jpg" title="Visit to new offices on Middle St" >}}
{{< figure src="/cashstar/mike.jpg" title="Mike's birthday" >}}
{{< figure src="/cashstar/mothersdaycontest.jpg" title="Mother's day contest" >}}
{{< figure src="/cashstar/outing.jpg" title="Team outing mini golf" >}}
{{< figure src="/cashstar/phelps_ball_office.jpg" title="Phelps on Ball" >}}
{{< figure src="/cashstar/phelps_ballons_1.jpg" title="Filling Phelps office with Balloons" >}}
{{< figure src="/cashstar/phelps_ballons_2.jpg" title="Filling Phelps office with Balloons 2" >}}
{{< figure src="/cashstar/phelps_ballons_3.jpg" title="Filling Phelps office with Balloons 3" >}}
{{< figure src="/cashstar/phelps_ballons_4.jpg" title="Phelps in his office after popping all balloons" >}}
{{< figure src="/cashstar/phelps_meeting.jpg" title="Phelps using ladder as a chair in a meeting" >}}
{{< figure src="/cashstar/rainbow.jpg" title="Rainbow as seen from the office" >}}
{{< figure src="/cashstar/thanksgiving.jpg" title="Thanksgiving lunch" >}}
{{< figure src="/cashstar/gbs_buy_1.jpg" title="David making first purchase" >}}
{{< figure src="/cashstar/gbs_buy_2.jpg" title="Glenn taking picture of David making first purchase" >}}
{{< figure src="/cashstar/GBS_celebrate_1.jpg" title="David opening champagne" >}}
{{< figure src="/cashstar/GBS_celebrate_2.jpg" title="David and Phelps drinking champagne out of plastic cups" >}}
{{< figure src="/cashstar/startup-fridge.jpg" title="Our fridge fully stocked" >}}
{{< figure src="/cashstar/kickball.jpg" title="Playing Kickball at the castle" >}}
{{< figure src="/cashstar/bocce.jpg" title="Sheri breaking her bocce ball" >}}
{{< figure src="/cashstar/my-desk.jpg" title="My desk" >}}
{{< figure src="/cashstar/whiteboard.jpg" title="Whiteboard during a brainstorm session." >}}
