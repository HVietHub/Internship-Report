---
title: "Blog 1"
date: 2025-09-09
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Apex Legends Migrates to Amazon GameLift in Just 10 Days

Respawn Entertainment recently migrated **Apex Legends** to **Amazon GameLift** servers, completing the traffic cutover in just **10 days** with no visible downtime to players.  
A key priority for Respawn was to perform technical integration and validation without disrupting development teams or player experience. AWS met that requirement by partnering with **Code Wizards Group** and leveraging the Amazon GameLift Migration Acceleration Program to build a staged, low-risk migration plan — resulting in one of the largest AWS-led infrastructure transitions ever undertaken for a live service game.

---

## Defining Priorities

As a live service game, Apex Legends continually evolves — not only in gameplay and content but also in operational quality. With millions of players worldwide, Respawn needed infrastructure that is **observable**, **consistent**, and **flexible**. When evaluating modern hosting options with AWS and Amazon GameLift, they identified three clear goals:

1. **Expand Player Experience**  
   Infrastructure must scale to meet player demand spikes during new seasons or events.

2. **Modernize Hosting**  
   Migrate from hybrid infrastructure with bare metal servers to cloud-based hosting to support a global player base with scalable environments.

3. **Maintain Development Velocity**  
   The new solution must integrate smoothly with Respawn’s existing processes — build, test, deploy — without slowing game development.

> “Minimizing the disruption for our player population was a critical success factor for the migration. The coordinated effort between Respawn and the Amazon GameLift Servers team made this a reality.”  
> — Robert LaCruise, Principal Engineer, Online Game Services, Apex Legends  

---

## Planning the Migration

To ensure Amazon GameLift met these needs, Respawn collaborated with AWS and Code Wizards Group — a long-standing industry partner — to conduct a detailed evaluation. Amazon GameLift already powers leading titles such as *Dead by Daylight*, *Mortal Kombat 1*, and *Marvel SNAP*, and can scale to **100 million concurrent players**.  

Together, the teams identified three technical proofs of concept:

1. **Main Flow**: Ensure matchmaking and server placement run smoothly for all players  
2. **Zero Downtime Updates**: Ability to push updates without disrupting player sessions  
3. **Metrics & Observability**: Collect real-time data to monitor and improve player experience  

> “We believe their (AWS) services offer the best overall player facing performance when compared to other alternatives. Additionally, AWS servers are standard for many other FPS titles and we believe our players will see the benefits of this migration globally.”  
> — Robert LaCruise, Principal Engineer, Online Game Services, Apex Legends  

---

## Proof-of-Concept Implementation

To validate the migration strategy, the teams built and tested several new components.

### Seamless Matchmaking

The first step was creating a **player redirect layer** to route players to Amazon GameLift servers without modifying backend logic, avoiding risky code changes or lengthy QA cycles. At the same time, a **Game Server Wrapper** was built to use the current game server build unchanged while adding logging and observability. Both components, developed by Code Wizards Group, were key to a smooth migration:

> “We knew that player experience was super important to Respawn and their players. We created the Wrapper and Portal to enable this. The end result was no downtime to the player experience.”  
> — Stuart Muckley, CEO & Founder, Code Wizards Group  

### Zero Downtime Updates

Code Wizards also developed a system to automatically move players to updated servers after matches — enabling new builds to be deployed without interrupting live gameplay:

> “Player-facing goals were formalized into our architecture designs… ensuring that zero downtime deployments were a reality from start to finish.”  
> — Jared Cugno, Head of Engineering, Apex Legends  

### Real-Time Monitoring

Monitoring and observability services gave Respawn insight into session health, latency, and server performance — helping identify and address issues quickly:

> “We are leveraging the global infrastructure of AWS to continuously optimize server and network performance. We have more options and flexibility on the new infrastructure platform.”  
> — Robert LaCruise, Principal Engineer, Online Game Services, Apex Legends  

After validation, these systems went through production-grade testing, benchmarking, and performance tuning.

At the backend layer, Respawn now enjoys:

- AWS’s global low-latency infrastructure  
- AWS debugging and server tuning tools  
- Improved cost efficiency via instance choice  
- Preservation of existing dev pipelines through wrapper and redirect layer  
- Greater hosting control via Amazon GameLift APIs such as **TerminationGameSession** and **queue placement override**, integrated with wrapper & redirect  

Respawn didn’t need to slow development or alter pipelines — instead, they gained stronger tools without sacrificing speed.

---

## Migrating in 10 Days

Following evaluation, Respawn executed a **region-by-region cutover** to Amazon GameLift Servers — starting with smaller regions to minimize player impact and build confidence. AWS provided hands-on technical support, real-time observability tools, and pre-tested rollback systems to guarantee stability.  

Before cutover, teams defined rollback thresholds, validated alerting pipelines, and ran migration simulations to ensure readiness. During migration, key metrics were continuously tracked:

- Player connection stability  
- Latency and jitter  
- Queue wait times  
- Match placement success rate  

With automation and real-time alerts, teams were ready to roll back instantly if needed. But the migration went smoothly — with **no visible downtime to players** throughout the 10-day process.  

Respawn shared early results with the community and continues to monitor performance and optimize experience.

---

## Conclusion

The migration of **Apex Legends** to Amazon GameLift demonstrates what’s possible when modernization is player-focused.  
With support from AWS and Code Wizards Group, Respawn executed a high-stakes migration in record time — without disrupting gameplay or development pipelines.

> “The migration team were put through their paces several times… They did it well, and I’m looking forward to seeing what the future holds.”  
> — Jared Cugno, Head of Engineering, Apex Legends  
