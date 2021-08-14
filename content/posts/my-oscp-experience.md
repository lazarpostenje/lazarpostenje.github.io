---
author: "Lazar"
title: "My OSCP Experience"
date: "2021-06-17"
description: "This is my story on how I obtained OSCP certification first try at 17 years old."
tags: ["OSCP", "Experience", "Story"]
ShowToc: true
ShowBreadCrumbs: true
---

## Introduction to "trying harder" 

### How it all started

When I was 14 years old, I was amazed by how hackers could do something which seems impossible to an average non-IT person. After some googling and researching, I found out about OSCP certification and after reading bunch of reviews and experience stories about it, it seemed impossible for me to obtain it. I got so scared that I forgot about it completely and started learning web development. Three years of web development later, when the 2019 was coming to an end, I wanted to set a goal for "New Year's Resolutions" and I thought of that "scary" certification - OSCP. Since COVID-19 breakout caused global lockdowns, I had all the time in the world to study for it.

![OSCP](https://raw.githubusercontent.com/lazarpostenje/lazarpostenje.github.io/master/assets/oscp.png#center)

----------

## My "not so good" approach

When I officially started, 1st of january 2020, I didn't know basic UNIX shell commands. I was so noob that I thought `cat` command meant the literal house cat and I was like "*what the f\*\*\* is this all about? A cat in the command line?*". The first thing that I had to learn is how to use basic shell commands and how to navigate the system, learn how files and directories are organized in both Linux and Windows. I obviously had **0 experience** with any of pentesting stuff. Luckily I had some understanding of networking and programming.

### After the basics

After learning the basics of command line, the next logical step for me is to get out there and do things! I remember trying to do **Traverxec** box on [*HackTheBox*](https://www.hackthebox.eu) pentesting platform with very little success. It took me 15 days to root it, even though the initial foothold was *Metasploit* module for RCE and privilege escalation was simple sudo command. The whole *get out there and do things* was very bad approach because it involved me googling for hours and hours just to find out how *that one thing* is called.

### The real progress

I started seeing progress after regularly watching [Ippsec's](https://www.youtube.com/channel/UCa6eh7gCkpPo5XXUDfygQQA) videos on retired [*HackTheBox*](https://www.hackthebox.eu) machines. To replicate his videos, I had to purchase monthly VIP subscription on HackTheBox which **really paid off**. I was so obsessed with pwning retired boxes that I sometimes forgot to eat.

Of course, I took notes while watching Ippsec's videos, I didn't figure out those boxes by myself. I did not want to waste time googling stuff that I can easily watch being explained by Ippsec. I watched one of his videos every single day, following **TJ Null's** [OSCP-like boxes list](https://docs.google.com/spreadsheets/d/1dwSMIAPIam0PuRBkCiDI88pU3yzrqqHkDtBngUHNCw8/edit#gid=0) and taking notes on what stuff can be exploited, what to search for when I see *X* port open. 

![TJ Null OSCP Like boxes](https://raw.githubusercontent.com/lazarpostenje/lazarpostenje.github.io/master/assets/tjnulloscplikeboxes.png#center)

Quickly after, I developed my own methodology which was mostly based on his, with little adjustments which suited me better. **You probably won't succeed without self-explanatory and detailed notes.**

### How I took notes

I used a free software called [Joplin](https://joplinapp.org/) which is available on all kinds of devices, **for free!**. Joplin is a markdown editor which has built-in backup feature. I connected it to my dropbox account and **voila**! Free notes! 

![Joplin](https://raw.githubusercontent.com/lazarpostenje/lazarpostenje.github.io/master/assets/joplin.png#center)

# Beginning of PWK

After 6 months of watching Ippsec's walkthroughs, I finally decided to purchase *90-day lab period*. After cashing out $1350, I felt really scared because at the time, that was all of my savings, I even had to sell my phone and downgrade just to do PWK. My lab time started on 20th of june 2020 at 2am. I couldn't sleep that night, all I did was browsing around the student forums and learning how to use the PWK Labs control panel. On the second day, I started reading the PDF and watching videos. 

## The mistakes I made

Going through the material, I immediately started doing the exercices and documenting them until I burned out from all the studying. It felt like I wasn't making progress, like I was stuck and the depression jumped in. I quickly abandoned the PDF and videos and jumped straight into labs - **BUT YOU SHOULD NOT DO THE SAME!**. That was the dumbest thing I ever did, because I had to refer back to the PDF every time I got stuck on a machine which cost me a lot of *precious* lab time. 

Second mistake that I made was not communicating with others. I didn't have any contacts to discuss about anything related to penetration testing and I felt like I was alone at it. **The community is very supportive and go out and make as much as *pentesting friends* as possible! Sharing experiences and knowledge is what makes pentesting fun**.

## Buffer overflow - how?

PWK module for buffer overflow didn't do much for me, not because it is bad, but because it only gave me 2 examples on how to exploit the vulnerability. I needed more throrough explanation so I saw on [r/oscp](https://www.reddit.com/r/oscp/) that everyone recommended The Cyber Mentor's [Buffer Overflows Made Easy](https://www.youtube.com/watch?v=qSnPayW6F7U) playlist. That playlist is a **gold mine** of BoF resources and explanations. He did a very good job making it as simple as possible and I recommend everyone to watch it.

After learning basic BoF, I moved on to some non-PWK labs to practice it even further. I stumbled upon TryHackMe [Buffer overflow prep](https://www.tryhackme.com/room/bufferoverflowprep) room which had 10 unique binaries to exploit. **THIS IS ALL YOU NEED FOR PWK BOF!!!**

## The labs

I could not do any of the lab machines on my own, and I had to rely on hints from student forums. Which seems like *bad* thing to do, but it worked for me. All I needed at the time was the **experience** in popping shells and knowing what attack vectors are possible, because I didn't know some of them even existed. If I hadn't checked the forums for hints, I would've probably wasted so much lab time googling the stuff I had in front of my eyes. **BUT** the important thing is that after looking at the hints, I did not just move on, but I looked at it deeper and researched about why does that vulnerability occur and how to exploit it. 

Couple of days later, I caught COVID-19 and couldn't leave my house for a month because of the quarantine. I used all of that time to study and root boxes in the lab because what else could I do? Schools and everything else was closed, even the lockdown got much more strict, which is why I had so much time to invest into labs.

After some time, I managed to move between the departments and eventually I rooted whole PWK labs, having almost 2 months left to do exercices and finish PDF. I felt ready for the exam, but I did not want to schedule it 2 months earlier! So I sat down and started doing **boring** exercices. Exercices were the most difficult thing on PWK! They required you to google a lot, to think out of the box and sometimes you don't know what is the goal of the exercice and get confused. After a month, I finally managed to finish the lab report documenting 10 lab machines, step by step, and all required exercices. I remember finding on r/oscp a **checklist of all required exercices and you should too!**. Don't get me wrong, exercices were very useful, and those 10 lab boxes which you had to document are the best practice for final **exam report**. At the end, it was worth it.

## External labs

Not knowing what else to do, I bought a subscription on [CyberSecLabs](https://www.cyberseclabs.co.uk/) which paid off! They don't have the same difficult boxes as for example, HackTheBox, but they teach you a vast majority of misconfigurations and common vulnerabilities which were left out from PWK labs. Their boxes we're usually very easy, but still a good practice and confidence boost right before the exam!

------

# The Exam

I scheduled my exam for 15th of september 2020 at 12pm. That time worked the best for me, but it might not do well for you. The night before the exam, I could barely sleep. I had to take melatonin just to stop my thoughts and fall asleep. 

After I woke up, I realized that I didn't have a camera which is **required** so the proctor can see and identify you. I panicked but I managed to get one from my friend. Before the exam started, I sat down and made a strategy for next 23 hours and 45 minutes.

## The strategy

Before the exam, I opened up my two main cheatsheets I commonly browsed, full of useful info, and of course, my own notes from PWK labs which I cannot share for obvious reasons. Those are the links:

- [Sushant747 gitbook](https://sushant747.gitbooks.io/total-oscp-guide/content/)
- [Hacktricks book](https://book.hacktricks.xyz/)

I used [NmapAutomator](https://github.com/21y4d/nmapAutomator) to scan all boxes except BoF one. While I did the BoF, results came back for all other boxes saving me some time. I used tmux to make multiple windows, and ran `nmapautomator` on all of the boxes.

My strategy was to take a 15 minute break every 2 hours or after every time I get root. 

## Workflow of the exam

I first jumped right into the BoF box, where they give you the **debug machine** to test your payload. I managed to do it in 35 minutes, taking screenshots while following the cheatsheet at [noobsec](https://www.noobsec.net/bof/). After getting first 25pts I felt **VERY** motivated thinking that I'll finish the exam very soon. 

After BoF machine, I took a break for 15 minutes and started my 2 hour timer, while examining enumeration results of 25pt box. After examining the results, I had no idea what to do! I felt very scared because I really wanted to do the hardest one first. I didn't know what I was looking at, I felt the same way that I felt when I did my first ever box, *Traverxec*. 

After 2 hours, the timer rang and I had to move on to another box, wasting 2 hours googling the stuff I haven't even heard about, without much success. I moved on to 20pt box, which I owned in 4 hours. It wasn't straighforward at all, full of rabbit holes.

After rooting the box, I knew that I had 55 points, 8 hours in. Not bad. I had to choose between going back to 25pt box or doing 10pt + 20pt. I didn't want to reply on my lab report to pass. I moved on to 20pt box, examining the results and not finding anything for next 4 hours. I felt very scared and stressed that I tried to sleep, but I couldn't! The adrenaline was rushing through me and I couldn't even close my eyes. I knew I had to try harder.

Without anything on 20pt box, I moved on to 10pt one where I quickly found the way to  exploit but for some reason I just couldn't! I used my **metasploit allowance** which did not help me. That box was very frustrating and I had to ask offsec staff to verify if it is exploitable or not, and after 2 hours they said that it is working properly. 

16 hours in, still at 55 points without any progression. I decided to go for a walk and come back after half an hour. I came back to 25pt box and caught myself doing all the same things that I did the first time tackling it, so I stopped and thought "Why don't I do something else?" and I started thinking out of the box. Soon I realized what I've been missing, and found initial foothold which was very tricky to get working. 2 hours later, I finally got user on 25pt box. But still it was not enough to pass, so I **tried harder**.

After 2 hours of enumerating the 25pt box internally, I found out what I needed. It took me an hour to get exploit to work, and I got code execution as root user but I could't replicate it again. It took me some troubleshooting to get it working again. I was at 70 points + lab report. I knew that I have enough points to pass and I shouted "YEEEEEAAAAAHHH" as loud as I could. Because of all the adrenaline and happiness coming out of me, I told myself why not try those 2 remaining boxes? And I managed to root both of them in 1 hour, ending the exam after 23 hours.


# Report day

After I ended the exam, I took a nap and dreamed of me failing which was the worst nightmare I've ever had. After waking up I realized it was just a dream and that I have 105 points in reality! Such an amazing feeling.

While doing the boxes, I screenshotted everything I could, maybe I did it too much, but it is better to over-do it than under-do it. For report, I followed *John Hammond's*  tutorial on note taking and report generating which you can see [here](https://www.youtube.com/watch?v=MQGozZzHUwQ). Markdown made it much easier and much faster to write a report. My report consisted of 60 pages, where I explained everything with as much detail as I could. 

Offsec is strict when it comes to reports - you must know what you did on the exam. You need to explain it. If you aren't native english speaker, like me, don't worry, they don't mind some grammar mistakes but you should know the basics of english.

I finished my report after 6 hours, read it couple of times before submitting and waiting for the results.

----

# The results

This email brought me so much happiness knowing that I achieved something I worked really hard for 9 months! I successfully completed the goal for 2020!

![Result](https://raw.githubusercontent.com/lazarpostenje/lazarpostenje.github.io/master/assets/result.png#center)

One month after, I finally received the **physical certificate**

![Cert](https://raw.githubusercontent.com/lazarpostenje/lazarpostenje.github.io/master/assets/cert.jpg#center)

## Thank you!

I want to thank all the people I mentioned above! Without them and their content creation, this wouldn't be possible! Now going for **OSWE** :)