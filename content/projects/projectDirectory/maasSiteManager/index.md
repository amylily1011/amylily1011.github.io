+++
title = 'MAAS Cluster Manager from Concept to Product (Serving 60K MEC Sites)'
date = 2022-03-12T17:15:51+01:00
draft = false
type = "projects"
+++

**Context:**
MAAS is Canonical’s bare metal provisioning tool used by enterprise and telco clients to manage thousands of machines. As MAAS scaled to over 60,000 edge sites across multi-region infrastructures, users struggled with fragmented deployments, image management, unreliable observability, and inconsistent management experiences. I led the design of MAAS Site Manager to solve this.

***

**Challenge:**

How might we empower organizations managing thousands of MAAS instances to monitor, troubleshoot, and control their infrastructure from a central UI without compromising scalability or security?

***

**My Role:**

Lead UX Designer collaborating with Product, Engineering, Directors and VPs and Field Engineering teams.

- Led research with 7 enterprise users across telecom, cloud, and retail sectors

- Synthesized findings into scalable platform requirements

- Mapped roles, RBAC, observability, and image/network automation flows

- Prototyped dashboard and designed feature modules for 60K+ edge site support

***

**Timeline & Impact:**

This 6-week research and prototyping cycle laid the foundation for the future of centralized infrastructure control within Canonical. The work influenced the creation of Cluster Management and introduced concepts and design patterns for the Design System across Cloud & Infra teams.

***

**User Research:**

I conducted two rounds of empathy interviews with 7 companies:

- Telco (BT), VMware, AMD, Square (Block), Home Depot, Tata, and Canonical Bootstack team

- Identified 3 core infrastructure archetypes: MEC edge clusters (Telco), cloud-regions via AWS (Square), and traditional Region+Rack setups (AMD)

Extracted common pains: image duplication, configuration drift, role delegation, delayed observability, and unscalable workflows

> One telco user had 60,000 micro-MAAS instances (1 per RAN site), each with 10–15 machines. They lacked central visibility and had no way to understand which site is down or push critical updates at scale.

***
**Let's look at different use cases:**

<h3>Using Amazon AWS as a Region Controller</h3>

This use case is a from 2 of our external clients where they remove the need to manage Postgres themselves and create a semi-centralized MAAS setup by installing region controllers on Amazon AWS.

![Amazon RDS](/images/maas-site-manager/amazon2.png)

In this scenario, MAAS instances are distributed by multiple geographical locations around the world. MAAS instances act as a PoP (Point of Presence) for all data centers. In the more extreme cases, like this example, there can be 1000+ machines per Data Center. In total, they are managing around 10,000+ machines per PoP. 

> One of the biggest pain points here is Image Management, especially, custom images. 

![Amazon RDS](/images/maas-site-manager/amazon.png)

The current way to mange images require you to upload images to the region, then the images will get served to RDS. Then there is a synchronization process between MAAS and RDS, so all rack controllers in the PoP will have to download all images back from that database. As described in the image above, the latency to upload an image is ~16 mins locally and ~127 mins to RDS, plus pushing back down to the rack. 

Going to the next PoP means the user needs to go through the exact same step.

**There are 2 Problems here:**
1. Dealing with images is manual and redundant
2. There is a lot of traffice of the same data being stored multiple times in the Database
    - Database cost in the cloud is expensive, you have to either go into each MAAS instance's table and clean the images or pay a really expensive price. 

.
.
.

<h3>Using MAAS to manage high scale MEC sites</h3>

This is a common scenario amongst our Telco use cases. The Telco could use case is interesting because there are different purposes for Bare Metal that we have identified.
![Telco](/images/maas-site-manager/MEC.png)

The first part is the mini sites called MEC (Multi-access Edge Computing) and RAN, where they install cell towers to the base stations. There are between 5-15 machines per site and one site respresents 1 MAAS instance. In this setup (for a particular external customer), they anticipated growing this mini-sites to around 60K sites all over the UK. 

The second part is their core network, where they need this to run their services. In this example, there are between 100-300 machines per Core Site. A single Core Site could be governed by 1-2 MAAS instances. There are 25 core sites in total.

> Given the current scale image management is still a problem, but instead of dealing with 10 MAAS instances, we're talking about 25 core cloud sites + 60K mini sites. 

Working with images in this case is quite difficult. 

Aside image management, Telco users really cares about **operation, monitoring, and troubleshooting** guidlines as they have a very strict protocol for how to fix things and keeping everything in a state where it is well-maintained. 

> This is because if there is a power outage, it would be catastrophic if our phones don't work. 

Therefore, the important value here is they need to be able to plug any kind of manager to integrate with any systems like MAAS into their NOC.

.
.
.
<h3>Using MAAS as a Region+Rack controller</h3>

The last use case is our normal case where MAAS is a combined controller (Rack+Region controller).

