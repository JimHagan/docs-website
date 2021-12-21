---
title: Telemetry Data Governance
tags:
  - Observability maturity
  - Operational efficiency 
  - Data Ingest Management
  - Data Governance
  - Right Sizing Data Ingest
  - Implementation guide
metaDescription: Data governance is a practice of ensuring optimal value for telemetry data collected by an organization particulary a complex organization with numerous business units and working groups.
---

## Overview [#overview]

## Desired outcome [#desired-outcome]

This section will describe what we can expect from a properly executed governance plan.  Keep in mind that the priorities (techncial and business) will differ between organizations.  But generally all organizations will want some of these outcomes.

### Increased Transparency

When an organization adopts an observability platform it does not take long for developers and SREs to begin filling it up with a variety of telemetry.  When integration is fairly easy it will often be the case that in time no one person or group will know exactly what telemetry is present.  One outcome of data governance is to increase transparency in terms of the following telemetry attributes:

1. What group (within the broader org) configured this telemetry
2. What platform or application does it monitor
3. What are the growth characteristics of this data
4. Does it support critical troubleshooting

### Reduced Noise

In addition to raising the transparency of data and data sources data governance aims to reduce what "noise" in data streams.  This will follow on by the introduction of metadata standards and also through apply a value based approach to telemetry we may be able to reduce unneded redundancy.  As part of the right sizing process we will often drop unneeded attributes and metadata from an otherwise valuable data source.  By creating baseline budgets and conducting regular check ins we'll see how erratic telemetry will be identify and potentially removed if does not provide adequate value.

### Alignment of Costs With Observability Needs

One of the most important parts of the data governance framework is to align collected telemetry with *observability needs*.  What we mean here is to ensure that we understand what the primary observability objective is when we configure new telemetry.

When we introduce new telemetry we will want to understand what it delivers to our overall observability solution.  There will be overlap, but if we are considering introducing telemetry that we can align to any of the key objectives we may reconsider introducing that data.

Needs include:

- Meeting an Internal SLA
- Meeting an External SLA
- Supporting Feature Innovation (A/B Perfomance & Adoption Testing)
- Monitor Customer Experience
- Hold Vendors and Internal Service Providers to Their SLA
- Business Process Health Monitoring
- Other Compliance Requirements

### Management Structure & Process

When properly executed the data governance framework will create a simple but actionable structure and continous process for executing the key actions of the framework:

- Development of baseline budget
- Aligning new telemetry to observability objectives
- Providing checks and balances on telemetry configuration
- Tracking the telemetry footprint of newlly acquired business units or restructures within the org.

To borrow language from *agile* methodology we will create 3 main artifacts and 2 rituals to govern the process.  In addition we will create 2 roles.

*Artifacts*
- Baseline Budget Sheet
- Quarterly Growth Estimates
- Telemetry Standards Guide
- Dashboard: Real Time Ingest Tracking (By Subaccount & Telemetry Type)

*Rituals*
- Baseline Budget Planning Session
- Quarterly check-in of telemetry master and telemetry managers
- Ad Hoc Resolution Sessions

*Roles*
- Telemetry Master (responsible at high level for all org. accounts)
- Telemetry Managers (responsible for specific sub-accounts or telemetry types in sub accounts)
- Telemetry Type SMEs (expert in a broad domain such as "Logs", "APM Transactions", "On Host Integrations" etc.)
- Vendor Technical Manager

## Key Concepts: Kinds of Telemetry Costs
### On Prem Overhead (Storage, Compute)
When provisioning compute on prem or in a private account within a cloud provider there are some compute and storage costs which may be incurred in the collection of telemetry.  Some examples include:
- Storage and compute needed for a Prometheus or Elasticsearch database
- Storage need for raw log files such as those in `/var/logs`
- Compute for log forwarders such as `fluentdbit`, `logstash`, etc.
- Compute for APM and infrastructure monitoring agents
All storage and compute is priced differently depending on the host profile.  The only major distinction are those costs that are incurred to persist adn large amounts of data (e.g., Prometheus or Elasticsearch) vs. those used to forward data onto a SaaS provider where the long term persistence takes place.

### Cloud Provider Service Costs
Most cloud providers offer some different native monitoring solutions such as AWS CloudWatch. The provider will provide a per host or volume based fee for using these products.

### Egress Costs

Egress costs refer to the fees incurred by transmitting data from one network zone to another.  These can be paid to a network service provider servicing an on prem. data center.   All cloud service providers will assess some cost for moving data outside of the cloud providers domain.  Egress costs are a consideration when transmitting telemetry to to a SaaS observability platform.  these costs need to be balanced against the overall value of telemetry in our overall observability solution.

### SaaS Ingest Costs

Cloud providers may assess telemetry ingest fees based in a variety of ways.  Sometimes these can be quite complex but generally it will be along the lines of.

