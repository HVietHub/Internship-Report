---
title: "Blog 1"
date: 2025-09-09
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Apex Legends Migrates to Amazon GameLift Servers in Just 10 Days

By Michael Jackson, David Holladay, and Juho Jantunen on 05/12/2025 in Amazon GameLift, Game Development, Games, Industries

Respawn Entertainment recently migrated Apex Legends to Amazon GameLift servers, completing the traffic cutover in just 10 days with no downtime to players.

A core priority for Respawn was to perform technical integration and validation without disrupting development teams or the Apex player base. AWS met that need by partnering with AWS Partner Code Wizards Group and leveraging the Amazon GameLift Migration Acceleration Program to deliver a staged, low-risk, comprehensive migration plan — resulting in one of the largest AWS-led infrastructure transitions ever undertaken for a live service game.

![Apex Legends GameLift Migration](../../images/3-BlogsTranslated/blog1-image-1.png)

---

## Setting Priorities

As a live service game, Apex Legends continually evolves — not only in gameplay and content but also in operational quality. With millions of players worldwide, Respawn needed more observability, consistency, and flexibility in their infrastructure. When exploring modern hosting options with AWS and Amazon GameLift, they set three clear goals:

**Expand Player Experience**  
Infrastructure needed to scale to meet player demand spikes during new seasons and events.

**Modernize Hosting**  
Moving from hybrid infrastructure with bare metal to cloud-based would deliver scalable gameplay environments for the global player community.

**Maintain Development Velocity**  
The new solution had to integrate smoothly with Respawn's existing development, testing, and deployment workflows — without slowing progress.

> "Minimizing the disruption for our player population was a critical success factor for the migration. The coordinated effort between Respawn and the Amazon GameLift Servers team made this a reality."  
> — Robert LaCruise, Principal Engineer, Online Game Services, Apex Legends

---

## Planning the Migration

To ensure Amazon GameLift Servers could meet these requirements, Respawn partnered with AWS and Code Wizards Group — a long-standing migration partner in the gaming industry — to conduct a detailed assessment.

Amazon GameLift brings experience from powering popular games like Dead by Daylight, Mortal Kombat 1, and Marvel SNAP, with the ability to scale to 100 million concurrent players.

The teams identified 3 key technical proof-of-concepts:

**Main Flow**: Smooth matchmaking and server placement for all players.

**Zero Downtime Updates**: Ability to deploy updates without disruption.

**Metrics & Observability**: Real-time data to monitor and improve player experience.

> "We believe their (AWS) services offer the best overall player facing performance when compared to other alternatives. Additionally, AWS servers are standard for many other FPS titles and we believe our players will see the benefits of this migration globally."  
> — Robert LaCruise, Principal Engineer, Online Game Services, Apex Legends

---

## Proof-of-Concept Testing

To validate the migration strategy, the teams built and tested multiple new technologies.

### Seamless Matchmaking

The first step was building a "player redirect layer" that could route players to Amazon GameLift Servers without changing the game's backend logic, avoiding risky changes and unnecessary QA cycles.

Additionally, a Game Server Wrapper was created to leverage the existing game server build without modification while adding observability and logging. Both were developed by Code Wizards Group and were key to the migration.

> "We knew that player experience was super important to Respawn and their players. We created the Wrapper and Portal to enable this. The end result was no downtime to the player experience."  
> — Stuart Muckley, CEO & Founder, Code Wizards Group

### Zero Downtime Updates

Code Wizards developed a system to automatically move players to upgraded servers after matches end — allowing updates to be deployed without disrupting gameplay.

> "Player-facing goals were formalized into our architecture designs… ensuring that zero downtime deployments were a reality from start to finish."  
> — Jared Cugno, Head of Engineering, Apex Legends

### Real-Time Monitoring

Monitoring and observability services gave Respawn clear visibility into session health, latency, and server performance — enabling timely detection and resolution of issues.

> "We are leveraging the global infrastructure of AWS to continuously optimize server and network performance. We have more options and flexibility on the new infrastructure platform."  
> — Robert LaCruise, Principal Engineer, Online Game Services, Apex Legends

### Backend Benefits

After testing and optimization, Respawn now benefits from:

- AWS's global, low-latency network
- AWS tools for debugging and server tuning
- Cost optimization with multiple compute instance choices
- Preserved existing development pipeline through wrapper and redirect layer
- More control via Amazon GameLift Servers APIs like TerminationGameSession and queue placement override

This means Respawn didn't need to slow development or change pipelines but still got better tools — without sacrificing speed.

![Portal Translation Layer](../../images/3-BlogsTranslated/blog1-image-2.png)

*Figure 1: The "Portal" translation layer redirects traffic from existing backend to Amazon GameLift Servers.*

---

## The 10-Day Migration

After the assessment phase, Respawn proceeded with region-by-region traffic cutover, starting with the smallest regions to minimize impact and build confidence gradually.

AWS provided:

- Direct technical support
- Real-time monitoring tools
- Pre-tested rollback systems

Before cutover, teams:

- Defined rollback thresholds
- Validated alerting pipelines
- Ran simulated migration tests

During migration, they continuously monitored:

- Player connections
- Latency and jitter
- Queue wait times
- Match placement success rates

Thanks to automation and real-time alerts, teams were ready to rollback if needed. But in reality, the process went smoothly with no downtime throughout the 10 days.

Respawn shared initial results in last month's update with the community and continues to monitor and optimize player experience.

---

## Conclusion

The migration of Apex Legends to Amazon GameLift Servers demonstrates what's achievable when infrastructure modernization is player-centered.

Backed by AWS and Code Wizards Group, Respawn executed a large-scale migration in record time without disrupting gameplay or development pipelines.

> "The migration team were put through their paces several times… They did it well, and I'm looking forward to seeing what the future holds."  
> — Jared Cugno, Head of Engineering, Apex Legends

---

## Authors

![Michael Jackson](../../images/3-BlogsTranslated/blog1-author-1.png)

**Michael Jackson:**  
Michael Jackson is the Global Business Development Director for Amazon GameLift Services, focused on game server and game streaming solutions at Amazon. He has experience in managed hosting and cloud technology designed specifically for the gaming industry. Michael has helped some of the world's most creative studios modernize infrastructure, reduce operational risk, and deploy at global scale.

![David Holladay](../../images/3-BlogsTranslated/blog1-author-2.png)

**David Holladay:**  
David Holladay is the Head of Game Product Marketing at AWS.

![Juho Jantunen](../../images/3-BlogsTranslated/blog1-author-3.png)

**Juho Jantunen:**  
Juho Jantunen is a Global Principal Solutions Architect on the AWS for Games team, specializing in game server hosting and backend solutions. He has experience in the gaming industry and cloud technology, and has built and operated game backends on AWS for multiple titles with millions of players.
