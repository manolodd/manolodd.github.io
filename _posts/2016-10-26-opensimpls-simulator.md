# OpenSimMPLS Simulator

**Author: Manuel Domínguez Dorado**  
**Date: October 26, 2016**

## Introduction

Every now and then, a project you expect to be “quiet” ends up gaining unexpected traction. That’s what happened to me with the OpenSimMPLS simulator. I originally developed it as part of my final degree project (FDP) in 2004.

The FDP focused on designing technologies to recover lost packets in an MPLS (Multi Protocol Label Switching) network locally, avoiding end-to-end retransmission of entire TCP segments and enabling certain levels of **Guarantee of Service (GoS)** for network traffic. It involved months of protocol reengineering, algorithm design, buffer management, and more.

OpenSimMPLS was a supporting development, created to empirically validate the technologies I designed. Though I released it under GPL 2.0+ with care, I didn’t expect it to have much impact beyond the scope of the project.

!OpenSimMPLS Retrievals

## Expansion

The technical ideas from my FDP have been used in various universities, master's theses, doctoral dissertations, and even private company projects. But what truly succeeded was the simulator itself.

About two years after its release, downloads began to grow exponentially. By 2010–2011, OpenSimMPLS was being used in over **130 countries**—a surprising number considering the UN recognizes only 194 countries. I received inquiries from research centers, universities, and companies worldwide—from the USA to Saudi Arabia and Japan. One U.S. organization even nominated it as the best project for engineering education.

Originally, OpenSimMPLS was meant to demonstrate improvements in MPLS technology. But unintentionally, I had created a fairly complete MPLS simulator. The extensions from my FDP could be used optionally, but the simulator itself proved highly valuable for **educational purposes**, especially in teaching MPLS networking. It was also used in R\&D, though to a lesser extent.

!OpenSimMPLS Git Repository

## Challenges to Growth

Despite its success, OpenSimMPLS faced several challenges over time:

*   **Limited extensibility**: The simulator wasn’t designed to be modular or plugin-based.
*   **Documentation in Spanish**: While well-documented, everything was in Spanish, making it hard for non-Spanish speakers to contribute.
*   **Outdated Java features**: Some parts of the code used deprecated Java features.
*   **CVS repository**: Initially hosted on SourceForge using CVS, which became obsolete.
*   **License incompatibility**: Developers wanted to integrate components with licenses incompatible with GPL.
*   **Lack of community**: There was no established user or developer community.

Most issues stemmed not from increased usage but from the passage of time. The project needed a refresh. More than a decade after its creation, I found myself spending more and more time answering questions and making updates. It was time for a fundamental change.

## Project Relaunch

To modernize OpenSimMPLS and ensure its continued relevance, I initiated a relaunch plan:

*   Migrated the repository from CVS on SourceForge to GitHub.
*   Changed the license from GPL 3.0+ to the more permissive **Apache Software License 2.0**.
*   Updated the user interface with a modern look and feel.
*   Centralized all project resources on GitHub, including the old website now moved to the GitHub Wiki.
*   Refactored the source code: renamed variables, methods, classes, and interfaces to English.
*   Translated all internal documentation to English.
*   Identified areas in the code needing updates for newer Java versions.
*   Created communication channels:
    *   Email: <opensimmpls@manolodominguez.com>
    *   Twitter: @opensimmpls

!OpenSimMPLS Website

Not all changes are complete yet, but the most critical ones—like translation and code refactoring—are well underway. The repository migration and license update are also done. With these updates, I hope OpenSimMPLS continues to thrive for another decade, either in its current form or as a fork.

There are already forks on GitHub, though pull requests are on hold while I finish the transition 😟. Still, the project sees around **1,000 downloads per month**, with releases ready to use.

***

If you'd like to contribute, use the simulator, learn more, or share your experience, feel free to reach out through any of the channels mentioned above.
