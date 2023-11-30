---
title: "A Common YANG Data Model for Scheduling"
abbrev: "Common Schedule YANG"
category: std

docname: draft-ma-opsawg-schedule-yang-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "Operations and Management"
workgroup: "OPSAWG"
keyword:
 - calendaring
 - scheduling
 - YANG
 - groupings

author:
 -
    fullname: Qiufang Ma
    organization: Huawei
    street: 101 Software Avenue, Yuhua District
    city: Jiangsu
    code: 210012
    country: China
    email: maqiufang1@huawei.com
 -
    fullname: Qin Wu
    organization: Huawei
    street: 101 Software Avenue, Yuhua District
    city: Jiangsu
    code: 210012
    country: China
    email: bill.wu@huawei.com
 -
    fullname: Mohamed Boucadair
    organization: Orange
    city: Rennes
    code: 35000
    country: France
    email: mohamed.boucadair@orange.com
 -
    fullname: Daniel King
    organization: Lancaster University
    country: United Kingdom
    email: d.king@lancaster.ac.uk

normative:

informative:


--- abstract

   This document defines a common schedule YANG module which is
   designed to be applicable for scheduling information such as event, policy,
   services, or resources based on date and time. For the sake of better modularity,
   the module includes basic, intermediate, and advanced version of scheduling groupings.

--- middle

# Introduction {#intro}

Several specifications include a provision for scheduling. Examples of such specifications
are (but not limited to) {{?I-D.ietf-opsawg-ucl-acl}}, {{?I-D.contreras-opsawg-scheduling-oam-tests}}, and {{?I-D.united-tvr-schedule-yang}}.
Both {{?I-D.ietf-opsawg-ucl-acl}} and {{?I-D.contreras-opsawg-scheduling-oam-tests}} use the "ietf-schedule" module
initially specified in {{?I-D.ietf-opsawg-ucl-acl}}.

Given that the applicability of the "ietf-schedule" module is more general than scheduled
policy and OAM tests, this document defines "ietf-schedule" as a common schedule YANG module. The module includes a set of reusable groupings which
are designed to be applicable for scheduling information such as event, policy,
services or resources based on date and time.

## Editorial Note (To be removed by RFC Editor)

   Note to the RFC Editor: This section is to be removed prior to publication.

   This document contains placeholder values that need to be replaced with finalized
   values at the time of publication.  This note summarizes all of the
   substitutions that are needed.  No other RFC Editor instructions are specified
   elsewhere in this document.

   Please apply the following replacements:

   * XXXX --> the assigned RFC number for this draft
   * YYYY --> the assigned RFC number for {{I-D.ietf-netmod-rfc6991-bis}}
   * 2023-01-19 --> the actual date of the publication of this document

# Conventions and Definitions

{::boilerplate bcp14-tagged}

   The meanings of the symbols in tree diagrams are defined in
   {{?RFC8340}}.

Also, this document uses the YANG terminology defined in {{Section 3 of !RFC7950}}.

#  Module Overview

   The "ietf-schedule" module defines the following groupings:

   * period-of-time
   * recurrence
   * recurrence-with-date-times
   * icalendar-recurrence

   {{schedule-tree}} provides an overview of the tree structure {{?RFC8340}} of
   the "ietf-schedule" module in terms of its groupings.

