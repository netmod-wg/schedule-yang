---
title: "A YANG Data Model for Scheduling"
abbrev: "schedule YANG"
category: std

docname: draft-ma-netmod-schedule-yang-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "Operations and Management"
workgroup: "NETMOD"
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

   this document defines a common schedule YANG module which is
   designed to be applicable for scheduling information such as event, policy,
   services or resources based on date and time.

--- middle

# Introduction {#intro}

   This document defines a common schedule YANG module which
   is designed to be applicable for scheduling information such as event, policy,
   services or resources based on date and time. This model is designed with the intent to be reusable in other
   scheduling contexts. In particular, the groupings can be reused by other modules.

   The YANG data models in this document conform to the Network
   Management Datastore Architecture (NMDA) defined in {{!RFC8342}}.

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

#  Modules Overview

##  The Schedule YANG Module

   This module defines two groupings "period" and "recurrence", which conform to
   the definition of the "period of time" and "recurrence rule" formats defined in
   {{?RFC5545}}. The authors might consider to define more formats in future revisions.

   {{schedule-tree}} provides an overview of the tree structure of the "ietf-
   schedule" module in terms of its groupings.

~~~~
{::include ./yang/ietf-schedule-tree.txt}
~~~~
{: #schedule-tree title="Schedule Tree Structure" artwork-align="center"}

   The "period-of-time" allows a time period to be represented using either a start ("period-start")
   and end date and time ("period-end"), or a start ("period-start") and a positive time duration ("period-duration").

   The "recurrence" indicates the scheduling recurrence of an event. The
   repetition can be scoped by a specified end time or by a count of occurences,
   and the frequency identifies the type of recurrence rule. For example, a "daily"
   frequency value specifies repeating events based on an interval of a day or more.
   The interval represents at which intervals the recurrence rule repeats. For example,
   within a daily recurrence rule, an interval value of "8" means every eight days.

   An array of the "bysecond" (or "byminut", "byhour") specifies a list of seconds within a minute (or minutes within an hour, hours of the day).

   The parameter "byday" specifies a list of days of
   the week, with an optional direction which indicates the nth occurrence of a specific day within
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

   The "wkst" data node specifies the day on which the week starts. This is
   significant when a "weekly" recurrence rule has an interval greater than 1, and
   a "byday" data node is specified. This is also significant when in a "yearly" rule
   and a "byyearweek" is specified. The default value is "monday".

### Examples

   The following subsections provide some examples to illustrate the use of the period and recurrence formats defined as
   YANG groupings. Only the message body is provided with JSON used for encoding {{?RFC7951}}.

#### Period of Time

   The example of a period that starts at 08:00:00 UTC, on January 1, 2023 and ends at 18:00:00 UTC
   on December 31, 2025 is encoded as follows:

~~~~
{
  "period-of-time": {
    "period-start": "2023-01-01T08:00:00Z",
    "period-end": "2025-12-01T18:00:00Z"
  }
}
~~~~

   An example of a period that starts at 08:00:00 UTC, on January 1, 2023 and lasts 15 days and
   5 hours and 20 minutes is encoded as follows:

~~~~
{
  "period-of-time": {
    "period-start": "2023-01-01T08:00:00Z",
    "period-duration": "P15DT05:20:00"
  }
}
~~~~

   Now, consider the example of a period that starts at 08:00:00 UTC, on January 1, 2023 and lasts 20 weeks:

~~~~
{
  "period-of-time": {
    "period-start": "2023-01-01T08:00:00Z",
    "period-duration": "P20W"
  }
}
~~~~

#### Recurrence Rule

  The following snippet can be used to indicate a daily recurrent in December:

~~~~
{
  "recurrence": {
    "freq": "daily",
    "byyearmonth": [12]
  }
}
~~~~

   The following snippet can be used to indicate 10 occurrences that occur every last Saturday of the month:

~~~~
{
  "recurrence": {
    "freq": "monthly",
    "count": 10,
    "byday": [
      {
        "direction": [-1],
        "weekday": "saturday"
      }
    ]
  }
}
~~~~

   The following indicates the example of a recurrence that occurs on the last workday of the month until December 25, 2023:

~~~~
{
  "recurrence": {
    "freq": "monthly",
    "until": "2023-12-25",
    "byday": [
      { "weekday": "monday" },
      { "weekday": "tuesday" },
      { "weekday": "wednesday" },
      { "weekday": "thursday" },
      { "weekday": "friday" }
    ],
    "bysetpos": [-1]
  }
}
~~~~


   The following depicts the example of a recurrence that occurs every other week on Tuesday and Sunday, the week starts from Monday:

~~~
{
  "recurrence": {
    "freq": "weekly",
    "interval": 2,
    "byday": [
      { "weekday": "tuesday" },
      { "weekday": "sunday" }
    ],
    "wkst": "monday"
  }
}
~~~


#  YANG Modules

##  The "ietf-schedule" YANG Module {#sec-schedule}

   This module imports types defined in {{!I-D.ietf-netmod-rfc6991-bis}}.

~~~~
<CODE BEGINS>
file=ietf-schedule@2023-01-19.yang
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

   This document registers the following URIs in the "IETF XML Registry" {{!RFC3688}}.

~~~~
        URI: urn:ietf:params:xml:ns:yang:ietf-schedule
        Registrant Contact: The IESG.
        XML: N/A, the requested URI is an XML namespace.
~~~~

##  The "YANG Module Names" Registry

   This document registers the following YANG modules in the "YANG Module Names"
   registry {{!RFC6020}}.

~~~~
        name:               ietf-schedule
        namespace:          urn:ietf:params:xml:ns:yang:ietf-schedule
        prefix:             schedule
        maintained by IANA: N
        reference:          RFC XXXX
~~~~

--- back

# Changes between Revisions


# Acknowledgments
{:numbered="false"}

   This work is derived from the {{?I-D.ietf-opsawg-ucl-acl}}. There is a desire
   from the WG to see this model be seperately defined for wide use in scheduling context.

   The authors would like to thank Adrian Farrel, Wei Pan, Tianran Zhou and Joe Clarke
   for their valuable comments and great input to this work.
