---
title:    "The five phases of ethical hacking"
date:     2019-02-10
tags:     hacking
category:	Cybersecurity
comment:  header sizes are 2000x750
header:
  image:    assets/images/2019-02-10-five-phases-hacking/header.jpg
  teaser:   assets/images/2019-02-10-five-phases-hacking/header.jpg
  caption:  "Photo credit: [**Unsplash**](https://unsplash.com)"
---

An Ethical Hacker is someone who specialises in computer security through what's
known as "Penetration Testing", which are series of exercises carried out against
computer systems to spot vulnerabilities and weaknesses.

Ethical hacking is generally divided into five steps, each of which to be performed
one after the other. This is the methodology most penetration testing professionals
apply.

## Reconnaissance

This is the first phase of the process, and involves gathering information about
the target passively. For example, using [Shodan](https://www.shodan.io),
visiting the company website to understand their network infrastructure and employee
hierarchy, searching Twitter for official accounts and more. In this stage, the
hacker does not interact directly with the company systems, but simply uses OSINT,
or Open Source INTelligence.

## Scanning

After Reconnaissance has been executed, it's time for the scanning phase. The
scanning phase involves interacting and communicating directly with the company's
servers with tools like Nmap, Nikto or Nessus, to get a deeper understanding of the
network topology, open ports, software versions, hidden directories and vulnerabilities
present in the systems.

## Gaining access

Once the ethical hacker understands all the weaknesses and entry points in a network,
they can launch an attack in order to obtain privileged access into the system,
for whatever purpose they need. They might want to use the first attack to pivot
to a different server, or they might want to get root access to an HTTP server running
inside the company's LAN, or just dump databases from an unprotected PostgreSQL server.

## Maintaining access

To ensure the success of the operation and be able to exfiltrate as much data as possible
the attacker will want to create a form of persistence in the affected systems, be
it through Unix crontabs, Windows task schedulers, daemons, modifying legitimate
binaries, and more. To this end, the attacker will have to remain stealthy because
modifying a target system will often leave a trace that can be spotted by network
and system administrators.

## Covering tracks

The final step is reverting changes to leave as little trace as possible,
such as deleting logs, re-enabling security measures, and deleting temporary files
that aided in the attack. This will help the attacker stay in the network for
as long as they want, while ensuring nobody suspects they've been attacked.
