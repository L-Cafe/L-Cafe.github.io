---
title:		"What Apple's T2 chip means for the security of Macs"
date:		  2018-10-12
tags:		  opinion Apple
comment:	header sizes are 2000x750
header:
  image:		assets/images/2018-10-12-apple-t2-security/header.jpg
  teaser:		assets/images/2018-10-12-apple-t2-security/header.jpg
  caption:	"Photo credit: [**Unsplash**](https://unsplash.com)"
---

An [article](https://www.macrumors.com/2018/10/04/t2-macs-must-pass-diagnostics-for-certain-repairs/) by Mac Rumors was recently published, revealing Apple had designed a component, which, according to an Apple document, would "result in an inoperative system" in the event a hardware replacement or repair was performed without running a proprietary diagnostics solution after.

Naturally, e-waste activists and supporters of the "right to repair" movement (like me) are annoyed by this new chip that would, apparently, make it impossible to perform third-party repairs, which are much cheaper, or outright turning the computer into a very expensive paperweight when Apple decides to stop repairing those devices because they are considered too old to be accounted for.

{% include video id="4KuVvb9DTaU" provider="youtube" %}

This, however, is nothing new. Apple has, at least since the inception of the iPhone, tried to keep people away from their software (closing loopholes used by jailbreakers) with increasing success as of lately, as well as hardware (using special screws and gluing parts). It is very clear why a company like Apple would want to do this: Their business model is selling hardware, and the more they sell, the better. Even better if they can make their devices last less so you have to buy new products more often. Louis Rossman, an electronics technician, has, repeatedly, shown how [Apple designs their products so that only they can open and repair them](https://youtu.be/4KuVvb9DTaU) (video above). This is a very widespread practice across the whole consumer electronics industry, and other companies have quickly adopted designs that leave removable batteries and expandable storage out, in favour of planned obsolescence. This goes back as far as 2007, at least, with the infamous [Sony Timer](https://en.wikipedia.org/wiki/Sony_timer).

What I've seen so far, are heated up arguments on how Apple and others are intentionally building devices with an artificially short lifespan. However, I believe we're not considering some key details, and I want to provide an additional point of view, which is entirely my opinion, on why this is might be a good idea even for the consumer.

Supply chain security is important as evidenced by the [recent news](https://www.bloomberg.com/news/features/2018-10-04/the-big-hack-how-china-used-a-tiny-chip-to-infiltrate-america-s-top-companies) by Bloomberg. Although security experts and the companies accused of having been infiltrated are not completely sure of the sources Bloomberg used and the veracity of the document, some experts argue that this is definitely possible, and we must be very careful with the security of hardware. One thing is clear: the best way to be reasonably safe from security issues is to control the full supply chain, and Apple has a higher degree of control over the supply chain for MacBooks than, say, Lenovo or HP for their laptops.

Computer Security in general, has become a critical asset that any company nowadays must take seriously. And it's not just evil hackers selling credit cards in the dark web from a basement, we also must be aware of State-sponsored attacks, terrorist organisations, and organised crime gangs.

{% include video id="loBX_vEXxVA" provider="youtube" %}

Remember the video of the [hardware backdoor installation](https://www.youtube.com/watch?v=loBX_vEXxVA)? (video above). It's very clear that once a computer is physically in the hands of a skilled criminal, the possibilities are endless. From attaching a hardware keylogger, a GPS tag, replacing the Ethernet controller firmware with a malicious one, cloning the hard disk... It's exactly as easy for an attacker as it is for a third-party repair shop that is not associated with the laptop's brand.

This could be impossible to do in a MacBook Pro 2018 with the T2 chip: If Apple is performing the appropriate verifications at boot time to ensure the hardware hasn't been tampered with, there is simply no way to break into a MacBook Pro, especially because almost all its parts are either glued-in or soldered into the mainboard. Of course, there's also the issue of [Thunderstrike](https://trmm.net/Thunderstrike_FAQ) in regards to malicious Thunderbolt devices. The good news is, as a proof of Apple being committed to security, [they hired the team behind Thunderstrike 2](https://appleinsider.com/articles/16/02/02/apple-hires-firmware-security-experts-who-worked-on-thunderstrike-2-exploit).

Apple is, out of the big four technology companies (Microsoft, Google, Facebook, Apple), probably the one that treats their clients' security and privacy with the most respect. Microsoft injects ads in Windows 10, and bundles all sorts of tracking into the OS. Google runs literally the largest advertising agency (DoubleClick), and gives lots of free services in exchange for user information and placing ads and tracking features everywhere. I don't think it's even necessary to mention what Facebook does at this point. Apple, however, has a radically different business model: Their products are aimed at the higher end of the purchasing power spectrum, are beautifully designed, and, while Apple used to run an advertising service, they made so very little money off ads they shut down their iAd App Network service.

In general, I would argue that Apple products are expensive because they refuse to sell user data and/or bundle bloatware into their devices. The sad truth is that even high-end, expensive smartphones like the Galaxy S series still carry bloatware.

Wrapping it all up: it's probably safe to assume Apple is bundling the T2 chip to artificially limit the lifespan of their devices, so people buy more devices more often: after all, that's how they really make money. But the truth is we don't really know if that's the case, and while it's true shortening the lifespan of a device is a very good reason to bundle such chip, it also brings security features that no other brand can offer.