- Number of agent instances
- Number of application transactions monitored
- Number of traces collected
- Number of monthly active users
- Number of logs ingested
- *Gigabytes of data ingested*

This framework could provide some value to a variety of models.  However it is intended to work with the *Gigabytes of data ingested* model used by New Relic.

### Opportunity Costs

Assuming a fixed data ingest budget of $100,000 per year we can say that there are some tradeoffs between telemetry types that are stored.  For example in an organization which is currently ingesting exactly $80k per year.  One team wants to use the additional 20k for detailed user transaction logs.  These give great insights into user transactions including the products that are being purchased as well as other user context that will be really useful for the business analytics and overall business health.  Another team wishes to use the additional budget to send Browser metrics included RUM data that will offer a great detail of information about underlying user experience in our core application.  This conflict represents an opportunity cost in the sense that we can do one or another but not both within the 100k budget.  Later on we'll see how the budget planning process can be used to rectify these by possibly reducing a third source of data that may turn out to have lower value than both the Browser metrics and the Logs.  This kind of "horse trading" is an important concept in a value base data governance strategy.  If we are unable to support both Browser metrics and logs, one will be omitted and that represents the opportunity cost.

### Key Concepts: Understanding Telemetry Growth

When executing this framework it is also critical to understand the sources of telemetry growth that will occur through the year and over the years.  Some of these are generally anticipate and others may be unexpected and others completely anomalous.  These concepts are important when coming up with baseline budgets and growth targets and can also help during an ad hoc resolution of unexpected telemetry growth.

#### Business Transaction & User Growth
This class of user growth is often welcome but can also seem overwhelming if we have not data governance plan in place.  Our business is growing and we are bringing in new users and the activity of each of those users is causing additional Browser, APM, and Log data to be emitted.  The need to scale K8s clusters, load balancers, and supporting platforms like Kafka also cause an increase in the emitted telemetry.  Another type of growth is caused by an increase in business transactions without an obvious increase in the number of users.  For example a website that sells one type of product (Shoes) has now broadened its inventory to offer Hats and Gloves.  This results in more business transactions per user causing a similar cascade in telemetry as an increase in users.

#### Code Logic Change
There are some scenarios where a change in application code will cause a sudden increase in telemetry volume without any additional users or business transactions.  Some examples:

- A developer adds additional java javascript code that interacts with the backend every time a user visits a page.
- A developer adds new logging code to some application methods that are called very frequently.
- A new database schema requires  multiple database calls where previously one was needed.
- A monolithic application is broken into 5 microservices with the resulting APM and distriubuted trace data being emitted for each.

#### Instrumentation Misconfiguration
Later on we will discuss telemetry standards.  One of the thing often governed by a telemetry standard is the sampling rate for various monitoring activities.  Let's assume an organization uses a 30s sampling rate for core operating system metrics for about 2000 hosts.  A misconfiguration or unauthorized change to 10s could easily triple the OS metrics telemetry captured from those hosts.

#### Increasing Breadth of Observability
It is part of the continuous improvement process to expand observability.  An organization that was monitoring Kafka broker health for nearly two years decided to start monitoring Kafa topic offsets.  Not realizing the verbosity of topic monitoring data they are surprised when the telemetry footprint from Kafka monitoring increase 5 times.  

#### Unexpected Third Party Change
A JMX integration is designed to get any metric exposed by a third party application with prefix "Transaction_".  With version 1.0 of the application that yields in 10 events per sample.  The team maintaining the third party application adds 10 additional metrics with the "Transaction_" prefix.  When our team installs the new code, we are a bit surprised to know what JMX events have increased 2 times.


## Framework Practice: Establishing Baseline Condition & Growth Forecast [#current-state]
### High Level Overview
#### Critical Dashboards
#### Change Analysis
### Deeper Dive
### Assigning Roles
#### Telemetry Master
#### Telemetry Managers
#### Telemetry SMEs
### Ritual 1: Baseline Budget Planning Session
#### Value Mindset
#### Understanding Growth Drivers
#### Creative Solutioning
### Artifact 1: Baseline Budget Sheet
### Artifact 2: Quarterly Growth Estimates


## Framework Practice: Ongoing Management of Telemetry Budget
### Artifact 3: Telemetry Standards Guide
#### Instrumentation Standards
#### Observability as Code
#### Cleanup & Tech Debt Management
#### Metadata Standards
- Platform Ownership
- Application Ownersihp
- Enforcement
- Environment Management
#### Authorization and Accountability
#### Vendor Touchpoints
### Observability as Code
### Accountability
### Ritual 2: Quarterly Checkin

##  Framework Practice: Overage Resolution
### Ritual 3: AD Hoc Resolution Session
### Seek out redundant telemtry
### Data quality validation
### Re-assess observability needs
### Re-assess vendor roadmap

## Framework Practice: Vendor Relations
### Ritual 4: Monthly Vendor Checkin
### Roadmap Awareness
### Best Practice Evolution
### Consulting Engagements


