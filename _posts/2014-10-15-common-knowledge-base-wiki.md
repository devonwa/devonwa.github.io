---
layout: post
title: Create a Common Knowledge Base with a Wiki
tags: workflow
type: article
description: Using a private wiki among the lab group creates a common knowledge base. It lessens the importance and risk of individual members becoming experts, and allows teammates to quickly create content in a tone and style specific for the group to digest.
---

## Knowledge presents an issue

A major struggle we've encountered in our lab has been information propagation among group members. There was always a pretty vague question about obtaining knowledge: what do I read or who do I talk to to learn about X? That question is a time waster and deterrent for people wanting to act independently. Sure, learning about the thermodynamic properties of a material is a well documented thing in the world, but in *my* lab, where is the procedure for running experiment Y?

### Centralizing knowledge

The solution is to have a centralized point where everyone knows to go in order to learn about anything in the lab. A lot of the time, procedures are written down but it isn't centralized; it's hidden inside some archaically named MS Word document in an unknown directory. By putting information in a central and easily accessible source, group members are empowered to be independent. Bringing new employees onboard also becomes easier and less of a time commitment for the other members to educate them.

### Reducing experts

Sometimes the information doesn't exist in any documentation. Sometimes experiment Y was made by Greg and only Greg knows how to perform it, or only he knows how to clean the equipment properly. This is an issue because now Greg is an expert and the only way for others to get this information is to ask him. That's a hassle for both the person who needs the information and for the expert, because it's going to take up their time. Also, what if the expert leaves the lab? Someone else would need to try to inference what the expert did and do that learning all over again. Worse is that you usually you don't know how many areas someone is an expert in until they leave.

## One solution: A wiki

A wiki is the best solution I've found for centralizing knowledge in our lab for several reasons.

### Familiarity

First and most importantly, everyone is familiar with [Wikipedia][] and the general feel of navigating a wiki-like structure. This is a key factor in the group adopting a new technology because being familiar with a product is the first step toward actually using it. It's also intuitive and simple to find the information you need, no matter how far buried in a document, by using the search feature. Searching is fast and thorough because all the information for every page is stored in a database.

A downside of a wiki is that you only get out of it what you put into it. No one person in the group should have the responsibility of maintaining the wiki. It's crucial to have everyone in the group contributing to the wiki so that it grows quickly and stays up to date. It should probably go without saying that the expert in topic area should be writing the article.
 Think of it as writing up a process as an informal and easily digestible [SOP][].

### Sparking content creation

The second reason for a wiki is that creating content is simple. To start, I created a few [stub][] articles as placeholders with a little content in each. No one was eager to start creating articles, but they did edit my stub pages and increased their content. Then after making enough edits, gaining enough familiarity, they felt comfortable creating their own articles.

The [formatting][MediaWiki - Formatting] for the wiki I chose was also really easy to pick up. It's very similar to [Markdown][], if you're familiar with that (a well-thought out, text-only formatting language). If you have the time, you can get really crazy with plugins and custom styles to add functionality or to make your wiki's appearance personalized. I don't have the time, so I'm glad it works and looks great out of the box.

### Easy access

Our wiki can be viewed easily on any device that has access to our private network (ie. open an internet browser to *http://my_server/wiki*). That means you could bring up a process procedure on your computer while you operate equipment, or reference a wiring schematics on your phone while you're working on the control system. You're not limited to managing a private server, as I'll explain later, there are also web hosting options to access your wiki from anywhere. If parts of the wiki are more sensitive than others, you can setup security for individual users to be restricted in what they can access or edit.

## Owning a wiki: the details

I use [MediaWiki][] to host my group's wiki on our private server. It's free and [open source][MediaWiki - Repository], and the documentation surrounding the project is excellent (both on the project site and on [Stack Overflow][Stack Overflow - MediaWiki]. There are tons of choices for wiki software out there, both for hosted and self-hosted options.

### To host or to own your server

There are websites that provide wiki hosting, like [WikiDot][], [Wikia][], and [others][Wiki Hosting List]. I can't recommend any because I use a private server (sorry), but the prices seem to fall between free and reasonable. I set our wiki up on a private server because it cost nothing and I was unsure if it would actually be used.

I like owning the server because I can make edits to the PHP or install plugins as I please. This may end up to be more hassle than it's worth in the end, though, as the base functionality of a MediaWiki is excellent. As our wiki grows, I have no idea what the load of a MediaWiki would look like at scale. You may want to do additional research if you have a big team and feel like the wiki is going to grow quickly.

### Getting MediaWiki up and running

MediaWiki is built on top of PHP and MySQL, so these are both dependencies that you'll need installed on your system. I followed the Windows 2008 R2 guide in the project's [installation guides list][MediaWiki - Installation] and it worked out really well, though the server had its idiosyncrasies. That's just the server I had available, though, and I imagine installation on a linux box would be incredibly simple (maybe 20 minutes max?).

## Wrap up

So using a wiki leads us back to addressing the original issue of having too many experts in the lab. Now instead of any single person being an expert, anyone can reference the documentation on the wiki and be brought up to speed quickly. That's not to say certain people aren't the defacto experts on something, but if they leave the lab then someone else is able to step into their role.

A wiki continues to be a great tool for my group, but I definitely didn't have a list of all the other solutions I could have gone with. Let me know in the comments if your lab uses a different and equally excellent tool.


[MediaWiki]: https://www.mediawiki.org/ "MediaWiki"
[Markdown]: http://daringfireball.net/projects/markdown/ "Markdown"
[MediaWiki - Formatting]: http://www.mediawiki.org/wiki/Help:Formatting "MediaWiki - Formatting"
[MediaWiki - Installation]: http://www.mediawiki.org/wiki/Manual:Installation_guide "MediaWiki - Installation"
[MediaWiki - Repository]: https://git.wikimedia.org/tree/?r=mediawiki/core.git "MediaWiki - Repository"
[SOP]: http://en.wikipedia.org/wiki/Standard_operating_procedure "Standard operating procedure"
[stub]: http://en.wikipedia.org/wiki/Wikipedia:Stub "Stubs"
[Stack Overflow - MediaWiki]: http://stackoverflow.com/search?q=mediawiki "Stack Overflow - MediaWiki"
[WikiDot]: http://www.wikidot.com/ "WikiDot"
[Wikia]: http://www.wikia.com/Wikia "Wikia"
[Wikipedia]: https://www.wikipedia.org/ "Wikipedia"
[Wiki Hosting List]: http://en.wikipedia.org/wiki/Comparison_of_wiki_hosting_services "Wiki Hosting List"

<!-- Working outline
- Intro - need for a common knowledge base
- - Too many experts
- - Onboard time of new group members
- Why a wiki is the right choice
- 1. Familiarity with wikis from Wikipedia
- 2. Simple content creation
- 2. - simple editing
- 2. - uploading pictures
- 3. Access it anywhere on the private network
- - Have a program with a help section? Link to sections of wiki pages
- How to wiki
- Conclusion
-->
