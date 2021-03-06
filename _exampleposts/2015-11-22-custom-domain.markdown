---
layout: post_simple
title:  "Setting up a custom Domain name with Jekyll and Github Pages"
date:   2015-11-22 



tags:
- blog
- jekyll
---

I think it's fitting that my first blog post is about something I dealt with while setting up my blog. This blog uses Jekyll Bootstrap hosted in Github pages. It's a quick and easy way to host static content for free by using Github as your web hosting provider. There's a great tutorial for getting your blog setup and using it here http://jekyllbootstrap.com/usage/jekyll-quick-start.html. 

Once you're finished with that guide, you'll have your own site with a custom subdomain of Github (ex.  http://bpittner.github.io). However, chances are you'll want your own custom domain name to further personalize your site (ex. http://benpittner.com).  Again, there is a tutorial available at https://help.github.com/articles/setting-up-a-custom-domain-with-github-pages/, but it took me some time to wade through the several pages on Github's help section and troubleshoot some issues using Stack Overflow.

Here are the exact steps I took to get my custom domain name configured properly so that I could access my blog by going to both http://benpittner.com and http://www.benpittner.com. Note: I'm using namecheap.com as my DNS provider, but I helped a friend get setup who uses godaddy.com with these steps.

1. Create a CNAME file in your Git repository

The first step is to create a file called "CNAME" (all caps, no file extension) at the top level of your Git repository. The content of the file is simply your root / base / apex domain such as "benpittner.com". In fact, my file just looks like

"benpittner.com" (callout)

Once you're done, commit and push it to Github. 

2. Create DNS records with your DNS provider

Halfway there! Next, log into your account on your DNS provider. Typically, you'll find the settings you need to change under some "advanced" section. With Namecheap specifically, I clicked on manage benpittner.com and went to the "Advanced DNS" tab. My friend found a similar section in GoDaddy's account management portal.

You're going to be updating your host records, and many DNS hosting providers come with some built in records already. I deleted all the preconfigured records and then added these three:

Your management portal may look a little different, but the process is standard. You're creating the following records:
	- Two "A" records that point the apex domain at two IP Addresses provided by Github in their documentation
	- One "CNAME" record that points the "www" subdomain to your Github pages URL on Github. The format of this one is "[yourname].github.io.". Note the necessary trailing '.' character.

3. Wait!

DNS changes are never instantaneous. They need time to propagate around the world! Github's documentation gives an example of using the "dig" command on Mac or Linux to check if your two "A" records were set up correctly, but that didn't work for me as an indication that all the changes were ready. I found using the "ping" command more useful, and it works on Mac, Linux, and Windows. Simply ping your apex domain, and if the reply comes back from either of the two Github IP addresses, you should be good to go.