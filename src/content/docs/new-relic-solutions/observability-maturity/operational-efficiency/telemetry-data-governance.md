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
- Ingest Fact Report
- Baseline Budget Sheet
- Quarterly Change Plan
- Telemetry Standards Guide


*Sessions*
- Baseline Budget Planning Session
- Monthly/Quarterly Check-In (Telemetry Master & Telemery Managers)
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
### High Level Accounting

At this point it is necessary to get a high level view of all of the telemetry currently being generated in your organization.  The assumption is that any substantial organization will have a master account and multiple sub-accounts which may be dedicated to specific, teams, projects, or even separate business units.  In the context of New Relic it is necesssary to break the current ingest down by:

- Organization
- Sub account
- Billable Telemetry Type

There is value in deeper breakdowns but this one can be facilitated on any New Relic account.  Billable Telemetry Types 

- Mobile Events
- Infrastructure Hosts
- Infrastructure Integrations
- Infrastructure Processes
- APM Events
- Tracing
- Metrics
- Logging
- Custom Events
- Browser Events
- Serverless

Download [this dashboard] and install it into your NR1 Master Account or POA account.  This will allow you a fairly flexible visualization of ingest by an organizations accounts and telemetry types.  You can also visualize some built in views in New Relic's "Manage Your Data" UI.  For the purposes of transparency we will show examples of the underlying NRQL queries in this document.

Let's first see what our daily average ingest has been for the past month.  

```
SELECT rate(sum(GigabytesIngested), 1 day) AS 'Daily Ingest Rate (GB)'  FROM NrConsumption WHERE productLine = 'DataPlatform' LIMIT MAX SINCE 30 days AGO
```
*Note the use of the `rate` operator.  This will be very useful when querying the NrConsumption model.*

Our simple response for the entire organization is

```
Daily Ingest Rate: 30.4 k
```

That's a little over 30,000 GB or 30 TB per day or overy 900TB per month.  While that's a very impressive number and is meaningful to the procurement department and whoever needs ot know this months bill.  It is only the starting point to setup a data governance plan.   Let's assume our organization has a yearly budget for the current fiscal for 12TB per year.  We could infer that we are at about 90% of budget with the calculation but that assumes we are consuming at steady rate of 900TB each month.  Due to seasonality and observavabily needs evolving that is almost never the case.  Here is our actual consumption over the past 12 months. Let's do a simple `sum` rather than a rate since we have all the data collected.  Note that December 2021 will be incomplete since we are not all the way through the month.

```
SELECT sum(GigabytesIngested) AS 'Daily Ingest Rate (GB)'  FROM NrConsumption WHERE productLine = 'DataPlatform' facet monthOf(timestamp) LIMIT MAX SINCE 56 weeks AGO
```

The resulting table shows fairly high variability.  Note that things were fairly `hot` in `august` and September.  Some of that is our organization seasonality but also was related to some increasing the breadth of our telemetry coverage.

|MONTH OF TIMESTAMP|GB INGESTED|
|---|---|
|December 2021*|636 k|
|November 2021|901 k|
|October 2021|873 k|
|September 2021|1.05 M|
|August 2021|1.08 M|
|July 2021|1.05 M|
|June 2021|887 k|
|May 2021|881 k|
|||

There was a bit of an effort to cool things down a bit in the fall.
Here is where a timeseries really helps


![30 Day Ingest Last 56 Weeks](images/oma-oe-dg-30-day-ingest-last-56-weeks.png)



```
SELECT rate(sum(GigabytesIngested), 1 day) AS 'Daily Ingest Rate (GB)'  FROM NrConsumption WHERE productLine = 'DataPlatform' FACET usageMetric LIMIT MAX SINCE 30 days AGO
```


|Usage Metric|Daily Ingest Rate (GB)|
|---|---|
|MobileEventsBytes|9494.4826089365|
|InfraProcessBytes|7669.19820781793|
|ApmEventsBytes|4963.984544762140|
|MetricsBytes|2628.792197218970|
|InfraIntegrationBytes|2350.775419285230|
|InfraHostBytes|1276.2645170261700|
|CustomEventsBytes|940.0184543993|
|LoggingBytes|807.1707645250340|
|TracingBytes|160.5486142743330|
|BrowserEventsBytes|79.75575001916670|
|ServerlessBytes|6.372198490000000|
|||

