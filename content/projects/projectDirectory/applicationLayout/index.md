+++
title = 'Redesigning MAAS: Scalable Infrastructure UI That Inspired a Design System Shift'
date = 2023-02-01T14:53:41Z
type = "projects"
draft = false
+++

**Context:**
We want to redesign MAAS UI to fit in with the modern world cloud-like experience and align designs across products.
***
**Challenge:**
We need a way to create a functional design that can be applied across products under the Cloud and Infrastructure division.
***

**My Role:**
UX lead, collaborating with engineering and PM. I conducted user interviews with 5 internal users and 1 external user. 

***
<div style="display: flex; flex-wrap: wrap; gap: 2rem; align-items: flex-start; margin-bottom: 2rem;">
<!-- Column 1: Image -->
    <div style="flex: 1; max-width: 45%;">
        <strong>Timeline:</strong>  
We spent a total of 6 weeks on this project to spin up a prototype of the new MAAS UI.

We were fortunate enough to have an external user signed up to test out the new UI with us along with the 5 initial internal testers.

In this experiment, we went through 6 design iterations before the final version was published. After the final version was shared with the design team, other teams were keen to pivot into the new layout. As a result, the application layout project became part of our design system.
    </div>

  <!-- Column 2: Text -->
  <div style="flex: 2; max-width: 45%;">
    <img src="/images/maas-application-layout/timeline.png" alt="MAAS Application Layout Timeline" style="width:100%; max-width: 100%; height: auto; border-radius: 6px;" />
</div>
</div>

***

**Users:**
During my on-boarding week, I went through past research videos from the previous experiment where the team was testing out MAAS with 11 internal developers and Field Engineers who are working to support MAAS clients. With this leading insights I've created a survey to reach out to external users of MAAS, map the data to our Grafana Dashboard, and categorized our users into groups.

The graph below shows categories of users of MAAS and their purpose of using MAAS. 

![maas user groups](/images/maas-application-layout/usergroups.png)

We found that 35.5% of our users are using MAAS for production purposes in their private clouds. The majority of our users (64.6%) are using MAAS for lab purposes such as creating a mini homelab with a MAAS cluster. There is also an overlap of 18.2% where companies use MAAS to support both lab and production environment. The insights on the survery also aligns with our gathered data from the Grafana Dashboard as well.  

***
**Problems:** 
Based on the input data we received, I have observed that our UI was not easily learnable, not efficient, and quite error tolerant. The three aspects below are factors that contributes to problems that persists in the experience of MAAS. 

This redesign focuses on 3 aspects of the current (MAAS 2.8 - year 2020) UI:
1. Navigation and information architecture
2. Error-prone action button
3. How to make MAAS process and events feel more real-time in the UI?

**What's wrong with MAAS navigation and the information architecture?**

The picture below shows the old navigation of MAAS in version 2.8 (2020).
![maas old nav](/images/maas-application-layout/old-nav.png)

Despite having a clean top bar navigation, the menu items become harder to scale in a semantic way. This creates a cluttering problem in terms of categories. Once clicked on a menu item, there are another layer of nested menu under the main menu. As a result, the navigation experience is difficult, inefficient, and very error prone based on the users' feedback. 

*Past video interviews have also shown that 9 out of 10 testers have clicked on the wrong menu when they needed to check the logs.*

From the card sorting experiment, it has become apparent to us that users struggle most in the category of networking. Which the cluster of smartNICs related topics show moderate to high simliarity (55-88). Topics related to machine topology relating to networking is quite ambiguous.
![MAAS cardsort](/images/maas-application-layout/card-sorting-1.png)

Concepts like fabric, DHCP, VLAN and subnets in correlation with controllers and other hardware are not as clear compared to the other topics. 

![MAAS cardsort](/images/maas-application-layout/card-sorting-2.png)

From this experiment coupled with our past testing results, we can conclude that the current navigation and its information architecture is still confusing. 

**Why is the ACTION button a problem?**

The image below shows the old version of MAAS UI. In the old version, all actions were cluttered under the green action button. Since *machines* are the main part of MAAS, the interaction point to all machines with the actions are quite far. This is an inefficient experience. Considering that every time a user clicks on the wrong item on the list, they will need to redo the experience again, making this very error prone and unpleasant to our users.  
![MAAS action](/images/maas-application-layout/action-button.png)

On top of these two main problems, MAAS UI doesn't surface data that reflect reality. Many users needed to rely on the CLI to figure the current status of loading processes or things that are in waiting state.

