+++
date = "2013-04-02T21:19:49+01:00"
draft = false
title = "An agile process improvement braindump"

+++

At work, I'm trying (where I can) to help push process improvement all the time. When things are slow, inefficient and painful I get grumpy. I'm not the only one, and I have the privilege to work with some great people with great minds and ideas. Together we are making, and have made some good progress. 

I've been meaning to write something about continuous integration and/or delivery, agile methods etc. General development/software processes etc. How to do things right. But I don't think I am yet qualified to write with any authority on these subjects - so I am going to settle for this...

<!--more-->

Warning to the reader - this is nothing but a brain dump... Just a list of (no-brainers) things that I've been involved in as part of process improvement that have proven to be really successful to-date:

* **Requirements are a business requirement**: No brainer, right?

* **Everyone is a customer** (or stakeholder): our customers, the business, the helpdesk/support team, operations/infrastructure team, data/reporting services, the testing team and us - the developers. We *all* have first-class requirements. Even technical requirements end up being business requirements if it helps a system running smoothly.

* **Test First Approach**: call it whatever you want, it should involve as much as time can afford and include anything you can justify automating. Testing functionality, behaviours, requirements, performance, logging, application black-box, end-2-end systems and integration. Do whatever you can to get the most bang for your buck.

* **Peer-review of code**: okay not every line of code needs to be looked at. But if you focus on some of the following, you'll do well from it: schemas/contracts/interfaces, automated test cases, algorithms (the real meat of the matter parts). Try using a Pull Request in git to force this as part of the development process.

* **A good version control process**: version control should be considered the back-bone of a good development process. Without it you're dead in the water. There are no excuses in this day and age. But you should (as a team) take the time to discover a process/workflow that suits you and your projects, document it, understand it and use it to your advantage.

* **Continuous integration**: I don't just mean installing a CI server somewhere and automating builds. Don't be scared of introducing new functionality (or removing it in fact). Don't work on something new all alone and then struggle for a week trying to re-integrate that work into the main development line. Understand how to introduce changes without breaking the software and do it as often as possible.

* **Practice Failover and Rollback**: Things shouldn't go wrong - but when they do, how you respond is incredibly important. Have understood failure and rollback plans in place - and actively test them. If you have plans and are too scared to test them on your production systems, then you obviously are not confident that they work. If failover and rollback are fast and seamless - you will appear error free.

* **Monitoring and Logging**: are first-class business requirements. You need to know what is going on, and when. If (or rather when) your systems fail (and you've handled it with a failover or rollback plan!) - it is incredibly important to be able to look at the reasons why.

* **Script everything**: If you do something once, fine, manually is okay. If you do something twice - write a script for it already. Even if you don't use the script again, or it is broken by the next time you come to it - it will at least serve as a method of documenting how and what you did.

* **No process is perfect**: Review and question what and how you do everything - every week you should aim to be better than last week. Review existing processes - pick something that is bad, and try and make them better. Don't always strive for perfection - take baby steps; every single week. Given a few months and you'll be in a better place.
