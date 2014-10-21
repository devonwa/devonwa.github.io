---
layout: post
title: Use a password manager
tags: Efficiency, Technology
type: article
description: If you only use a handful of passwords on the internet, you're putting your identity at risk. Use a password manager to easily create and maintain passwords tailored to every website you use. Read how to implement Keepass2 as your new password assistant.
---

If you're like me, you use around 10 sites every day, and another 20 or so sporadically. There's also that untold number of sites left in the dust after you pillaged their free trial or something. But even though you don't use them, they still have one of your rotated passwords, your username, and probably your email address (which doesn't use the same password _right_?).

Let's say a big company, like [Adobe][] or [LinkedIn][], gets hacked and your account info is leaked. Now you need to figure out how many other sites use the same username and password and change them. There is no chance I'm remembering that. The only solution is to have a unique password for that site, so when it does get hacked, I only need to change that one password.

<small>\* One great site I found while researching this article is [haveibeenpwned.com][] by Troy Hunt.</small>


## Introducing the password manager

I use [Keepass2][] on every desktop computer and mobile device I own. It's free and open-source: I don't want someone holding my passwords for money, and I want everyone to be able to see the underlying security algorithms (not me though, I'm not that smart).

### The perks

* Automatically generate a different password **for every site**
  * You provide the username and Keepass provides the password (or you can).
* One-click login
  * Keepass can open the login page in your browser and automatically log you in. I've had varying success with this.
  * There's also room to write down notes or web adresses.
* Logical organization
  * Think folders. There's also room in each log to add notes or web addresses. Remember these things are all encrypted as well.
* Multi-device synchronization with Dropbox
  * There are desktop and mobile apps for Keepass, which can all link up with Dropbox.

### The parts

There are three parts to Keepass2 that I'll be referencing in the following sections.

1. **Password database**: The vault. A password database is the encrypted storage container of all of your passwords. With Keepass, it's your passwords.kdbx file.
2. **Key file**: Part 1/2 of logging into your password database. The key file contains a huge string of bits that represent an incredibly unique password. This lives on your device and only transfer it to other devices using Keepass and you trust. Keepass will remember where this is located on the device, so you won't have to go find it each time.
3. **Master password**: Part 2/2 of logging into your password database. A password you use in conjunction with the key file to unlock your password database. This is a backup to the key file, not the major source of security. It will give you some time if both your password database file and your key file are compromised. Keepass will ask you for this password every time you open your password database.


## Adopting Keepass as your password manager

It's simple to get the setup I'm about to explain up and running. For my setup, you'll also need [Dropbox][] installed across each device you plan to use Keepass on.

### Installing Keepass2 on Windows / Mac / Linux

Keepass2 can be installed on any OS, as can Dropbox. For mobile, I use [Keepass2Android][] to access my password database file on Dropbox (also installed on the device). There's also [MiniKeePass][] for iOS users, but I haven't tested this personally.

After installing Keepass, create a new _password database_ file. Tell Keepass to generate a _key file_ for this database, and also give it a _master password_ of your choice. The password should be something easy for you, maybe 8 to 10 characters (remember that the key file is your main security, this is just a temporary backup). Now you have a password database file (.kdbx) that uses your key file and your master password as its login credentials.


### Setting up Keepass with Dropbox

I think cloud storage is crucial for a password manager to be useful because I need my passwords everywhere, and I need to be able to update them everywhere. That's where Dropbox comes in because systems can read and update the files in a Dropbox folder, just as they would any other folder.

With your password database file (.kdbx) created, move it into your Dropbox directory. Now boot up Keepass and login using the key file and your master password. That's it. You're in.

Once you get the feel for things, add Keepass to your other devices. No need to create a new password database file, simply point to the one you've already placed in your Dropbox.  Other than that, it's time to start switching all of your old passwords over to your password manager.

Personally, I took awhile to commit and only switched a few passwords at first. I got to test-drive how I liked the setup and then finally took the plunge about a week later. I'd recommend you do that too, using sites that you visit fairly frequently.


### Don't keep your email password in the password database

An important note: Do not keep your email address password in Keepass. If you ever lose access to your password database, then it'll be hard to open a recovery email on an account you can't get into. Gmail offers a service for getting into a lost email account by providing your phone number up front, so I recommend setting that up.


## Potential negatives and solutions

This setup has some downsides that I need to tell you about so you can react appropriately if something "bad" ever happens.

### I don't have access to my password manager and I need to login somewhere

This is another reason to keep your email address password outside of the password vault. Resetting a password is usually a quick process: the website sends you a link in an email, then you pick a new password. Just do that if you're in a pinch and change the password in Keepass the next the time you have access to it.


### One possible hacking scenario...

Someone hacked Dropbox and got my encrypted password database.
&nbsp;_They did!? Shiii....._

Well at least they don't have my local key file.
&nbsp;_They do!? Shiii....._

Well they definitely don't know the password that I only use for this one thing.
&nbsp;_... Phew, okay cool._

But it's time to get to serious work. You need a new key file and then update the passwords on all sites in your password database.


## Alternative password managers

This method of setting up Keepass requires a bit of technical skill, so there are other sites out there that do the same if you're willing to pay them.

* [LastPass][] - It's nice that every part of this service is in the cloud, so you don't need to do the whole Dropbox setup. Mobile use requires a subscription fee.
* [1Password][] - Also a solid choice. You need to purchase a license after a trial period. Not open source and no linux support.

[1Password]: https://agilebits.com/onepassword "1Password password manager"
[Adobe]: http://blogs.adobe.com/conversations/2013/10/important-customer-security-announcement.html "Adobe Account Compromise"
[Dropbox]: https://www.dropbox.com/ "Dropbox cloud storage"
[haveibeenpwned.com]: https://haveibeenpwned.com/ "Check if one of your accounts has been compromised"
[Google Drive]: https://drive.google.com/ "Google Drive"
[Keepass2]: http://keepass.info/ "Keepass password manager"
[Keepass2Android]: https://play.google.com/store/apps/details?id=keepass2android.keepass2android&amp;hl=en "Keepass2Android on the Play store"
[LastPass]: https://lastpass.com/ "LastPass password manager"
[LinkedIn]: http://blog.linkedin.com/2012/06/06/linkedin-member-passwords-compromised/  "LinkedIn Account Compromise"
[MiniKeePass]: https://itunes.apple.com/us/app/minikeepass-secure-password/id451661808?mt=8 "iOS KeePass Manager"