One of our external users quoted that:
> I only use the UI for bulk actions because I cannot do that in the CLI, but the CLI is more reliable. 

About 30% of our user based are recurring users of MAAS and started using MAAS since there were no UI. The CLI was originally design for machines to talk to machines, so most provisioning actions were done through a script via Foundations Cloud Engine (FCE) to on-board a new private cloud. A usual behavior is that users will rely on the CLI and only use the UI on redundant tasks. This creates a layer of complexity which made the entire experience very confusing, hardly learnable, and inefficient. 

***
<H3> 🎉 Five iterations of prototyping and testing</H3>

In the first prototype, we broke down our assessment based on 5 components: the quick access machine list, status bar, action bundle, and side navigation. Our evaluation is based on 2 major heuristics, which are ease-of-use and pleasantness. 

There are 3 aspects of ease-of-use that we address in our testing sessions: learnability, efficiency, and error tolerance. 

We tested out our prototypes with 6 participants using rapid prototyping methodologies and quickly making changes with given feedback as we go. 

✅ **What went well?:**

**Quick access machine list**
- 5/6 users love the fact that they can switch between machines to see the details.
- Most people find collapsing the quick list very convenient but they might use it all the time.
- Reducing the amount of information in this list made it cleaner and easier to distinguish that this is a list of machines.

**Status bar**
- Surfacing background tasks such as commissioning, deploying, or acquiring is a good addition to the new MAAS layout because it allows our users to be aware of this information while working on the other tasks in MAAS.

**Action bundle**
- 5 out of 6 users find the broken down action button to be very convenient as it reduces the click complexity in the UI. 

**Side Navigation** 
- Not having to gaze from left to right feels very pleasant for all of our users because they can just look at one side and see the list of menus. 

⚠️ **What can we improve?:**

**Quick access machine list**
- A few people find the example in the machine name Chimp.maas much like a list of historical logs of a machine. It took them a few seconds to realize that these are actually just a list of machines. 
- Grouping machine status by default can be counter productive if you are running a commissioning action in the background and one machine jumps from one list to another. This can be error prone for users to click on the wrong item in the list.
- Performance plays a big part in this concept since if we show the entire list of machines (1800+) it can take 3 minutes to load each time. 


**Action bundle**
- It will be more helpful if the primary (first action in the action bundle) is contextual to the machine status. For instance, a machine in a deployed state shouldn’t be encouraged to commission. - It should be more focused on actions such as released or locked. 
		
**Status bar**
- The information about error in the status is out of sight, BMC and any error or bug level severity should be reported or announced in a manner that it grabs attention. When it is located in this status bar, users barely know that it’s there. 
- When running more than 1 action such as deploy and commissioning to multiple machines can be difficult to show. We need a way to show that efficiently. 


❓ **Sidenote:**

**Information Architecture**
- We’ve learnt that the concept of `spaces` belongs to the networking tab and shouldn’t be exposed in the parental navigation, because the concept of spaces is not utilized enough in the context of MAAS except defining the relationship within the `subnet` or `fabric`.
- The concept of storage is still fuzzy and needs more definition, however, since the goal for this tab is to set up for the storage template and networking template work, we will need to use a different vocabulary to communicate this.


![MAAS research summary](/images/maas-application-layout/research-summary.png)

---

**<H3>Results in Details:</H3>**

**<H4>Status bar</H4>**

Below is the prototype that shows the result after commissioning failed.

![MAAS research summary](/images/maas-application-layout/commissioning-fail.png)
> “The bottom bug feels out of sight, very easy to not see.”  
---David A. (MAAS external user with 1800 machines)

The user was able to quickly recognize that commissioning has failed, because it is placed in the rightmost bottom. Although we highlighted the error message in red, the location of this red alert is outside of the F pattern, where our visual cognitive looks for. **We need a way to surface this information more efficiently.** 

Another problem with the status bar is that it surfaces running tasks while a user navigates to a different page. However, the example above shows the information efficiently when there is only one task. **How might we show multiple running tasks in this space efficiently?**

We discovered that non-noisy local information such as last commissioned and deployed timestamp is useful for our users, but surfacing that information might be confusing when the intention of the status bar is to show the global information. **How might we show other useful global information in this status bar? And how might we show other useful information that are machine specific efficiently?**

.
.
.
**<H4>Action bundle</H4>**

Below is the example of how we solve the problem.

![MAAS action before and after](/images/maas-application-layout/before-after.png)

In the previous version, there is only one button that expands into multiple actions after clicking. 

![MAAS action bundle](/images/maas-application-layout/action-bundle.png)