Since we are trying to get a general lay of the land let's creat a time series query based on our previous query so we can see the daily ingest rates for the last six months

```
SELECT rate(sum(GigabytesIngested), 1 day) AS avgGbIngestTimeseries  FROM NrConsumption WHERE productLine = 'DataPlatform' FACET usageMetric LIMIT MAX SINCE 180 days AGO TIMESERIES auto
```

![Daily Ingest For Org Time Series](images/oma-oe-dg-daily-ingest-for-org-timeseries.png)

This kind of view provides a lot of visual information, especially if you have at least a little familiarity with the org.  Note the dip in InfraIntegrationBytes in late September.   A good working theory is that it's related to when the one of the teams altered the sample rate for their Postgres integration.  Looking at a vew like this periodically and comparing it to known ongoing work is a really valuable thing as we always want to be able to explain such dips (or spikes as the case may be).  

One final thing we can do very easily with a simple modification of the previous queries is we can facet the queries by account.  We have a far greater ability know who made a change if we can narrow it down to a specific account and telemetry type.

Let's look at the 30 day rate for all of our accounts using the last 60 days as input data.  This 30 day rate gives us the best estimate for what a customer's data ingest bill would be.


Let's limit to 5 results to keep the example table small:

```
SELECT rate(sum(GigabytesIngested), 30 day) AS avgGbIngestTimeseries  FROM NrConsumption WHERE productLine = 'DataPlatform' FACET consumingAccountName  LIMIT 10 SINCE 60 days ago
```

|Consuming Account Name|30 Day Ingest GB|
|---|---|
|Master Account|154416.2783845950|
|Streaming Video Team|147954.21967237500|
|Cloud Platform Team|100012.83564087500|
|Experimental Technology Team|88635.5364573755|
|Finance and Accounting Team|78512.21037361650|
|International_Multiplatform|51230.566752227500|
|Shipping & Receiving|48850.56031237550|
|Front End Development|45605.86433004600|
|Marketing Technology|27633.66805779|
|||


#### Artifact Definition: Ingest Fact Report

To some of extent we have explored the main components of an ingest fact report in the previous section.  Here we will simply codify the facts you will want to generate on at least monthly basis.  Having them in a dashboard may suffice, but it is also suitable to supplement with a spreadsheet of the most important values.  These figures should be updated at least as frequently as the chosen cadence of Check-in sessions (be it monthly or qurterly).  We suggest maintining them in dashboard form as well as a shared folder with spreadsheet versions of the most relevant ones.