~~~~
{::include ./yang/ietf-schedule-tree.txt}
~~~~
{: #schedule-tree title="Schedule Tree Structure" artwork-align="center"}

   Each of these groupings is presented in the following subsections. Examples
   are provided in {{usage}}.

## The "period-of-time" Grouping

   The "period-of-time" grouping represents a time period using
   either a start ("period-start") and end date and time ("period-end"), or a
   start ("period-start") and a positive time duration ("duration"). For the first
   format, the start of the period MUST be before the end of the period
   {{Section 3.3.9 of !RFC5545}}.

## The "recurrence" Grouping

  The "recurrence" grouping specifies a simple recurrence rule, the definition
  conforms to part of the "recurrence rule" properties in {{Section 3.3.10 of !RFC5545}}.

  The "recurrence-first" container defines the first instance in the recurrence set.
  It also determines the start time and duration (if specified) of subsequent recurrence instances. If the
  "date-time-start" node is specified as a date-no-zone value type with no duration specified,
  the recurrence's duration is taken to be one day.

  The frequency ("frequency") identifies the type of recurrence rule. For example,
  a "daily" frequency value specifies repeating events based on an interval of a day or more.

  The interval represents at which intervals the recurrence rule repeats. For example,
  within a daily recurrence rule, an interval value of "8" means every eight days.

  The repetition can be scoped by a specified end time or by a count of occurrences,
  indicated by the "recurrence-bound" choice. The "date-time-start" value always counts
  as the first occurrence.

## The "recurrence-with-date-times" Grouping

  The "recurrence-with-date-times" grouping uses the "recurrence" grouping and
  adds a "date-times-choice" statement to define an aggregate set of repeating occurrences.
  The recurrence instances are defined by the union of occurrences defined by
  both date-times and recurrence rule. When duplicate instances are generated,
  only one recurrence is considered.

  Date-times definition inside "recurrence-with-date-times" grouping refers to
  but does not fully comply with {{Section 3.8.5.2 of !RFC5545}}. A timeticks
  type based period is added.

## The "icalendar-recurrence" Grouping

  The "icalendar-recurrence" grouping uses the "recurrence-with-date-times" grouping
  and add more data nodes to enrich the definition of recurrence. It conforms to
  the definition of recurrence component defined in {{Section 3.8.5 of !RFC5545}}.

   An array of the "bysecond" (or "byminut", "byhour") specifies a list of
   seconds within a minute (or minutes within an hour, hours of the day).

   The parameter "byday" specifies a list of days of the week, with an optional
   direction which indicates the nth occurrence of a specific day within
   the "monthly" or "yearly" frequency. For example, within a "monthly" rule,
   the "weekday" with a value of "monday" and the "direction" with a value of "-1"
   represents the last Monday of the month.

   An array of the "bymonthday" (or byyearday", "byyearweek", or "byyearmonth") specifies a list of
   days of the month (or days of the year, weeks of the year, or months of the year).

   The "bysetpos" conveys a list of values that corresponds to the nth occurrence
   within the set of recurrence instances to be specified. For example, in a "monthly"
   recurrence rule, the "byday" data node specifies every Monday of the week, the
   "bysetpos" with value of "-1" represents the last Monday of the month.
   Not setting the "bysetpos" data node represents every Monday of the month.

   The "workweek-start" data node specifies the day on which the week starts. This is
   significant when a "weekly" recurrence rule has an interval greater than 1, and
   a "byday" data node is specified. This is also significant when in a "yearly" rule
   and a "byyearweek" is specified. The default value is "monday".

   the "exception-dates" data node specifies a list of exceptions for recurrence. The
   final recurrence set is generated by gathering all of the date and time values
   generated by any of the specified recurrence rule and date-times, and then
   excluding any start date and time values specified by "exception-dates" parameter.

#  Features and Augmentation

   The "ietf-schedule" data model defines the recurrence related groupings using
   a modular approach. Basic, intermediate and advanced representation of recurrence
   groupings are defined, with each reusing the previous one and adding more parameters.
   To allow for different options, two features are defined in the data model:

   *  basic-recurrence-supported
   *  icalendar-recurrence-supported

   {{features}} provides an example about how that could be used. Implementations
   may support a basic recurrence rule or an advanced one as needed, by declaring
   different features.

   These groupings can also be augmented to support specific needs. As an example,
   {{augments}} demonstrates how additional parameter can be augmented.

#  The "ietf-schedule" YANG Module {#sec-schedule}

   This module imports types defined in {{!I-D.ietf-netmod-rfc6991-bis}}.

~~~~
<CODE BEGINS> file "ietf-schedule@2023-01-19.yang"
{::include ./yang/ietf-schedule.yang}
<CODE ENDS>
~~~~

# Security Considerations

   The "ietf-schedule" YANG module specified in this document defines schema for data
   that is designed to be accessed via network management protocols such
   as NETCONF {{!RFC6241}} or RESTCONF {{!RFC8040}}.  The lowest NETCONF layer
   is the secure transport layer, and the mandatory-to-implement secure
   transport is Secure Shell (SSH) {{!RFC6242}}.  The lowest RESTCONF layer
   is HTTPS, and the mandatory-to-implement secure transport is TLS
   {{!RFC8446}}.

   The Network Configuration Access Control Model (NACM) {{!RFC8341}}
   provides the means to restrict access for particular NETCONF or
   RESTCONF users to a preconfigured subset of all available NETCONF or
   RESTCONF protocol operations and content.

   The "ietf-schedule" module defines a set of types and
   groupings.  These nodes are intended to be reused by other YANG
   modules.  The module by itself does not expose any data nodes that
   are writable, data nodes that contain read-only state, or RPCs.  As
   such, there are no additional security issues related to the "ietf-
   schedule" module that need to be considered.

#  IANA Considerations

##  The "IETF XML" Registry

   This document registers the following URI in the "IETF XML Registry" {{!RFC3688}}.

~~~~
        URI: urn:ietf:params:xml:ns:yang:ietf-schedule
        Registrant Contact: The IESG.
        XML: N/A, the requested URI is an XML namespace.
~~~~

##  The "YANG Module Names" Registry

   This document registers the following YANG module in the "YANG Module Names"
   registry {{!RFC6020}}.

~~~~
        name:               ietf-schedule
        namespace:          urn:ietf:params:xml:ns:yang:ietf-schedule
        prefix:             schedule
        maintained by IANA: N
        reference:          RFC XXXX
~~~~

--- back

# Examples of Format Representation {#usage}

   This section provides some examples to illustrate the use of the
   period and recurrence formats defined as YANG groupings. Note that "grouping"
   does not define any data nodes in the schema tree, the examples illustrated are
   just for the ease of understanding. Only the message body is provided with
   JSON used for encoding {{?RFC7951}}.

## The "period-of-time" Grouping

   The example of a period that starts at 08:00:00 UTC, on January 1, 2025 and ends at 18:00:00 UTC
   on December 31, 2027 is encoded as shown in {{ex-1}}.

~~~~
{
  "period-start": "2025-01-01T08:00:00Z",
  "period-end": "2027-12-01T18:00:00Z"
}
~~~~
{: #ex-1 title="Simple Start/End Schedule" artwork-align="center"}

   An example of a period that starts at 08:00:00 UTC, on January 1, 2025 and lasts 15 days and
   5 hours and 20 minutes is encoded as shown in {{ex-2}}.

~~~~
{
  "period-start": "2025-01-01T08:00:00Z",
  "duration": "P15DT05:20:00"
}
~~~~
{: #ex-2 title="Simple Schedule with Duration" artwork-align="center"}

   An example of a period that starts at 2:00 A.M. in Los Angeles on November 19,
   2025 and lasts 20 weeks is depicted in {{ex-3}}.

~~~~
{
  "period-start": "2025-11-19T02:00:00",
  "time-zone-identifier": "America/Los_Angeles",
  "duration": "P20W"
}
~~~~
{: #ex-3 title="Simple Schedule with Time Zone Indication" artwork-align="center"}

## The "recurrence" Grouping

   {{ex-4}} indicates a every other day recurrence for
   10 occurrences, starting at 3 p.m. on December 1, 2025 in the Eastern United States time zone:

~~~~
{
  "recurrence-first": {
    "date-time-start": "2025-11-01T15:00:00",
    "time-zone-identifier": "America/New_York"
  },
  "frequency": "ietf-schedule:daily",
  "interval": 2,
  "count": 10
}
~~~~
{: #ex-4 title="Simple Schedule with Recurrence" artwork-align="center"}

   {{ex-5}} illustrates an example of an anniversary that will occur annually, from 1997-11-25, until 2050-11-25:

~~~~
{
  "recurrence-first": {
    "date-time-start": "1979-11-25"
  },
  "frequency": "ietf-schedule:yearly",
  "until": "2050-11-25"
}
~~~~
{: #ex-5 title="Simple Schedule with Recurrence and End Date" artwork-align="center"}

## The "recurrence-with-date-times" Grouping

   {{ex-6}} indicates a recurrence that occurs every 2
   hours from 9:00 AM to 5:00 PM, and 6PM UTC time on 2025-12-01:

~~~~
{
  "recurrence-first": {
    "date-time-start": "2025-12-01T09:00:00Z"
  },
  "frequency": "ietf-schedule:hourly",
  "interval": 2,
  "until": "2025-12-01T17:00:00Z",
  "date-times": ["2025-12-01T18:00:00Z"]
}
~~~~
{: #ex-6 title="Example of Recurrence With Date Times" artwork-align="center"}


   {{ex-7}} indicates a recurrence that occurs every
   30 minutes and last for 15 minutes from 9:00 AM to 5:00 PM, and extra two occurrences
   at 6:00 PM and 6:30 PM with each lasting for 20 minutes on 2025-12-01:

~~~~
{
  "recurrence-first": {
    "date-time-start": "2025-12-01T09:00:00Z",
    "duration": "PT00:00:15"
  },
  "frequency": "ietf-schedule:minutely",
  "interval": 30,
  "until": "2025-12-01T17:00:00Z",
  "period": [
    {
      "period-start": "2025-12-01T18:00:00Z",
      "duration": "PT00:00:20"
    },
    {
      "period-start": "2025-12-01T18:30:00Z",
      "duration": "PT00:00:20"
    }
   ]
}
~~~~
{: #ex-7 title="Example of Advanced Recurrence Schedule" artwork-align="center"}

## The "icalendar-recurrence" Grouping

   {{ex-8}} indicates 10 occurrences that occur at
   8:00 AM (EST), every last Saturday of the month starting in January 2024:

~~~~
{
  "recurrence-first": {
    "date-time-start": "2024-01-27T08:00:00",
    "time-zone-identifier": "America/New_York"
  },
  "frequency": "ietf-schedule:monthly",
  "count": 10,
  "byday": [
    {
      "direction": [-1],
      "weekday": "saturday"
    }
  ]
}
~~~~
{: #ex-8 title="Simple iCalendar Recurrence" artwork-align="center"}

   {{ex-9}} is an example of a recurrence that occurs on the last
   workday of the month until December 25, 2024, from January 1, 2024:

~~~~
{
  "recurrence-first": {
  "date-time-start": "2024-01-01"
  },
  "frequency": "ietf-schedule:monthly",
  "until": "2024-12-25",
  "byday": [
    { "weekday": "monday"},
    { "weekday": "tuesday"},
    { "weekday": "wednesday"},
    { "weekday": "thursday"},
    { "weekday": "friday"}
  ],
  "bysetpos": [-1]
}
~~~~
{: #ex-9 title="Example of Advanced iCalendar Recurrence" artwork-align="center"}

  {{ex-10}} indicates a recurrence that occur every 20
  minutes from 9:00 AM to 4:40 PM (UTC), with the occurrence starting at 10:20 AM
  being excluded on 2025-12-01:

~~~~
{
  "recurrence-first": {
    "date-time-start": "2025-12-01T09:00:00Z"
  },
  "until": "2025-12-01T16:40:00Z",
  "frequency": "ietf-schedule:minutely",
  "byminute": [0, 20, 40],
  "byhour": [9, 10, 11, 12, 13, 14, 15, 16],
  "exception-dates": ["2025-12-01T10:20:00Z"]
}
~~~~
{: #ex-10 title="Example of Advanced iCalendar Recurrence with Exceptions" artwork-align="center"}


# Examples of Using/Extending the "ietf-schedule" Module

   This non-normative section shows two examples for how the "ietf-schedule" module
   can be used or extended for scheduled events or attributes based on date and time.

## Example: Schedule Tasks to Execute Based on a Recurrence Rule {#features}

   Scheduled tasks can be used to execute specific actions based on certain recurrence rules (e.g.,
   every Friday at 8:00 AM). The following example module which "uses" the "icalendar-recurrence"
   grouping from "ietf-schedule" module shows how a scheduled task could be defined
   with different features used for options.

~~~~
{::include ./yang/example-scheduled-backup.yang}
~~~~

## Example: Schedule Network Properties to Change Based on Date and Time {#augments}

   Network properties may change over a specific period of time or based on a
   recurrence rule, e.g., {{?I-D.ietf-tvr-use-cases}}.
   The following example module which augments the "recurrence-with-date-times"
   grouping from "ietf-schedule" module shows how a scheduled based attribute
   could be defined.

~~~~
{::include ./yang/example-scheduled-link-bandwidth.yang}
~~~~

  {{ex-11}} shows a configuration example of a link's bandwidth that is
  scheduled between 2023/12/01 0:00 UTC to the end of 2023/12/31 with a daily
  schedule. In each day, the bandwidth value is scheduled to be 500Kbps between
  1:00 AM to 6:00 AM and 800 Kbps between 10:00 PM to 11:00 PM. The bandwidth
  value that's not covered by the period above is 1000 Kbps by default.

~~~~
{::include-fold ./examples/example-scheduled-link-bandwidth.xml}
~~~~
{: #ex-11 title="Example of Scheduled Link's Bandwidth" artwork-align="center"}

# Changes between Revisions


# Acknowledgments
{:numbered="false"}

   This work is derived from the {{?I-D.ietf-opsawg-ucl-acl}}. There is a desire
   from the OPSAWG to see this model be separately defined for wide use in scheduling context.

   Thanks to Adrian Farrel, Wei Pan, Tianran Zhou, and Joe Clarke
   for their valuable comments and inputs to this work.

   Many thanks to the authors of {{?I-D.united-tvr-schedule-yang}}, {{?I-D.contreras-opsawg-scheduling-oam-tests}}, and {{?I-D.ietf-netmod-eca-policy}}
   for the constructive discussion during IETF#118.
