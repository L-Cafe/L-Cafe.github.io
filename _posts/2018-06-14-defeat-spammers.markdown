---
title:      "The DEFINITIVE guide to defeating spammers"
date:       2018-06-14 08:35:20 +0100
tags:       productivity tip privacy
comment:    header sizes are 2000x750
header:
  image:	/assets/images/2018-06-14-defeat-spammers/header.jpg
  teaser:	/assets/images/2018-06-14-defeat-spammers/header.jpg
  caption:	"Photo credit: [**Unsplash**](https://unsplash.com)"
---

Nowadays, spamming is a very common technique, and nobody can deny that. Spamming is so cheap that even a 10% opening rate is a tremendous success, because sending e-mails doesn't require paying fees to ISPs (like you would with mass SMSs and robocalls), and it also doesn't require hiring anyone to take the time to manually write and send e-mails (as opposed to postal spam, which have to be physically delivered into mailboxes by people), when you can just buy e-mail addresses in bulks by thousands of providers that sell active e-mail addresses by prices so ridiculously affordable, it lowers the barrier to entry dramatically.

This is why, nowadays, postmasters and system administrators need to be aware of (and correctly implement) the many technologies that exist to reduce the amount of spam received. Among them:
- [DKIM (DomainKeys Identified Mail)](http://www.dkim.org/)
- [DMARC (Domain-based Message Authentication, Reporting & Conformance)](https://dmarc.org/)
- [SPF (Sender Policy Framework)](http://www.openspf.org)
- [DNSBLs (Domain Name System Blacklists)](https://www.dnsbl.info/)
- [Greylisting](https://www.greylisting.org/)
- [SpamAssassin](https://spamassassin.apache.org/)

Most e-mail servers implement these tools to some degree, or use alternatives, of which there are plenty. Especially the first three elements are considered an integral part of e-mail technology nowadays, and most e-mail servers will reject your mail if you haven't properly implemented these technologies in your service.

However, like I said, spam being such a cheap technique, hasn't really decreased at all in volume. What happened is that spam filters now use machine learning and are able to train themselves on what legitimate e-mail and spam look like, decreasing false positives and greatly avoiding the chances of getting a spam e-mail in your inbox, but spammers haven't ever stopped trying, and it doesn't look like the problem is going to go away soon.

A few years ago, I came up with a technique that would dramatically decrease received spam volume, and, at the same time, hold the companies that sell your data accountable. Actually, it has many benefits, because it also increases privacy, by making the collection and discovery of accounts impossible by just trying e-mails from public breach dumps, and eliminates the chance of having your online accounts breached even with password reuse (but seriously, do **NOT** reuse your passwords, ever).

It all started because I used to have two e-mail addresses. A "spammy" one I would use to sign up for fishy stuff, or for websites that required me to log in to see their content. The other "good" would be my personal e-mail address, and I would use it only to sign up for trustworthy websites and give away to people so they can contact me. By "trustworthy" websites I mean companies that are relatively well established, such as Facebook, Twitter, Valve, Apple, you name it.

Well, turns out big companies also have data breaches. So my "good" e-mail address eventually ended up in public data leaks, and spam e-mail would start to flow in. Even though it was a Gmail account, and Google is rather great at stopping e-mail, I was still annoyed, mostly by the fact I didn't really know who betrayed me and gave my e-mail address away! (Or simply got breached). The worst thing is that if I attended to conferences, for example, a few months later I would start receiving unsolicited e-mail, not only from the organisers of the conference, but even from companies I didn't even know about, which I knew came from one of those conferences, but I couldn't prove it or specify exactly which of these conferences gave my e-mail address away.

We can all agree giving away your customer's information for marketing purposes is pretty despicable and shameless, but companies keep doing it because you can't really know who did it.

But what if you had a different e-mail address for every single service you signed up for? Well, even with password managers, signing up for a single Gmail account can take a few minutes, don't even imagine having to go through that process every single time you sign up for a website you might not use ever again.

So there had to be a better way...

# Catch-all domains

The solution I came up with didn't require any more time than what's typical when signing up for an online service, and shouldn't necessarily cost more than 10 dollars a year (if you already have your own e-mail server), but it had a profound impact in how I approach sign-ups nowadays.

Basically, I purchased a domain I would use exclusively for e-mail. It needed to be short and legible, so .com domains were out of the question, but the introduction of new TLDs have greatly decreased the chances of having your favourite domain name taken by [domain squatters](https://en.wikipedia.org/wiki/Cybersquatting), as you only have to choose a different TLD if the domain name you want is taken.

Then, I had to configure my e-mail server to accept mails from said domain, and have it redirect all e-mails to my actual inbox. Since replying from each address was not necessary, I didn't have to set up individual accounts on my mail server. If I ever need to reply to someone's e-mail, I reply with my personal e-mail address.

Now I can quickly identify where e-mail comes from, and if a company decides to sell my data to spammers, I will know immediately just by looking at the address it was sent to. For example, imagine my e-mail domain is "spameater.online" (hint: It isn't). If I sign up for Twitter as "twitter@spameater.online", and suddenly start receiving viagra ads addressed to "twitter@spameater.online", I'll know Twitter betrayed me, and I can decide to close my account there.

Now comes the best part. Since no real accounts were created to handle all the incoming e-mail traffic, if I start receiving spam at any of the addresses, blocking e-mail traffic is as easy as having my server (Postfix) drop all incoming e-mail for any address I want. Problem solved, my other e-mail addresses are not published (yet), so there's no need to worry.

As a side benefit, like I said before, even if I shared passwords across services, hackers would be unable to log in, because the e-mail address I use in every service is different. Obviously, if I used simply <nameoftheservice>@spameater.online, it wouldn't be very difficult for a determined attacker to breach into my accounts by changing the e-mail address, so instead of that, to make things even more difficult, I use alphanumeric combinations of variable length as usernames, like 1b7dp64aafg6@spameater.online for Twitter, 454gy4nm6a@spameater.online for Facebook, and m93c1@spameater.online for iCloud, and save the e-mail address and password to my password manager.

The results have been dramatically positive. In the last 2 years I have been using this experiment, I have received exactly 0 (**ZERO**) spam e-mails, although, in all honesty, I do apply all the technologies mentioned at the top of the article, so spammers already have a hard time spamming me, with the catch-all domain feature being just another layer I can quickly and effectively disable for specific addresses.

Want to learn more and/or implement it yourself? Check out the links below. If you still have questions, you can leave a comment below, or try getting in touch with me, but replies are not guaranteed 😉.

[https://serverfault.com/questions/560906/postfix-virtual-aliases-and-catchall-for-undefined-addresses](https://serverfault.com/questions/560906/postfix-virtual-aliases-and-catchall-for-undefined-addresses)

[https://tecadmin.net/setup-catch-all-email-account-in-postfix/](https://tecadmin.net/setup-catch-all-email-account-in-postfix/)

[https://www.cyberciti.biz/faq/howto-setup-postfix-catch-all-email-accounts/](https://www.cyberciti.biz/faq/howto-setup-postfix-catch-all-email-accounts/)