|Fact|Type|Query|
|---|---|---|
|consuming-accounts|Number|SELECT uniqueCount(consumingAccountName) from NrConsumption since 7 days ago|
|avg-daily-rate-30-day-full-org-num|Number|SELECT rate(sum(GigabytesIngested), 1 day) FROM NrConsumption WHERE productLine = 'DataPlatform' LIMIT MAX SINCE 1 month AGO|
|daily-rate-9-month-full-org-ts|Timeseries|SELECT rate(sum(GigabytesIngested), 1 day) FROM NrConsumption WHERE productLine = 'DataPlatform' LIMIT MAX SINCE 9 months AGO TIMESERIES auto|
|consumption-by-month-full-org-ts|Timeseries|SELECT sum(GigabytesIngested) FROM NrConsumption WHERE productLine = 'DataPlatform' LIMIT MAX SINCE 56 weeks AGO TIMESERIES 30 days|
|consumption-by-month-full-org-table-tb|Table|SELECT sum(GigabytesIngested) FROM NrConsumption WHERE productLine = 'DataPlatform' facet monthOf(timestamp) LIMIT MAX SINCE 56 weeks AGO|
|avg-daily-rate-30-day-teltype-num|Table|SELECT rate(sum(GigabytesIngested), 1 day) FROM NrConsumption WHERE productLine = 'DataPlatform' LIMIT MAX SINCE 1 month AGO facet usageMetric |
|daily-rate-9-month-teltype-ts|Timeseries|SELECT rate(sum(GigabytesIngested), 1 day) FROM NrConsumption WHERE productLine = 'DataPlatform' LIMIT MAX SINCE 9 months AGO TIMESERIES auto facet usageMetric|
|consumption-by-month-teltype-ts|Timeseries|SELECT sum(GigabytesIngested) FROM NrConsumption WHERE productLine = 'DataPlatform' LIMIT MAX SINCE 56 weeks AGO TIMESERIES 30 days facet usageMetric|
|consumption-by-month-teltype-table-tb|Table|SELECT sum(GigabytesIngested) FROM NrConsumption WHERE productLine = 'DataPlatform' facet monthOf(timestamp), usageMetric  LIMIT MAX SINCE 56 weeks AGO|
|avg-daily-rate-30-day-acct-teltype-num|Table|SELECT rate(sum(GigabytesIngested), 1 day) FROM NrConsumption WHERE productLine = 'DataPlatform' LIMIT MAX SINCE 1 month AGO facet usageMetric, consumingAccountName |
|avg-daily-rate-9-month-acct-teltype-ts|Timeseries|SELECT rate(sum(GigabytesIngested), 1 day) FROM NrConsumption WHERE productLine = 'DataPlatform' LIMIT MAX SINCE 9 months AGO TIMESERIES auto facet usageMetric, consumingAccountName |
|consumption-by-month-acct-teltype-ts|Timeseries|SELECT sum(GigabytesIngested) AS FROM NrConsumption WHERE productLine = 'DataPlatform' LIMIT MAX SINCE 56 weeks AGO TIMESERIES 30 days facet usageMetric, consumingAccountName|
|consumption-by-month-acct-teltype-table-tb|Table|SELECT sum(GigabytesIngested) FROM NrConsumption WHERE productLine = 'DataPlatform' facet monthOf(timestamp), usageMetric, consumingAccountName   LIMIT MAX SINCE 56 weeks AGO|
||||

#### Change Analysis

There are some powerful techniques we can use to understand the rate of change in certain telemetry.  These change analysis techniques can be used to develop quarter and yearly growth estimates.  Some techniques include:

1. NRQL `COMPARE WITH` operator

```
SELECT sum(GigabytesIngested) FROM NrConsumption WHERE productLine = 'DataPlatform'  and usageMetric = 'BrowserEventsBytes' since 6 months ago until 1 week ago TIMESERIES 7 weeks COMPARE WITH 2 months ago
```

![Simple Compare With Time Series](images/oma-oe-dg-simple-compare-with-time-series.png)


2. NRQL `SLIDING WINDOW` operator (for smoothing away certain noise)

Telemetry is inherently noisy.  Real world phenomeno happen in spurts leaving with many random peaks and troughs in the signal.  This is good in a way since it lets us view the full comlexity of a phenomenon.  However when we are seeking to see trends we can be distracted by detail.  NRQL provides a powerful to smoothing out any time series by cominging each data point with slightly older points  This let's us focus on the overall temporal trend rather than one extreme `increase` or `decrease`


Note the jaggedness of the raw timeseries for 1 day ingest rate:

```
FROM NrConsumption SELECT rate(sum(GigabytesIngested), 1 day) WHERE productLine = 'DataPlatform' since 26 weeks ago TIMESERIES 1 day
```

![Jagged Daily Rate](images/oma-oe-dg-jagged-daily-rate-timeseries.png)