In the first case, the client uses MAAS to test semi-conductors by creating a lab that connects 2 MAAS intances to 4 Data Centers. (As the image below)

![AMD](/images/maas-site-manager/AMD.png)

In the second scenario, the client is a department store in the US where they provision Data Centers with MAAS to serve multiple internal teams. There are 7 MAAS instances that are connected to 2 Data Centers. Cloudbold was a tool for workload orchestration where they serve machiens for internal teams like QA teams and security teams. 

![HomeDepot](/images/maas-site-manager/HomeDepot.png)

These 2 examples are companies with fewer sites but with multiple MAAS instances. 
> **Image management** is still a problem in this scenario because both companies care a lot about image synchronization and the ability to replicate custom images. 

We didn't really learn any new problems from this scenario in particular, aside from how they setup they MAAS instances. 

*** 

**Concept Proposal**

Learning about the three scenarios above, the image below shows the concept of our first MVP. 

![Proposal](/images/maas-site-manager/proposal.png)

<h4>Image Management</h4>
The first feature we aim to prioritize is Image Management, which is the problem that users from all three scenarios really care about. **They need a way to manage their images (especially custom images) in a centralized manner.**


<h4>Dashboard</h4>
The second most important feature in our first prototype is the Dashboard. We are aware that geolocation data is important to Telco users and they need a way to identify a group of sites through the dashboard, but at this moment we don't know the exact data or components that should be in the Dashboard yet, next phase research will allow us to test out the MVP.


<h4>LMA Stack</h4>
We know from the 7 stakeholders that each company have their own LMA stack to monitor and track different things. They need a way to easily integrate with their current LMA stack so they can connect to their preferred alerting tools, monitoring tools, log management tools, and scheduling tools. 


<h4>Networking and Profiling</h4>
Network management and profile management are important aspects of this tool. Although most users didn't mention this as a problem, it is important to our implementation details. For this reason, we left this as a question mark. We think these are aspects that MAAS can benefit from if they are managed in a centralized manner. For instance, the networking setup could be managed in a centralized way such that creating a single subnet or deciding which data center they want to allocate it to can **allow 2 data centers to communicate via some routing configurations**. Secondly, profile management could be managed centrally through could-init snippets, storage profile or networking profile. 


<h4>Authentication</h4>
Similary for authentication, we think there needs to be a graceful way to handle authentication from MAAS intances to MAAS Site Manager through a shared mechanism. This is so that when a user decides to integrate their normal MAAS to MAAS Site Manager, they can bypass the manual sign-in process. 

***

**Sneak Peek: First Prototype of MVP**

This is a lightweigth prototype to show the concepts and components of MAAS Site Manager. This is not the final version, but a version to test out with our primary testing group. 

<h4>Dashboard</h4>

- The main problem that the dashboard needs to solve is how to visualize site location in a scale of at least 60K+. It needs to allow people to group MAAS instances by region, location, or organizational structure. 

- There needs to be a way to show health indicators, version consistency and alert at-a-glance.

![Dashboard](/images/maas-site-manager/Dashboard.png)

<h4>Listing View</h4>

- A user should be able to switch to a listing view and drill-down into any MAAS instance to manage machines or diagnose problems

![Listing](/images/maas-site-manager/listingView.png)

<h4>Initial Settings</h4>

- The first prototype still requires more information from test users, so at this stage based on what we can pull from MAAS API is the filtering mechanism using automatic tags. 

![Settings](/images/maas-site-manager/settings.png)

***

<div style="display: flex; flex-wrap: wrap; gap: 2rem; align-items: flex-start; margin-bottom: 2rem;">
<!-- Column 1: Text -->
    <div style="flex: 1; max-width: 45%;">
        <strong>Next Steps:</strong>  

We spent a total of 6 weeks on this project to define the first conceptual MVP and a rough prototype.

Just because this is our first proposal we don't want to stop here.
There are a few things that we want to explore after this.

- We would like to understand our internal DC Engineers: what they care about, how things are setup, and if there is a need for MAAS or MAAS Site Manager internally.
- We want to pick 2 proposed concepts from the MVP and go really deep into the conceptual design, and create a very simple first prototype to test out our concepts.
    </div>

  <!-- Column 2: Image -->
  <div style="flex: 2; max-width: 45%;">
    <img src="/images/maas-site-manager/timeline.png" alt="MAAS Application Layout Timeline" style="width:100%; max-width: 100%; height: auto; border-radius: 6px;" />
</div>
</div>

***
**Reflection & Learnings:**

- At scale, infrastructure management is about visibility and delegation. Our users didn’t want more automation — they wanted control at a glance.

- Prototypes revealed the need for progressive disclosure: too much detail overwhelmed users; too little left them unsure.

- This work taught me to translate distributed system constraints into human-friendly control models.

