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

### Alignment of Observability Needs

One of the most important parts of the data governance framework is to align collected telemetry with *observability needs*.  What we mean here is to ensure that we understand what the primary observability objective is when we configure new telemtry.

When we introduce new telemetry we will want to understand what it delivers to our overall observability solution.  There will be overlap, but if we are considering introducting telemetry that we can align to any of the key objectives we may reconsider introducting that data.

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

## Background Information: Understanding Telemetry Cost Drivers
### On Prem Overhead (Storage, Compute)
### Cloud Provider Service Costs
### Egress Costs
### SaaS Costs
### Opportunity Costs
### Understanding Telemetry Growth
#### Business Activity Growth
#### Unexpected Third Party Change
#### Increasing Breadth of Observability
#### Misconfiguration

## Establishing Baseline Condition & Growth Forecast [#current-state]
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


## Ongoing Management of Telemetry Budget
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

##  Overage Resolution
### Ritual 3: AD Hoc Resolution Session
### Seek out redundant telemtry
### Data quality validation
### Re-assess observability needs
### Re-assess vendor roadmap

## Vendor Relationship
### Ritual 4: Monthly Vendor Checkin
### Roadmap Awareness
### Best Practice Evolution
### Consulting Engagements


