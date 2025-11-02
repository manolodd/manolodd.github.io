---
layout: post
title: OpenSimMPLS simulator
subtitle: A java-based, standalone, network simulator
cover-img: /assets/img/posts/destacada-proyecto-opensimmpls.png
thumbnail-img: /assets/img/posts/opensimmpls-logo.jpg
share-img: /assets/img/path.jpg
tags: [projects]
author: Manuel Domínguez-Dorado
---

Very occasionally, there is a situation where one develops something thinking it will be a “quiet” project, and ends up realizing that the project has unexpected acceptance. That happened to me with the OpenSimMPLS simulator; I initially developed this simulator in the context of my final degree project (FDP) in 2004. The FDP itself consisted of designing a series of technologies that allowed the recovery of lost packets in an MPLS (Multi Protocol Label Switching) network locally, avoiding end-to-end retransmission of complete TCP (Transmission Control Protocol) segments and, ultimately, allowing certain levels of Guarantee of Service (GoS) to network traffic. So, for months I did protocol reengineering, algorithm and buffer design, etc. A rather thorough job. The project was called “Support of Guarantee of Service (GoS) over MPLS through Active Techniques.” OpenSimMPLS was an accessory development, necessary to verify that the designed technologies represented an objective and empirical advancement; and although I developed it with the greatest possible care, I did not think it would have much impact beyond the context of the project. Still, I released it under GPL 2.0+ in case it could be useful to someone.

![OpenSimMPLS packet retrievals](/assets/img/posts/opensimmpls-retrievals.jpg){: .mx-auto.d-block :}

## Expansion

The technical ideas of my FDP have been used in various universities and in some more developed proposals in master’s theses, doctoral theses, and even in private company projects. But what really “succeeded” was OpenSimMPLS, the simulator itself. About two years after finishing it, the number of downloads began to increase exponentially. That’s the time it takes for journal and conference publications to “take effect” and reach the scientific community. It started being downloaded from all continents and in about five years, in 2010–2011, it was already being used in more than 130 countries. That number surprised me, and even more when I learned that the UN recognizes “only” 194 countries worldwide. Wow! Almost 70% of all countries in the world. I started receiving inquiries from many research centers, universities, and companies that were using it, from the U.S.A. to Saudi Arabia or Japan. And even an organization from the United States nominated the project as the best project for engineering education.

The most surprising thing is that I created OpenSimMPLS to demonstrate that the improvements I proposed for an MPLS domain represented a real advancement over “standard” MPLS. And without realizing it, what I had made was a fairly complete MPLS simulator. The extensions created in the context of my FDP could be used or not, but as an MPLS simulator, OpenSimMPLS has enormous advantages for teaching. And that’s mainly where it was being used: in MPLS network teaching in universities around the world. Also in R&D, although this was more secondary.

![OpenSimMPLS packet retrievals](/assets/img/posts/opensimmpls-repo-git.png){: .mx-auto.d-block :}

## Problems to Grow

OpenSimMPLS went through a rigorous quality process during its development, mainly because it was going to be used to validate R&D results and therefore the simulator had to avoid being the weak point of the process. And in fact, few bugs have been reported in its history (let’s not kid ourselves, there have been some, but very few). Most of the inquiries from different places throughout its history have been related to:

*   Ease of extending MPLS node functionalities. Certainly, the simulator was not especially designed for this; it was developed for a specific time and goal. People asked for something more modular, like plugins.
*   Documentation in English. Big mistake. Since I developed it without thinking it would have such reach, classes, methods, attributes, internal and external documentation… were in Spanish. Quite well documented, yes, but in Spanish. At least the source code is internationalized and the simulator appears in Spanish or English depending on where it is executed. But of course, extending code written in a language you don’t know is complicated.
*   The project uses features that over time Java has marked as deprecated, and many people comment on the possibility of updating the simulator to use features of more modern Java versions.
*   The project was on SourceForge using a CVS (Concurrent Version System) repository. In 2004 this was not unusual, but later Subversion first and Git later replaced CVS, which has almost disappeared. Well, developers from everywhere asked for the repository to be changed.
*   Licensing problems. Other developers wanted to expand the simulator but wanted to use components they already had with other licenses incompatible with the GPL license used by OpenSimMPLS. I wouldn’t have paid attention to these requests if they hadn’t been repeated incessantly and recurrently from many places around the world.
*   There was no community. Certainly not. I didn’t think the project would have such significance and didn’t worry about creating one until I realized it would have been a good idea… but it was already too late.

So, most of the project’s problems were not so much due to increased use, but due to the passage of time. It needed to be “refreshed.” And I, who had finished the project more than a decade ago, found myself spending more and more “moments” each week answering questions, modifying functions, etc. Something had to change, fundamentally.

## Project Relaunch

Most of the things people were asking for here and there were more or less simple. Just a matter of spending time. So I launched a modernization plan for OpenSimMPLS to keep it fresh and current and to facilitate its extension in the medium term:

*   Change from the original CVS repository on SourceForge to a new Git repository on GitHub.
*   Change of license. At that time it was GPL 3.0+, and I leaned toward a more permissive and commercial software-friendly license: Apache Software License 2.0 (it was necessary to review contributions and third-party libraries to see if it was feasible).
*   Update of the user interface, with a new, more modern look and feel.
*   Centralization of everything on GitHub, including the old website, which would move to the GitHub Wiki.
*   Refactoring of the source code to change the names of variables, methods, classes, attributes, interfaces… to English.
*   Translation/refactoring of all internal project documentation to English.
*   Identification, in the code, of places where modifications are needed to correct some aspect or update for higher versions of Java.
*   Creation of a communication channel for questions or to share simulator knowledge:
    *   Email: <opensimmpls@manolodominguez.com>
    *   Twitter account: @opensimmpls

![OpenSimMPLS web](/assets/img/posts/opensimmpls-web.png){: .mx-auto.d-block :}

Not all measures are finished yet. In fact, some haven’t even started. But a large part is. Especially those that most prevent the project from being extended by third parties: translation to English and code refactoring. That task is quite advanced. As is the repository and license change. With this, I hope this project lives at least another 10 years, either as it is or as a fork that evolves in parallel. There are already forks of the project on GitHub that haven’t been able to make pull requests since I’m in the middle of executing the described tasks 😟 Sorry! In any case, project downloads are more or less constant at about 1,000 per month, since the releases are intact and ready to use.

So this is the OpenSimMPLS project. If you want to contribute, use it, learn more, or share experiences, don’t hesitate to get in touch through any of the means described.






