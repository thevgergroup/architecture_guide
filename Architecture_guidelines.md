# The VGER Group Architecture Guide

Created: September 15, 2023 1:57 PM
Updated: September 20, 2023 3:28 PM

# Guidelines to develop an Architecture

## Table of Contents
- [The VGER Group Architecture Guide](#the-vger-group-architecture-guide)
- [Guidelines to develop an Architecture](#guidelines-to-develop-an-architecture)
  - [Table of Contents](#table-of-contents)
  - [1. Introduction](#1-introduction)
  - [2. Software Design Principles](#2-software-design-principles)
  - [Axioms](#axioms)
    - [2.1 Shoe Horns are bad](#21-shoe-horns-are-bad)
    - [2.2 Cache is an optimization, not a performance improvement.](#22-cache-is-an-optimization-not-a-performance-improvement)
    - [2.3 Scalability](#23-scalability)
    - [2.3.1 Sessions](#231-sessions)
    - [2.3.2 File system dependencies](#232-file-system-dependencies)
    - [2.3.3 Databases](#233-databases)
  - [2.4. Security](#24-security)
    - [2.4.1 Internal user access](#241-internal-user-access)
    - [2.4.2 External Users](#242-external-users)
    - [2.4.3 Data \& Infrastructure access](#243-data--infrastructure-access)
    - [2.3.4 Keys](#234-keys)
  - [3. Source Control Methodology](#3-source-control-methodology)
    - [3.1. Workflows and branching](#31-workflows-and-branching)
    - [3.2. What should you source control](#32-what-should-you-source-control)
    - [3.3. Structure](#33-structure)
    - [3.4. Documentation](#34-documentation)
    - [3.5. To code review or not to code review?](#35-to-code-review-or-not-to-code-review)
    - [3.5.1 When should you always do code reviews?](#351-when-should-you-always-do-code-reviews)
    - [3.6. API Versioning](#36-api-versioning)
    - [3.6.1 URL Versioning](#361-url-versioning)
  - [4. Data \& ML Model Version Control](#4-data--ml-model-version-control)
    - [4.1. Storage and Deployment (Registry)](#41-storage-and-deployment-registry)
    - [4.2. Versioning](#42-versioning)
    - [4.3. Reproducibility (Pipelining)](#43-reproducibility-pipelining)
    - [4.4. Parameterization (Pipelining)](#44-parameterization-pipelining)
    - [4.5. Measuring and comparisons (Tracking)](#45-measuring-and-comparisons-tracking)



## 1. Introduction

At The VGER Group one of the driving principles we have is to provide a path to deliver consistently. 

To accomplish that we look at creating an architecture plan

The goal of an architecture plan is 

1. Provide stability 
2. Provide predictability 
3. Provide solutions that are repeatable 

<aside>
⚙ An architecture isn’t a panacea, it should be thought of solving a set of problems for a period of 18 - 24 months.

</aside>

Technology is always in flux,  constantly adopting new solutions, means you are also rapidly abandoning existing solutions. 

So as of 2023 / 2024 we’re presenting the set of solutions and directions we recommend companies to adopt. 

## 2. Software Design Principles

## Axioms

### 2.1 Shoe Horns are bad

Avoid the Martin Fowlers, Kent Becks, Mike Beedle (RIP) of the world of technology, not because they are wrong, they are brilliant, but their philosophy tends to suite large organizations where methodology can outweigh delivery, and striking a balance between method and delivery is generally considered a betrayal of principles. 

### 2.2 Cache is an optimization, not a performance improvement.

Maintaining cache is a lot more complex than people realize, when and how to expire cache, how to broadcast that a certain value is updated in cache and how to reload caches without causing a meltdown from say a restart are all issues that become real very quickly.

Invest in “well performing” code, before trying to take a short cut with cache. 

If cache becomes necessary, do not invent your own.

### 2.3 Scalability

Systems should be both horizontally and vertically scalable.

There are 3 common bottle necks in stability 

1. Sessions
2. File System Dependencies 
3. Databases

### 2.3.1 Sessions

Design user facing systems with a user sessions framework, one where you can plugin a session service like memcache, redis, session-server etc.. start with a file system by all means, for single servers / low traffic systems but have the ability to configure a remote session system as you grow.  The moment you need more than 1 server, switch to remote storage backends. 

One tip is never store sessions in an RDBMs, the marshaling and unmarshaling data volume is way to large and always causes bottlenecks. 

<aside>
⚙ Stateless systems still maintain state, it just might not be in your code, but eventually it will be.

</aside>

Recommended State Frameworks

| Framework | Language | Solution | Notes |
| --- | --- | --- | --- |
| DJango | Python | django-session with redis / memcache  |  |
| Flask | Python | flask-sessions with redis / memcache / nullsession  | Avoid mongodb and sqlalchemy sessions for performance |
| Express | Node | express-sessions, redis / memcache / firebase | Firebase index performance could hinder you and quickly become expensive. |
| Tomcat | Java | RedissonSessionManager redis | Avoid Tomcat clustering, cloud networking is complex, and MCast / TCP mastering server tends to get flaky as every server is chatty. |
| .Net Core | .net | Azure cache for redis | Just avoid DistributedCache for similar reasons to Tomcat |

### 2.3.2 File system dependencies

<aside>
💡 Establish a hard rule, file system dependencies must not change outside of version control.

</aside>

Admin tools that alter configuration, templates or content are often the culprit, synchronization, rollbacks, persistent backups make file system dependencies a major single point of failure that takes down major companies for extended periods of time. 

Design with a storage system like DBMS / NoSQL that maintains versions, and metadata of changes. 

Centralized file systems shared across servers should only be used where files are maintained in local app cache with a stat to invalidate the cache periodically is run. 

Over the wire file systems have slow performance, and rely on UDP to speed up requests think of sunrpc / nfs and tends to flood a network, thus should not be used on a per-user request. 

### 2.3.3 Databases

Scale and performance is very hard to correct, getting it close to right is important at the onset and worth investment.

Establish how the database will get used

- Always design a schema for multi-tenant, but deploy for a single tenant
    - use tenant id’s in tables
    - deploy a single dedicated schema per-tenant
    - app configuration should be per-tenant
        
        ```json
        databases = { "customerA" : {"host" : "abc.db.com", "db" : "customerA", ...},
        							"customerB" : {"host" : "abc.db.com", "db" : "customerB", ...},
        							"customerC" : {"host" : "DEF.db.com", "db" : "customerC", ...},
        ```
        

Once you start building your data warehouse and lakes, this will cut down your ETL development and processing by 80%. 

This also provides you with a way to scale independently, customC needs a vertically scaled DB, you can provide that without impacting everyone else.

- OLTP vs Reporting vs Mixed
    - OLTP, online transaction processing optimized for high performance display per-user request and high write performance.
        - Data is stored in normalized tables, smaller, easier to query and write to.
    - Reporting, requires rollups and summation queries
        - Uses denormalized tables for appending results
        - Uses star like schemas
    - Mixed, for small to mid sized apps
        - Star schemas with summation queries
        - Materialized views
            - ETL and crons can fill if db lacks materialization
        - Generated & indexed date columns for weekly, monthly, quarterly, yearly and partitions
        - Performance will always be a struggle, implementing a reporting system that loads data into an OLAP will ease the pain.

- ETL & Job Processing

A key process is ETL processing, generally a large CPU and memory hog.

Evaluate data life-cycles for ETL’s 

- Delta Processing?
    - Are there overlaps?
    - Can data providers produce clean deltas?
- Is there retro / re-processing?
    - Data providers updating historical records
- Full data dumps
    - Do you require state? Added / Removed on..
- External IDs vs Internal IDs
    - Always generate internal id’s
    - Design for External IDs to change
        - Software / schema updates, provider changes, disaster recovery may effect external ID’s especially auto increments
- Use an orchestrator
    - FaaS tend to lack clear status, tracking, monitoring.
        - Think of FaaS as simple a runtime.
    - Orchestrators like Airflow, Luigi, are designed to manage jobs.
        - Some can be configured to measure load and alert with null or unusually levels of processing.
    - Even one off jobs should be done in an orchestrator
        - Ensures people know where the job is the next time they’re asked to run it
            - One off jobs tend to get run a lot
        - Ensures consistency with configuration and skills
            - One off jobs are most likely to have hard coded values and secrets
        - Ensures if software migrations occur, everything is accounted for.
            - One off jobs tend to get left behind and only discovered to no longer work when needed.

- Cross Schema processing
    - ETL’s often suffer from pulling large amounts of data into an app memory
        - Python DataFrames average 4X data to memory usage, there are ways to optimize but can be a lot of work to get to 3X.
        - SQLite / DuckDB are often used to reduce that to 2X but at a cost of speed.
        - Cross Schema ETL’s keep data processing on the DB.
            - 2 schemas a raw-ingestion and final-schema on the same database server
            - ensure same login has RWD on both DB and can select between them
        - Now simple queries between both databases can happen against indexes without unneeded data crossing over the wire.
        
        ```sql
        select * into final-schema.table 
        (
        	select *
        	from raw-ingestion.nightly as A
        				final-schema.table as B
        	where
        		A.id <> B.id 
        )
        ```
        
        In this example having indexes on the id columns will cause this to run faster than any external app loading the data and calculating it locally. 
        
        However the cost is you are doing more processing on a customer database, so use appropriately. This is not for 24/7 critical services unless you have write masters and read slaves. 
        

## 2.4. Security

There are multiple types of security to take into account here’s how we classify them

- Internal user access
- External user access
- Data & Infrastructure access
- Keys
- Software Security

There is obviously a lot more to security in terms of controls, testing and procedures - but here’s how it relates to architecture. 

Companies face larger and larger threats these days from automated criminal activities, you don’t even have to be a target, a simple DNS registration is enough to put you on an automated list. 

Employees tend to be the number one route to gaining access, every employee is a set of attack vectors, every email account, every login they have, every browser page they visit is potentially a way in. 

At the same time you cannot block productivity without alienating employees and forcing people to find work arounds. 

<aside>
🔥 Security has to be the simplest way for people to get the job done, or it fails

</aside>

### 2.4.1 Internal user access

One of the biggest burdens companies have is on-boarding and off-boarding

Providing employees or contractors with access, maintaining that access list and revoking access once they have left the company is one of the most procrastinated tasks anyone undertakes. 

All access points should use an internal SSO process, be it Google login, Microsoft Login, Okta / Auth0, Cognito etc.. They are reasonably ok to implement, and put access provisioning, on-boarding and off-boarding in one central place. 

<aside>
🔥 Google GCP has a cloud run feature that automatically provides single sign on outside of the app code base.

</aside>

<aside>
🔥 AWS Application Load balancers provide Cognito integration, providing login again outside of the code base.

</aside>

To avoid a single point of compromise, ensure all accounts have 2FA enabled. 

3rd Parties are notorious for wanting to up-charge for security and enterprise SSO, establish a hard rule of not purchasing those products. 

<aside>
📢 Never trust anyone that thinks they’re good enough to be in the password & security business, and provide you with another service.

</aside>

Feel free to send them a security assessment and demand SOC reports if they’re unwilling to give you SSO for free. 

### 2.4.2 External Users

Having customers login to your products presents you with a whole host of problems, similar to internal users are you in the business of security and passwords or do you provide a service?

- Always use 3rd party authentication
- Always have an email address
- Establish if you are B2B or B2C
    - Who owns what data when a relationship is ended?
    - Is there opportunity for a user to bring your product to their next company?
- Design with a Person and life cycle in mind
    - A person can have multiple accounts
        - Multiple Personal accounts, hidden accounts or household accounts
        - Work accounts
        - Multiple companies, or clients
- Account activity is key to security
    - Can’t login at multiple locations simultaneously etc…
    - Re-verification with email 2FA or OTP when things change
        - New browsers, no activity for a month, new location
    - Always log who, where, when, and depending on your product security needs “actions”

To build trust with your users, always tell them why you are prompting them for verification, we haven’t seen you in a while, or from this location before etc. 

### 2.4.3 Data & Infrastructure access

Consider the laptop to be an extension of your data center or cloud, except it’s in the DMZ.

Engineers tend to be a critical group who generally need access to production data and infrastructure for debugging and hot fixes. 

<aside>
🔥 Establish a hard rule, PII never leaves the data center or cloud

</aside>

Invest in faker or anonymized data generation, GAN’s like ydata and LLM’s are fantastic at creating data aka ***synthetic data generation***.

It’s faster for developers to work with, easier for them to share results and safer to work with locally. 

Recommended software

| Software | Purpose | Notes |
| --- | --- | --- |
| Faker | Generate random people, profiles, cc’s, addresses, SSN’s etc.. | multiple languages, generally MIT licensed |
| YData-synthetic | Generate tabular or time series data, pass it a sample of real data and it creates a model that generates fake data similar to the real data | Both open source and commercial  supported solutions.  |
| LLM’s, OpenAI, LLAMA2 | Generate text based data, particularly useful for conversational mocking  | Commercial and Open Source models available, data generation can be limited and requires multiple runs for volume |

- Production database Access should now be either IAM based or rotated key based.
- Backups should be minimum daily, and cross regional
    - Companies struggle with justification and expense of cross AZ systems, backups are easy and worth doing (and it ticks a lot of compliance / control boxes)
- Keep separate your environments
    - Dev, QA, should be on separate cloud accounts
    - Staging, Early Access, Prod should not cross into development
    - IAM accounts should be provisioned and automated

Spend time investing in an infrastructure orchestration platform

| Solution | Thoughts | Recommendation |
| --- | --- | --- |
| Terraform | Generally the recommended solution, however Hashicorp are going through some issues. Still worth using, nothing beats their change plan. But be aware the tech is aging, and company is struggling to keep up. They haven’t developed a community that can contribute to their code engine.  | Use |
| AWS Cloud formation | Pain, lacks a lot of features that you would be better off using boto3 and home spun that adopting. | Avoid |
| CDK (cloud development kits) | Both AWS and Hashicorp have been moving towards CDK’s however they tend to just transpile to either Cloud formation or Terraform and bring little to the table other than a common language set | Avoid |
| Spacelift IO | Fresh blood with a business model, commercial but reasonable pricing. Can handle existing Terraform, Cloud Formation code if you want to off-board existing solutions. Features like drift detection are really useful.  | Start Using |
| Pulumi | Interesting, it’s tackling CDK’s a little differently by bringing policy management to the table. Allowing you to build rules to ensure policies are being adhered to. Possible in other solutions but not first class citizens. The hurdle with Pulumi is the lack of stacks and environments making it suitable for smaller environments but not larger ones. | Investigate - will get better in time. Expect it will probably get acquired in the future.  |

Some of the biggest data breaches in the past decade have been down to misconfigurations, companies will often provide the data science or research groups their own cloud to do with as they please. 

- Researchers need support, their support is now costing double asking them to do it themselves.
- Researchers are not specialized or immersed in the field of IT or Operations

Setting up cloud accounts with policies can help

- ensure public access configurations are disabled for storage and buckets
- VPN’s are enabled for DB access even in development
    - Do not rely on IP and white list management
- Jupyter labs / Sagemakers accounts are provided
    - with IAM access to data stores
    - No passwords should be used.
- Servers are deployed as Docker and not source code
    - Provides delivery, local development, replication, inventory, and scanning
    - Removes the need for prod cli access

### 2.3.4 Keys

Basic policies also need adhering 

- App keys are stored in KMS or secrets not locally
    - Separate keys for people vs system keys
- All repos must have ignore files that filter out standard account keys like
    - AWS Credentials ~/.aws/credentials
    - Git Credentials ~/.git-credentials
    - Standard sensitive locations env, .env (note the period), settings
- Enable repo scanning for push protection [https://docs.github.com/en/code-security/secret-scanning/push-protection-for-repositories-and-organizations#enabling-secret-scanning-as-a-push-protection-for-a-repository](https://docs.github.com/en/code-security/secret-scanning/push-protection-for-repositories-and-organizations#enabling-secret-scanning-as-a-push-protection-for-a-repository)

<aside>
💡 Develop modules or libraries that perform authentication for both developers and apps.

</aside>

This helps to ensure that the prescribed login process using keys, iam, secrets etc.. is adhered to. 

## 3. Source Control Methodology

Use git, use github, regardless of preferences github has more tools, integrations and has simply dominated the scene, any other system is just not worth the lack of tooling. 

### 3.1. Workflows and branching

Source control relies on Workflows with git there are two common workflows.

Pick the simplest flow where possible, either “Github flow” or “Git flow”

- Github flow is focused on fast short releases
    - Each 1 - 2 sprints releases to production
    - Suitable for small fast changes
- Git flow is focused on feature releases
    - Longer delivery for bigger projects with more complexity
    - Features being reprioritized and delays can be absorbed

Github flow should be used at the start of all projects, as the project becomes bigger and more mature you can migrate to Git flow, when consistency and customer stability becomes the primary driver. 

It’s almost impossible to go from Git flow to Github flow.

### 3.2. What should you source control

Source control is controlling the versioning and merging of 

- software sources
- dependency versions
- configurations
- templates and static assets.
- build systems

Large files can be stored in git-lfs but there are size limits there, however website image assets do well in git-lfs. 

A couple of don’ts

- Ensure libraries are not checked in
    - Ideally use Artifactory or Nexus which can give you supply chain analysis (99% of companies don’t)
- Separate infrastructure as code (IAC / Orchestration) from application code.
- Data, for say research, however should not be stored in source control. Most source control systems were designed for text based delta management. Data tends to be on the large side and slows down repos for adds, commits, pushes and fetches.
- DO not store credentials in source control!!!

<aside>
🔥 It bears repeating use .gitignore files and repo scanning for push protection to ensure sensitive data is prevented from getting into source control.

</aside>

### 3.3. Structure

Structure is a necessity for source control, with directories for public, src, package naming, vendors, templates, static assets clearly and cleanly existing. 

There is a tendency for projects to mix technologies e.g. React with Flask 

This mixes PyPi with Webpack and node, treat these are two separate subprojects when that happens. In the example below widget_server and widget_ux are at the top level of MyWidget

```sql
MyWidget/
					.
					├── widget_server
					│   ├── app.py
					│   └── requirements.txt
					└── widget_ux
					    ├── package.json
					    └── public
					        ├── assets
					        ├── components
					        └── index.js
```

### 3.4. Documentation

Engineering documentation should be part of source control in markdown format, if it’s not manageable in the same IDE and workflow for an engineer it gets left behind. 

Having engineering documentation match branches is tremendously important and something that cannot be accomplished in Wikis or Confluences. 

<aside>
🛠 Product documentation is not the same as engineering documentation, product documentation describes how end users can interact with an interface to accomplish a task. Engineering documentation describes how to setup, run, and manipulate an interface to accomplish different results.

</aside>

Product documentation should be slower to change than engineering documentation, and should only have one live version, thus is much more suited to a wiki or confluence setup. 

### 3.5. To code review or not to code review?

This should be an easy one, but it actually isn’t.

An interesting stat came out in 2020 that engineers spend up to 2 days waiting for code reviews on average. It does not mean engineers are blocked, they can move to the next task but it does create the engineering mind killer of context switching, which will account for about 15-20% productivity loss. 

Code reviews help younger, remote and distributed teams, it helps spread code awareness and knowledge of problem solving.

A couple of hard rules that help.

- Code reviews should remain within a team
    - Contracting teams, remote teams etc.. should do their own reviews
    - Teams should be responsible for delivery of quality
    - You should do a cursory review every now and then ensuring
        - Code is legible
        - Comments should be present, explaining thought and complexity
        - You should understand the level of quality you are receiving
- Code reviews are designed for information sharing, awareness and small defects
    - Reviews should not be thought of as quality control
    - They can pick up the occasional bug, but don’t mistake that for QC
        - And don’t substitute code scanning / analysis for code reviews
    - Reviews should be as much about commenting and clarity than code

<aside>
🔥 At the end of the day if you can’t trust the quality level of the code being delivered, get better engineers

</aside>

### 3.5.1 When should you always do code reviews?

- When it’s mission critical
- When it’s security related
- When it’s PII or PHI related
- When it’s compliance related
- When it’s the core of your business

The goal from this should be 

Does it work? 

Are we tackling it the right way? 

Can everyone understand it and know how to change it if we need to in the future?

Do you need to do this for every line of code in your system? No. But ensure that engineers know what the guidelines are. So when you do ask for a review nobody will get bent out of shape.  

For the rest use tooling 

| Tool | Function | Purpose | Recommendation |
| --- | --- | --- | --- |
| Snyk | SAST, Dependency Analysis | Source code scanning, aka Static Analysis Security Testing. To find common issues in source code | Good but features are hit and miss. Can be expensive to get full use out of. Reports are good. |
| SonarQube | SA, Quality / Reliability | Static Analysis, a ton of rules with resolutions, and  | Use! setup is difficult but it works and improves code quality significantly. |
| DeepSource | SA, SAST, OWASP | Provides multiple analysis testing, and documentation coverage, also includes AutoFix | Good for enterprises, milage will vary for things like AutoFix, but nice to have. |
| Dependbot | Dependency Analysis | Monitor libraries for reported vulnerabilities  | Use - simple github setup, there should be no excuse for not using this. |
| Github Advanced Security | SAST, Dependency Analysis, Push Scanning | Static Analysis, some security testing, dependency analysis and push scanning  | Worth dependbot and push scanning for keys. |
| SpotBot | SA | Java static analysis, replaces FindBug | Works, has plugins for security, but overtaken by SonarQube. |

The rest of source control methodologies are common place

- Pull requests
    - Protect the main branch and only allow PRs
- Templates for PRs
    - Commit templates aren’t enforced and are client side
- Tie tickets or issues to commits
- Semantic versioning
    - Major.minor.patch
    - Major for large releases or a break in backwards compatibility
    - minor for subset releases or improvements
    - patches for break fixes
- Tag releases

### 3.6. API Versioning

Lastly if you are providing an API, incorporate the version number in the API

if an SDK have .version method or in some languages a property. 

For remote interfaces like REST, it helps to have a versions.txt file especially if you have more than one. A quick way to do so during your CI process is to 

```sql
git log --decorate|head -n 1 > public/versions.txt
```

This way you can quickly verify all hosts are running the same version. 

### 3.6.1 URL Versioning

REST Service providers generally version the URL’s e.g. 

/v2/Person

When this happens, it’s because you plan on breaking backwards compatibility at some stage.

To do that you need to :

1. You need to develop a plan to migrate customers
2. Create a life cycle support policy, 
    1. support X revisions
    2. retire X-1 after Y months
3. Notification policy for customers
    1. General notification with 
        1. date 
        2. whats changing
        3. why
        4. how it impacts them and what changes they need to perform
    2. Repeat notification
    3. Individual reach out if they’re not responding (if it’s a paid service)

Another way to tackle this is by default versioning that maps to the latest version

e.g. 

/Person → /vLatest/Person

/Person → /v2/Person

The benefit of this is customers will always be on the latest version, if something breaks they have to update their software to get to a previously working version. So while they’re doing that they might as well update their software to work with the latest version. 

Again you need a customer plan, but with some subtle comms you’ll dramatically lower your overhead of managing consumers. 

## 4. Data & ML Model Version Control

With Data and Model version control there are several items that need to be managed

### 4.1. Storage and Deployment (Registry)

Data and models tend to be large, generally larger than source control or git-lfs can handle.

Thus you need to store their outputs in a SAN or cloud storage, if you are adhering to good practices your development and production systems should be detached so having the ability to copy from dev to promote to prod it essential.

### 4.2. Versioning

Change sets to large files files are hard to manage and performance bottle necks.

Manual time stamping and naming becomes complicated quickly and is error prone.

Ideally you will have source code and data versioning tied together using the same branching, and tagging strategies. Thus code, data and models match up.

### 4.3. Reproducibility (Pipelining)

Pipelines must reproduce data in similar fashion, random, quantized, class distributed etc.. 

Data may require sanitization, pre-processing, feature extraction, inference augmentation.

With the exception of feedback loops, most model updates occur infrequently, maybe yearly, managing knowledge transitions, and responsibilities is a challenge so ensuring pipelining is part of any data versioning is critical.  

### 4.4. Parameterization (Pipelining)

How is data split for training, testing, how many iterations / what stopping conditions are used.

Even down to what features are utilized is a key component to experiment tracking which needs to be capture as part of the pipeline.

### 4.5. Measuring and comparisons (Tracking)

How does model X compare to model Y.

Parameterization is often stored as experiment attributes, and how the model performs is used to determine should a model get chosen for deployment. 

At a minimum you want to capture

- Accuracy
- Loss
- True Negatives
- False Positives

There are several companies that offer parts or complete solutions

| Product | Provides | Notes | Recommendation |
| --- | --- | --- | --- |
| Hugging Face | Storage, Deployment | A fragmented set of offerings, useful for large model storage and deployments | Use but supplement with other solutions for development |
| pachyderm | Storage, Deployment, Versioning, Pipelining  | Offers an end to end solution, open source and commercial options. Requires a server instance for storage | Expensive, but commercial option is worthwhile, community option is overhead to manage |
| Neptune AI | Storage, Deployment, Versioning, Pipelining | Neptune AI is one of the go to’s for enterprises, lacks good CI integration, pay per-project vs seat, thus good to large companies | Watch, the per-project pricing is good if you have a limited number of models. CI / CD hurdles hurt |
| MLFlow | Storage, Deployment, Versioning, Pipelining, Tracking | Has everything, defacto standard, commercial options and open source. Open Source requires running a MLFlow server and tracking server | Use, probably overkill for most peoples needs |
| DVC | Storage, Deployment, Versioning, Pipelining, Tracking | Low overhead, supports multiple storage options, open source, most frequently used with MLFlow as a better storage and deployment tool | Use, highly recommended - much more unix / cli driven. Ties in well with git. Often combined with MLFlow and Neptune for UI |