Now if we use a [sliding window](https://docs.newrelic.com/docs/query-your-data/nrql-new-relic-query-language/nrql-query-tutorials/create-smoother-charts-sliding-windows/) of 4 days to reduce the impact of single day events we will see a clearer picture.  Four days is a good choice since it will blur the impact of `weekends` so data for a Sunday will be combined somewhat with data for a Friday etc.


```
FROM NrConsumption SELECT rate(sum(GigabytesIngested), 1 day) WHERE productLine = 'DataPlatform' since 26 weeks ago TIMESERIES 1 DAY SLIDE BY 4 days
```

![Smoothed Daily Rate](images/oma-oe-dg-smoothed-daily-rate-timeseries.png)

3. ADVANCED TREND ANALYSIS

NRQL provides us some tools to assess the rate of change.  This is useful since as we see in the previous example we had a very large increase over the past several months in Browser metrics.  This rate of change analysis uses the `derivative` operator and it gives us some confidence that the main growth happened back in early September.  It seems as though our growth rate based on the 7 day derivative is somewhat negative so we may have reached a new plateau at the moment in BrowserEventsBytes ingest.

```
SELECT derivative(sum(GigabytesIngested) , 7 day) FROM NrConsumption WHERE productLine = 'DataPlatform'  and usageMetric = 'BrowserEventsBytes'  LIMIT MAX SINCE 3 MONTHS AGO UNTIL THIS MONTH TIMESERIES 1 MONTH slide by 3 days compare with 1 week ago
```

![Rate of Growth Time Series](images/oma-oe-dg-rate-of-growth-time-series.png)

In this scenario the uptick was so blatant a simple time series of the rate will suffice.  However the benefit of the deriviative is it can be more sensitive at assessing the relative quanty of growth and give us a sense of when it first started.  This can be useful if we in the early stages of a major uptick.

Here is the simple plot of the SUM

```
SELECT sum(GigabytesIngested) FROM NrConsumption WHERE productLine = 'DataPlatform'  and usageMetric = 'BrowserEventsBytes'  TIMESERIES  7 days since 6 months ago
```

![Simple SUM Time Series](images/oma-oe-dg-simple-raw-sum-time-series.png)


#### Advanced: Drilling Deeper With `bytescountestimate()`

So far our discussions of analyzing data ingest have focused on the NrConsumption model.  This is intentional since this is the one model that all NR accounts will be able to leverage in their data governance program.  The drawback of that model is that it can only be grouped by the `consumingAccount` and the `usageMetric` (i.e, high level telemetry type).  There are times when we need to drill deeper in order to inform our growth forecasts and overall governance process.  For example:

- We see that InfraProcessBytes has increased dramatically accross all accounts but do not know why
- We have started to embrace Logs but feel like we have a hard time attributing log ingest by log level.
- We recently introduced metrics from Prometheus but can't tell easily which metrics comprise the bulk of data ingest.
- We want a better accounting of which APM or Browser applications contribute to TDP
- We want to know how much we are spending on MSSQL database monitoring

In this section we'll show some approaches to better understanding some of these.   In order to fully exploit this technique it helps to have a proper metadata standard for your organization.  That will be disucssed in the section on the *Telemetry Standards Guide*

__NOTE__: These queries will need to be run a normal consuming account.  Not a `Partnership` account.  In Partnership accounts you only have access to NrConsumption, but cannot query the underlying telemetry.

**Example 1: InfraProcessBytes**


```
ProcessSample gathers detailed resource usage information from programs running on a single system. We take a snapshot of this data every 20 seconds (configurable) for every active process and package it into a ProcessSample event, which is then sent to New Relic.
```

Process sample volume can be controlled by drop rules, as well as agent side sample rate configurations and even regex patterns in the Infrastructure agent itself.

First without faceting let's explore how many bytes of ingest are consumed by the [ProcessSample](https://docs.newrelic.com/docs/infrastructure/manage-your-data/data-instrumentation/default-infrastructure-monitoring-data/) event on our `Streaming Media Team` account in the last 7 days.

With the following query:

```
FROM ProcessSample select bytecountestimate()/10e8 as 'GB Ingested' since 7 days ago
```

We get the response:

```
63.296
GB Ingested
```

On a platform like New Relic where there is a transparent per GB ingest cost.  We can easily convert that into dollars with the formula `63.296*0.25` which yields $15.  So to collect process samples 

As an example of how can attribute specific ingest amounts to specific teams or platforms we'll modify the query to facet by the `team` tag which is one of our organizations standard infrastructure tag.

```
FROM ProcessSample select bytecountestimate()/10e8 as 'GB Ingested' since 7 days ago facet team 
```

From which we get the following result:

```
8.885
News

7.65
Entertainment

47.04
Other
```

From that we can know definitely that collecting process samples on Entertainment hosts costs $1.92 in the last 7 days.  More importantly we are now aware that the category `Other` which cost us $11.76 in the past 7 days.  This is a great argument for prioritizing metadata standards since by adhering to a consistent standard we can only account for the per-team cost of about 1/3 of our ingest for this particular telemetry type.


**Example 2: Logs**

We have recently begin to try to understand how different log sources contribute to our data ingest.  We have some written standards on attribution by application and log level, but what we've found is that there is great inconsistent even within a single account.  Some logs use `app` some use `application` others use `loglevel` others use `level` and still others use `log.level`.   Regardless we want to make sum general assessments of how many of our logs have not level indicator at all and how much those cost us each week.

First let's see what we are ingesting monthly in the Fantasy Sports Team account:

```
FROM Log select bytecountestimate()/10e8 as 'GB Ingested' since 7 days ago
```

Results in:

```
2552.504
GB Ingested
```

or about $638 in the past 7 days.  The obvious query to run is:

```
FROM Log select bytecountestimate()/10e8 as 'GB Ingested' where level = 'DEBUG' since 7 days ago facet level 
```

We know there is some inconsistency in the way in which we log `DEBUG` status so we use the like operator

```
FROM Log select bytecountestimate()/10e8 as 'GB Ingested' where level like '%DEBUG%' since 7 days ago facet level 
```

Which results in:


```
1.46
DEBUG

0.0253
app.DEBUG

0.000264
security.DEBUG

2252.74
Other
```

This is a warning sign that the use of the `level` attribute is not standard enough as we are ingesting over 88% of Logs without this attribute.  After some discussion with the development teams we realize that we have the following attributes that may indicate log level:

- level_value
- loglevel
- level
- log.level

Running the following query shows us we have 50% of our logs covered by at least one of these:

```
FROM Log select bytecountestimate()/10e8 as 'GB Ingested' since 7 days ago where level is NOT NULL or level_value is NOT NULL or loglevel is NOT NULL or log.level is not NULL
```

**Example 2: MSSQL**

The higher level NrConsumption model is very useful but can often obfuscate who much individual instrumentation contribute.  We have a team that has seen a 2x uptick in the GigabytesIngested for the InfraIntegrationBytes telemetry type.  They are unsure what contributed to it.

In the team account we see that the InfraIntegrationBytes for the past 7 days is 76GB.
They have a hunch that a substantial amount of the growth came from the addition of new MSSQL instances.  However there were also some 
The following query will provide a definitive answer as to how much of the InfraIntegrationBytes is acounted for:

```
FROM MssqlDatabaseSample, MssqlInstanceSample, MssqlWaitSample select bytecountestimate()/10e8 since 7 days ago
```

Results in:

```
49.55
GB Ingested
```

Which confirms our assesment that they MSSQL on host integration is the reason for the increase.
On additional query that will help us in our forecasting is to understand how many hosts we are monitoring.
Running the unique host count shows us that we have 10 hosts being monitored with the MSSQL OHI.  Since we plan on adding an additional 5 hosts next quarter.  We can make sure that we add an additional 100GB per month of ingest or an additional $25. 



### Assigning Roles & Responsibilities
#### Telemetry Master
#### Telemetry Managers
#### Telemetry SMEs

### Session Definition: *Baseline Budget Planning*
#### Value Mindset
#### Understanding Growth Drivers
#### Creative Solutioning

### Artifact Definition: *Baseline Budget Sheet*

### Artifact Definition: *Quarterly Growth Estimates*


## Continuing Practice: Ongoing Management of Telemetry Budget
### Artifact Definition: Telemetry Standards Guide
#### Instrumentation Standards
#### Observability as Code
#### Cleanup & Tech Debt Management
#### Metadata Standards
- Platform Ownership
- Application Ownersihp
- Enforcement
- Environment Management
#### Authorization and Accountability


### Session Definition: Quarterly Checkin

###  Framework Practice: Overage Resolution
#### Session 3: AD Hoc Resolution Session
#### Seek out redundant telemtry
#### Data quality validation
#### Re-assess observability needs
#### Re-assess vendor roadmap

### Framework Practice: Vendor Relations
#### Session 4: Monthly Vendor Checkin
#### Roadmap Awareness
#### Best Practice Evolution
#### Consulting Engagements


