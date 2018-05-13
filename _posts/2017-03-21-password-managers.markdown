---
layout: post
title: "Password managers"
date: "2017-03-21 19:29:29 +0100"
author: L1Cafe
tags: opinion infosec
---

The Internet nowadays is a very messy place. Identity theft, e-mail confirmations, instant notifications, mobile devices, multiple accounts, data leaks, account dumps, service breaches, malware that steals your bank's information, scammers, identity theft...

If you have ever read basic security and privacy tips you might be aware of some basic points:

1. Don't share your password with anyone, especially people claiming to be employees of these companies.
2. Always use hard to guess and long passwords, combining symbols, uppercase and lowercase letters, numbers, and never reuse them across services.
3. Don't use obvious passwords such as your e-mail address, your name, your wife's name, your birthdate or words like "password" or "1234".
4. Enable two factor authentication when possible, this adds an extra layer which, in case your password gets stolen, will make it more difficult to gain unauthorised access to your account.
5. Do a regular check-up of your online accounts, and change passwords.

These tips, while valid, are sometimes difficult to accomplish. Nowadays, almost every online service asks us to make an account, so that will be another new password we'll need to remember. As we sign up for more and more services, this becomes more and more unlikely until we reach the point of simply using the same password or using variations that are difficult for computers to guess but easy for human attackers, like changing Os for zeroes, or intentionally misspelling words (like writing "twtter" instead of "twitter"). This is not very wise, but fortunately, we have a better tool: password managers.

In this article, I will summarise a few options regarding password management, sorted by popularity ranking as per [AlternativeTo](https://alternativeto.net/) ratings.

## The most well known

### [LastPass](https://www.lastpass.com/)

![LastPass' interface]({{site.url}}/assets/images/2017-03-21-password-managers/lastpass.png)

Probably the most famous password manager. Released in 2008, their team of experts deliver a cross-platform experience, with extensions for the most used browsers, but also for less well known browsers like Vivaldi, Maxthon or Dolphin. Can work independently of the operating system because it can be used as a browser extension, so all you need is a compatible browser, and you can carry your passwords wherever you go. There are also applications for Android, iPhone, iPad, Windows Phone, etc. The premium plan, at 1 USD/month is rather affordable. It also has a few extra features, such as one that watches websites for breaches and notifies you if you have a stored password for a website that may have been compromised, or a password change wizard that allows for semi-automated fast password changing in supported sites that requires very little manual intervention.

#### Pros

- Well established
- Unified experience across devices
- Cloud sync
- Free plan
- Auto-fill features
- Can store encrypted files (with limits depending on the plan)

#### Cons

- Closed source, so it cannot be independently audited
- Cloud-based, local storage or third party sync (like Dropbox) is not feasible

### [KeePass](http://keepass.info/)

![KeePass' interface]( {{site.url}}/assets/images/2017-03-21-password-managers/keepass.png)

It isn't exactly an application, but rather, a standard. The application is completely open source and the most used implementation (KeePass), uses the .Net framework on Windows, and Mono on macOS and Linux. Because of its open nature, there are lots of third party implementations such as KeePassX (which uses Qt, much more desirable for non-Windows machines), KeePassDroid, and others. It is absolutely free to use, forever, and it doesn't carry a service or cloud with it: it focuses exclusively on password vaults as files. These files can then be easily synchronised using Dropbox, Resilio Sync, or your cloud storage tool of choice. These vaults are encrypted and they can be tuned to use stronger encryption (increasing the open delay, and decreasing the effectiveness of brute-force attacks). KeePass/X allows specifying a key file, a password, or a combination of both, making it a multi-factor solution. There are third party ports for Android, iOS, Windows Phone, Windows Mobile, and even web apps.

#### Pros

- Completely open source
- Cross-platform
- Fully free
- (Partly) audited
- Rudimentary auto-fill
- Allows storing arbitrary files inside the database

#### Cons

- Does not have cloud sync
- Notably uglier than other solutions
- Third party implementations sometimes lack minor functionality

### [1Password](https://1password.com/)

![1Password's interface]( {{site.url}}/assets/images/2017-03-21-password-managers/1password.png)

1Password has long been the prettiest of the bunch. It offers both a subscription based service, and allows paying a single time for a major version and subsequent updates. It has apps for Windows, and Mac, along with Android and iOS, and ships primarily as an app, rather than as a plug-in (although, browser plug-ins are available for integration). It supports a few OS specific features, such as the Touch Bar and the Touch ID on macOS and iOS, fingerprint authentication on Android, and, like LastPass, it has a service that watches for data breaches.

#### Pros

- Very well-designed interface
- Allows for both cloud and local storage
- Can store files
- Auto-fill features

#### Cons

- A bit pricier than its alternatives, no free plan
- No other desktop OS support besides Windows or macOS

## Other alternatives

While less known, there are other ways to manage your passwords.

### [Encryptr](https://spideroak.com/personal/encryptr)

![Encryptr's interface]( {{site.url}}/assets/images/2017-03-21-password-managers/encryptr.png)

Tiny little app that allows for fast password management. It's clean-looking, and it's cloud-based, so you'll never lose your passwords. It's cross-platform and it uses the Electron framework to bring a unified experience across operating systems. It has apps for Windows, Linux, macOS, as well as Android, iOS. It's free and open source. Doesn't allow storing files, only plain text.

### [Enpass](https://www.enpass.io/)

![Enpass' interface]( {{site.url}}/assets/images/2017-03-21-password-managers/enpass.png)

A newcomer to the password management industry. It is completely free to use on desktop (Windows, macOS, Linux), but its mobile apps (Android, iOS, Blackberry, Windows Phone, Android Wear, Apple Watch) have a one-time fee. It looks good and allows for both local and third party cloud-based storage. Supports Windows Hello, Touch ID, Android fingerprint, etc.

### [True Key](https://www.truekey.com/)

![True Key's interface]( {{site.url}}/assets/images/2017-03-21-password-managers/truekey.jpg)

Backed by Intel, it features biometric authentication, and is free for up to 15 passwords. After that, it requires a yearly subscription to use on Windows, macOS, iOS, and Android.

### [Master Password](http://masterpasswordapp.com/)

![Master Password's interface]( {{site.url}}/assets/images/2017-03-21-password-managers/masterpassword.png)

This one is a bit different. Instead of saving passwords in a file database or saving them on the cloud, it generates your passwords based on your master password, name, and website, so it never stores data.
