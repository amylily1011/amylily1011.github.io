+++
title = 'From Syntax to Semantics: Prototyping a Natural Language Interface for Secure Infrastructure Search'
date = 2025-06-12T17:15:51+01:00
draft = false
type = "projects"
+++

**Context:**

The default search relied on learning a custom DSL. This is not efficient, intimidating and error-prone for both expert users and new users. The translation step is where all users made the most mistakes. Once the correct query is constructed, users who check in the next day tends to forget how they constructed the query. 
***
**Challenge:**

How might we remove the manual translation step without compromising on precision?

***

**My Role:**

UX lead, collaborating with engineering and PM. I conducted user interviews, mapped pain points, and prototyped a natural language parser layered over the DSL logic.

***

**Problems:**

In MAAS 2.9 *(June 15, 2021)*, a user with 1800+ machines in their MAAS environment experienced a 3-min wait time before they can start working with the UI. This makes the entire search experience very in efficient, error-prone and in many cases unacceptable for mission-critial workflows. It is a very common behavior in MAAS 2.9 to open multiple tabs and execute tasks separately. Making the workflow fragmented. As a result, creating a disjoined experience with the product. 

![MAAS Search](/images/maas-search/output-onlinegiftools.gif)


Back in MAAS 2.9 and below, a common behavior for users with more than 200 machines were to:
1. Open multiple tabs and execute tasks separately. 
2. Every mistake cost extra time to load, which becomes harder to scale for their private cloud.
3. Search behavior is a string search-base and done on the client side. 
4. The API returns an object that is a product of joining multiple table making the runtime quite expensive.
5. Search logic is confusing. 

Based on the search query, there is a hint of an AND operation and an OR operation, but people won't know which one that is unless they visit and learn from the documentation. Having to go through the documentation from this page creates a disconnection in the experience because they are entering a new UI where they have to relearn how to navigate the docs. 

![Search Query](/images/maas-search/search-query.png)

What a typical search journey looks like?

![Search Journey](/images/maas-search/mermaid-search-journey.png)


**Our biggest problem statements are:**
- *[Engineering]:* We need a way reduce the load time *from 2 minutes to 10 seconds for 1000 machines*, because as the data center scales the number of machines shouldn't affect the load time.
- *[UX Design]:* We need a way  to enable people to *search without learning the syntax*, because the translation step is where people make the most mistakes.


***
<h3>Iteration 1 [MAAS 3.0 - 2022] - How might we reduce the load time from 2 mins to 10 secs for 1000 machines? </h3>

The table below shows the estimated Web Socket response time in miliseconds and the total UI load time in relation to the number of machines.

![Search performance](/images/maas-search/machinelist-metrics.png)

From this graph, we notices that when our user scales their environment to more than 100 machines in their environment, the load time starts to lag. This causes the entire experience to be less motivated.

![Search performance graph](/images/maas-search/maas-performance-graph.png)

**Exploring different handlers**

As an attempt to start fixing our performance, we created a spike to test out different websocket handlers. The following graph shows the timing for getting data for *1000 machines* (the whole dataset is from a sample database) with the current implementation websocket handler for the machine listing page. 
- The execution time does not include request/response roundtrip nor JSON serialization/deserialization
- Filtering and grouping mechanisms were not taken into account in this experiment.

![SQLAlchemy](/images/maas-search/sqlalchemy.png)

After implementing the websocket handlers and tried executing with different handlers with our client side. The result we as shown on the table below.

![SQLAlchemy Table](/images/maas-search/machine-sqlalchemy-table.png)

Our initial goal:
> We want to reduce the load time from 2 mins to 10 secs / 1000 machines

Based on our implementation result:

> We reduced the load time from 2 mins to 2 secs / 1000 machines

**What did we learn?**
1. Reimplementing the listing handler exposed that sometimes the database model was incorrect and overly complex, which resulted in slow and confusing queires. 
2. Data presented on the client side were joined between multiple tables, creating one big object which makes the list API quite expensive. We need the list API to work in a collection basis, which will allow the function to render all machines in one go. 

---
<h3>Iteration 2 - How might we allow users to search for machines on MAAS easily with the new speed?</h3>

**Exploring different UI interactions**

After moving the API calls to the server side, break down the object to fit for its purpose, we started testing out the UI interaction of this search behavior. 

**Adding helping texts**
<div style="text-align:center;">
    <img src="/images/maas-search/search-legend.png" alt="legend" width="350" >