The action bundle is located in a close proximity to the interaction now, but the question becomes what actions would be visible when multiple machines from different status are selected.

>“How will this button look like when I select ready machines and failed machines at the same time” - Stephane (LXD)

![MAAS bundle proposal](/images/maas-application-layout/action-bundle-proposal.png)

**How might we show these action bundles efficiently  in the Machine list page and Machine details page?**

Based on our research, we have discovered that at least 4 out of six users recall that there is an inline action in the machine listing page, however none of them actually uses them and don’t remember using them. They claimed that most times they interact with bulk actions rather than individual actions and so it is not the most obvious thing to do. However, to make this experience more foolproof we still need the inline action to be easier to discover.

**How might we display the inline actions in the machine listing page efficiently?**

.
.
.

**<H4>Quick-access list</H4>**

![MAAS quick access list](/images/maas-application-layout/quick-access-list-2.png)

This is the 5th version of the quick access list that we tested. The nice thing about this list is that it shows a list of machines that you can quickly switch back and forth. However, in an environment where we list out 100 or 1000+ machines in this list, the list would not be as useful as we wanted. We’ve learnt that most users use MAAS as a debugging tool and they look at failed machines first. 

**How might we only surface default machines that are actually helpful in this list without showing all machines and compromising the performance of listing UI?**

.
.
.

**<H4>Next steps</H4>**
Status bar
How might we show multiple running tasks in this space efficiently?
How might we surface this information more efficiently?
How might we show other useful global information in this status bar? And how might we show other useful information that are machine specific efficiently?
Should we consider adding the notification center in this status bar?

**Action bundle**

How might we show these action bundles efficiently  in the Machine list page and Machine details page?
How might we display the inline actions in the machine listing page efficiently? 

**Quick access list /Machine listing in general**

How might we only surface the default for machines that are actually helpful in this list without showing all machines and compromising the performance of listing UI?

***

**<H3>Sneakpeek: Final prototype</H3>**

<iframe style="border: none;" width="800" height="450" src="https://embed.figma.com/proto/JRD38DvBBt6VMdqbW97vqO/Untitled?scaling=scale-down&content-scaling=fixed&page-id=0%3A1&node-id=1-4809&starting-point-node-id=1%3A9124&embed-host=share" allowfullscreen></iframe>

**Task one: Commissioning flow**

1.) Click commissioning
  - What did you observe?
  - How do you understand what was going on?
  - Tell me more about what the error message mean.
2.) Try expanding and collapsing the navigation
  - What was your impression on these 2 different views?

**Task two: Quick access list**

Let's look into fast-flea, now compare between fast-flea and chimp.
  - Can you tell the difference between these 2?
  - Compare to the previous MAAS experience, what do you think about this version? (why?)
  - What makes it easier to harder than the previous experience? 

**Extra questions: Status bar**
  - How do you feel about the information below the page ?
  - Do you find any of this information useful?
  - What do you care most about in your MAAS environment ?
  - What do you think about the relative time, is it helpful ?
  - What do you think about the new placement of the action bundle?
  - Can you describe how this impacts the way you work with these machines?
  - From a scale of 1 -10 how would rate this new layout in terms of convenience? Why?
  - What’s the single best feature in this UI?
  - If there is one thing you would change, what would it be?

***

**<H3>Learnings:</H3>**

**Users open multiple tabs**
Our research has revealed use cases where at least 4 users open up multiple machine details tabs while working with MAAS. The reason is because:

-  **Performance:** When a user with 1000+ machines opens up the machine listing page, the UI needs to load the entire machine list first (3 mins) in order for our users to be able to do anything else. So they open up a machine details page in a new tab while waiting for all the machines to load.

- **Hardware Comparison:** one of the most common tasks for MAAS users is to compare HW information and configuration information of their machines. Because they want to make sure that it has the same settings when they find a bug. 

**Search**

The ability to do advanced search or with regular expression would be very helpful to monitor the list of machines
- For instance: Give me all machines with more than 4 cores AND 512GB
- Give me all the machines that have `-bjxkd-` somewhere in the name AND they must be in commissioning state.  

A few users also mentioned that it would be really helpful to have a dropdown of historical search to help them to do the same search. Sometimes they have the right search set up and forget how that was filtered.

***
**<H3>Future work and next steps:</H3>**

1) Advance search ([NLP Search]({{< relref "Projects/projectDirectory/maasSearch.md" >}}))
2) Notification center (global and local)
3) Showing local error messages
4) Machine profile templating (storage and networking)