</div>

The image below shows the new version of our proposed interaction. Instead of having to learn from the docs page, the available filtering/searching elements will show up as chips sorted alphabetically. It also shows implicit logical operators such as AND, OR, and NOT in the bottom to specify it's logical order. 

![helper chip](/images/maas-search/search-helper.png)

**Keyboard interaction and free text search**

A user can also type a free text search even when elements in the chips does not exist. This allows people who are proponent to keyboard interaction to search and navigate easily with their keyboards.

![free text](/images/maas-search/free-text-search.png)

**Editing and removing chips**

A user may edit the chip by clicking on the chip component which will turn into a text format for edit mode. They can also remove the chip by clicking on the *X* icon on the chip.
![edit chip](/images/maas-search/editing-chips.png)

**Saving and sharing search**

One a search query is constructed, a user have an option to either save the search, share their search or view search queries that are shared with them. They can also retrieve the last 5 historical search queries by clicking on the clock icon. 

![save search](/images/maas-search/save-search.png)

**Testing results**

We tested this prototype out with 11 people who are:
1. Users of MAAS with at least 100 machines their environment. (2 were users with 4000 machines.)
2. People with more than one MAAS clusters.
3. People who treats MAAS machines with naming conventions (cattle).
4. People who use logical operators with their search queries in older MAAS versions.

![pie chart](/images/maas-search/pie-chart.png)

Our test evaluates users based on their feedback and observation of their behavior with a rating of 1 to 5 (highly agreed).
We evaluate the interaction based on 4 elements: 
1. *Efficiency* - are users able to complete a task without help?
2. *Learnability* - are the language and interaction easy to learn?
3. *Error tolerance* - can a user recover from their mistakes easily?
4. *Pleasantness* - is the design and interaction pleasant to use?

**Observation**
1. Most people find the new experience easy to learn and quoting `it feels more efficient`. However, while interacting with the prototype, many users struggles with the interaction `switching between keyboard and mouse`. Based on this interaction, we cannot fully conclude that the prototype is more efficient. 
2. Although many users find it `easy to learn` they still need to translate the syntax and creating a logical query which still lead them to `making many mistakes` during the trial. 
3. The `search saving functionality` was one of many users' `favorite`, 9 out of 11 users quoted how they felt this solves their problem because they never remember the syntax and having to relearn this every time. 

> The problem here is although the UI felt easier to use. *People are still making a lot of mistakes* during the syntax creation/translation process. And even with all the fancy helper chip, 7 users still go back to the doc to find reference for the syntax. 
***
<h3>Iteration 3 - How might we remove the translation step out of search?</h3>

The video below shows our third UX prototype. In this prototype, I created a quick sample database with 10 machines to mimic NLP interactions that translates human language into MAAS search DSL. 

The idea was to remove the translation step out of the search journey and allow humans to search with human language. 

Visit [![GitHub Repo](https://img.shields.io/badge/view-on%20github-blue?logo=github)](https://github.com/amylily1011/NLPsearch) to see the sample code using python steamlit to simulate the interaction. 
<video width="100%" controls>
  <source src="/images/maas-search/maas-search-demo.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

> The goal for this search interaction is to create a central search behavior so efficient that all cloud products at Canonical can reuse.

*[Latest update]* We are still testing this out with other MAAS users and other cloud product teams. 

**The goal of the experiment is to:**
1. Identify common, reusable search terms or similar intents across Canonical's infrastructure products.
2. Understand what are the trade-offs between precision and ambiguity in the NLP design?
3. Understand how users phrase queries in natual language and where misunderstandings occur.
4. Develop a scalable, human-centric search model that can be reused by other team beyond MAAS.
***

Approach:
- 🧪 Rapid prototyping in Python + Streamlit to simulate interpretation
- 🤖 Prompt behavior testing to evaluate how partial, ambiguous input should resolve (e.g., “all Ubuntu machines not running”)
- 🧭 Behavior mapping: designed feedback loops when inputs were underspecified or invalid
- 🔍 Error exploration: mapped “model mismatch” moments (e.g., plural nouns, passive voice) to fallback DSL templates

***
Outcomes:
- Reduced wait time from 2 mins to 2 secs for 1000 machines.
- Remove the translation step where people make the most mistakes in the search interaction.
- Initial behavioral testing revealed common patterns of ambiguity — used to refine parser prompts and UI hinting
