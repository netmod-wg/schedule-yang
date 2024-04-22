---
title: "A Common YANG Data Model for Scheduling"
abbrev: "Common Schedule YANG"
category: std

docname: draft-ietf-netmod-schedule-yang-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "Operations and Management"
workgroup: "netmod"
keyword:
 - calendaring
 - scheduling
 - YANG
 - groupings

author:
 -
    fullname: Qiufang Ma
    organization: Huawei
    role: editor
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
    role: editor
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
   designed to be applicable for scheduling purposes such as event, policy,
   services, or resources based on date and time. For the sake of better modularity,
   the module includes basic, intermediate, and advanced versions of recurrence
   related groupings.

--- middle

# Introduction {#intro}

This document defines a common schedule YANG module ("ietf-schedule") that can be used in several scheduling contexts (e.g., (but not limited to) {{?I-D.ietf-opsawg-ucl-acl}}, {{?I-D.contreras-opsawg-scheduling-oam-tests}}, and {{?I-D.united-tvr-schedule-yang}}). The module includes a set of reusable groupings which
are designed to be applicable for scheduling purposes such as event, policy,
services or resources based on date and time.

Examples to illustrate the use of the common groupings are provided in {{usage}}. Also, sample modules to exemplify how future modules can use the extensibility provisions in "ietf-schedule" are provided in {{sec-ext}}.

## Editorial Note (To be removed by RFC Editor)

   Note to the RFC Editor: This section is to be removed prior to publication.

   This document contains placeholder values that need to be replaced with finalized
   values at the time of publication.  This note summarizes all of the
   substitutions that are needed.  No other RFC Editor instructions are specified
   elsewhere in this document.

   Please apply the following replacements:

   * XXXX --> the assigned RFC number for this draft
   * YYYY --> the assigned RFC number for {{I-D.ietf-netmod-rfc6991-bis}}
   * 2024-04-16 --> the actual date of the publication of this document

# Conventions and Definitions

{::boilerplate bcp14-tagged}

   The meanings of the symbols in tree diagrams are defined in
   {{?RFC8340}}.

Also, this document uses the YANG terminology defined in {{Section 3 of !RFC7950}}.

#  Module Overview

   The "ietf-schedule" module ({{sec-schedule}}) defines the following groupings:

   * "generic-schedule-params" ({{sec-gen}})
   * "period-of-time" ({{sec-period}})
   * "recurrence" ({{sec-rec}})
   * "recurrence-utc" ({{sec-rec-utc}})
   * "recurrence-with-time-zone" ({{sec-rec-tz}})
   * "recurrence-utc-with-date-times" ({{sec-rec-utc-dt}})
   * "recurrence-time-zone-with-date-times" ({{sec-rec-tz-dt}})
   * "icalendar-recurrence" ({{sec-ical-rec}})
   * "schedule-status" ({{sec-schedule-status}})

   {{schedule-tree}} provides an overview of the tree structure {{?RFC8340}} of
   the "ietf-schedule" module in terms of its groupings.

~~~~
{::include ./yang/tree/overall.txt}
~~~~
{: #schedule-tree title="Overall Schedule Tree Structure"}

   Each of these groupings is presented in the following subsections. Examples
   are provided in {{usage}}.

## The "generic-schedule-params" Grouping {#sec-gen}

   The "generic-schedule-params" grouping ({{gsp-tree}}) specifies a set of
   configuration parameters that are used by a system for validating requested
   schedules.

   The "time-zone-identifier" parameter, if provided, specifies the
   time zone reference of the date-and-time values.

   The "validity" parameter specifies the date and time after which a schedule
   will be considered as invalid.

   The "max/min-allowed-start" parameters specify the maximum/minimum scheduled
   start date and time, a requested schedule will be rejected if the first
   occurrence of the schedule is later/earlier than the configured values.

   The "max-allowed-end" parameter specifies the maximum allowed end time of
   the last occurrence.

   The "discard-action" parameter specifies the action if a requested schedule
   is considered inactive or out-of-date.

   These parameters apply to all schedules on a system and are meant
   to provide guards against stale configuration, too short schedule requests
   that would prevent validation by admins of some critical systems, etc.


~~~~
{::include ./yang/tree/sch-generic-params.txt}
~~~~
{: #gsp-tree title="Generic Schedule Configuration Tree Structure"}

## The "period-of-time" Grouping {#sec-period}

   The "period-of-time" grouping ({{pt-tree}}) represents a time period using
   either a start ("period-start") and end date and time ("period-end"), or a
   start ("period-start") and a positive time duration ("duration"). For the first
   format, the start of the period MUST be before the end of the period.


~~~~
{::include ./yang/tree/period-grp.txt}
~~~~
{: #pt-tree title="Period of Time Grouping Tree Structure"}


## The "recurrence" Grouping {#sec-rec}

  The "recurrence" grouping ({{rec-grp-tree}}) specifies a simple recurrence rule.

~~~~
{::include ./yang/tree/rec-grp.txt}
~~~~
{: #rec-grp-tree title="Recurrence Grouping Tree Structure"}

  The "recurrence-first" container defines the first instance in the recurrence
  set, and the "date-time-start" specifies the date and time at which the first
  instance in the recurrence set occurs.

  The frequency ("frequency") identifies the type of recurrence rule. For example,
  a "daily" frequency value specifies repeating events based on an interval of a day or more.

  The interval represents at which intervals the recurrence rule repeats. For example,
  within a daily recurrence rule, an interval value of "8" means every eight days.

  The repetition can be scoped by a specified end time or by a count of occurrences,
  indicated by the "recurrence-bound" choice. The "date-time-start" value always counts
  as the first occurrence.

## The "recurrence-utc" Grouping {#sec-rec-utc}

   The "recurrence-utc" grouping ({{rec-utc-grp-tree}}) specifies a simple recurrence
   rule in UTC format.

~~~~
{::include ./yang/tree/rec-utc-grp.txt}
~~~~
{: #rec-utc-grp-tree title="recurrence-utc Grouping Tree Structure"}

   The "recurrence-utc" grouping refines the "recurrence" grouping to require
   that the date and time values to describe the recurrence be reported in UTC format.

   It also augments the "recurrence-first" container with a parameter "duration"
   in units of seconds to allow the time period of the first occurrence to be
   specified. The "duration" also applies to subsequent recurrence instances.

## The "recurrence-with-time-zone" Grouping {#sec-rec-tz}

   The "recurrence-with-time-zone" grouping ({{rec-tz-grp-tree}}) specifies a simple recurrence
   rule with a time zone.

~~~~
{::include ./yang/tree/rec-tz-grp.txt}
~~~~
{: #rec-tz-grp-tree title="recurrence-with-time-zone Grouping Tree Structure"}

   The "recurrence-with-time-zone" grouping augments the "recurrence-first" container
   with the "time-zone-identifier" parameter which MUST be specified if the date
   and time value is neither reported in the format of UTC nor time zone offset
   to UTC.

   It also augments the "recurrence-first" container with a parameter "duration"
   to allow the time period of the first occurrence to be specified. The
   "duration" also applies to subsequent recurrence instances.

   Unlike the definition of "recurrence-utc" grouping {{sec-rec-utc}},
   "recurrence-with-time-zone" is intended to be friendly to human instead of
   machine.

## The "recurrence-utc-with-date-times" Grouping {#sec-rec-utc-dt}

   The "recurrence-utc-with-date-times" grouping ({{rec-utc-dt-grp-tree}}) uses
   the "recurrence-utc" grouping ({{sec-rec-utc}}) and adds a "period-timeticks"
   list to define an aggregate set of repeating occurrences.

~~~~
{::include ./yang/tree/rec-utc-dt-grp.txt}
~~~~
{: #rec-utc-dt-grp-tree title="recurrence-utc-with-date-times Grouping Tree Structure"}

  The recurrence instances are specified by the union of occurrences defined
  by both the recurrence rule and "period-timeticks" list. Duplicate instances
  are ignored. The value of the "period-start" instance must not exceed the
  value of "frequency" instance.

## The "recurrence-time-zone-with-date-times" Grouping {#sec-rec-tz-dt}

  The "recurrence-time-zone-with-date-times" grouping ({{rec-tz-dt-grp-tree}}) uses
  the "recurrence-with-time-zone" grouping ({{sec-rec-tz}}) and
  adds a "period" list to define an aggregate set of repeating occurrences.

~~~~
{::include ./yang/tree/rec-tz-dt-grp.txt}
~~~~
{: #rec-tz-dt-grp-tree title="Recurrence with Date Times Grouping Tree Structure"}

  The recurrence instances are specified by the union of occurrences defined
  by both the recurrence rule and "period" list. Duplicate instances
  are ignored.

## The "icalendar-recurrence" Grouping {#sec-ical-rec}

  The "icalendar-recurrence" grouping ({{ical-grp-tree}}) uses the
  "recurrence-time-zone-with-date-times" grouping ({{sec-rec-tz-dt}}) and define
  more data nodes to enrich the definition of recurrence. The structure of the
  "icalendar-recurrence" grouping conforms to the definition of recurrence
  component defined in {{Section 3.8.5 of ?RFC5545}}.

~~~~
{::include ./yang/tree/ical-grp.txt}
~~~~
{: #ical-grp-tree title="iCalendar Recurrence Grouping Tree Structure"}

   An array of the "bysecond" (or "byminute", "byhour") specifies a list of
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

   The "exception-dates" data node specifies a list of exceptions for recurrence. The
   final recurrence set is generated by gathering all of the date and time values
   generated by any of the specified recurrence rule and date-times, and then
   excluding any start date and time values specified by "exception-dates" parameter.

## The "schedule-status" Grouping {#sec-schedule-status}

   The "schedule-status" grouping ({{sche-status-tree}}) defines common parameters
   for scheduling management/status exposure.

~~~~
{::include ./yang/tree/schedule-status.txt}
~~~~
{: #sche-status-tree title="Schedule Status Grouping Tree Structure"}

   The "schedule-id" parameter is useful to uniquely identify a schedule in
   a network device or controller if multiple scheduling contexts exists.

   The "state" parameter is defined to configure/expose the scheduling state,
   depending on the use of the grouping. The "identityref" type is used for this
   parameter to allow extensibility in future modules. For example, a "conflict"
   state is valid in scheduling contexts where multiple systems struggle for the
   scheduling of the same property. The conflict may be induced by, e.g., multiple
   entities managing the schedules for the same target component.

   The "version" parameter is used to track the current schedule version
   information. The version can be bumped by the entity who create the schedule.
   The "last-update" parameter identifies when the schedule was last modified.
   In some contexts, this parameter can be used to track the configuration of a
   given schedule. In such cases, the "version" may not be used.

   The "schedule-type" parameter identifies the type of the current schedule.
   The "counter", "last-occurrence", and "upcoming-occurrence" data nodes are
   only avaliable when the "schedule-type" is "recurrence".

   The current grouping captures common parameters that is applicable
   to typical scheduling contexts known so far. Future modules can define other
   useful parameters as needed. For example, in a  scheduling context with multiple
   system sources to feed the schedules, the "source" and "precedence" parameters
   may be needed to reflect how schedules from different sources should be prioritised.

#  Features and Augmentations

   The "ietf-schedule" data model defines the recurrence related groupings using
   a modular approach. Basic, intermediate, and advanced representation of recurrence
   groupings are defined, with each reusing the previous one and adding more parameters.
   To allow for different options, two features are defined in the data model:

   *  'basic-recurrence-supported'
   *  'icalendar-recurrence-supported'

   {{features}} provides an example about how that could be used. Implementations
   may support a basic recurrence rule or an advanced one as needed, by declaring
   different features. Whether only one or both features are supported is implementation
   specific and depend on specific scheduling context.

   These groupings can also be augmented to support specific needs. As an example,
   {{augments}} demonstrates how additional parameters can be added to comply with specifc schedule needs.

#  Note and Restrictions

   There are some restrictions that need to be followed when using groupings defined
   in "ietf-schedule" yang module:

   *  The instant in time represented by "period-start" MUST be before the
      "period-end" for "period-of-time" grouping
   *  The combination of the day, month, and year represented for date and time
      value MUST be valid. See {{Section 5.7 of ?RFC3339}} for the maxinum day
      number based on the month and year.
   *  The second MUST have the value "60" at the end of months in which a leap
      second occurs for date and time value
   *  Care must be taken when defining recurrence occurring very often and
      frequent that can be an additional source of attacks by keeping the system
      permanently busy with the management of scheduling
   *  Schedules received with a starting time in the past with respect to
      current time SHOULD be ignored

#  The "ietf-schedule" YANG Module {#sec-schedule}

   This module imports types defined in {{!I-D.ietf-netmod-rfc6991-bis}}.

~~~~
<CODE BEGINS> file "ietf-schedule@2024-04-16.yang"
{::include-fold ./yang/ietf-schedule.yang}
<CODE ENDS>
~~~~

# Security Considerations

This section uses the template described in {{Section 3.7 of ?I-D.ietf-netmod-rfc8407bis}}.

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
  "period-end": "2027-12-31T18:00:00Z"
}
~~~~
{: #ex-1 title="Simple Start/End Schedule"}

   An example of a period that starts at 08:00:00 UTC, on January 1, 2025 and lasts 15 days and
   5 hours and 20 minutes is encoded as shown in {{ex-2}}.

~~~~
{
  "period-start": "2025-01-01T08:00:00Z",
  "duration": "P15DT05:20:00"
}
~~~~
{: #ex-2 title="Simple Schedule with Duration"}

   An example of a period that starts at 2:00 A.M. in Los Angeles on November 19,
   2025 and lasts 20 weeks is depicted in {{ex-3}}.

~~~~
{
  "period-start": "2025-11-19T02:00:00",
  "time-zone-identifier": "America/Los_Angeles",
  "duration": "P20W"
}
~~~~
{: #ex-3 title="Simple Schedule with Time Zone Indication"}

## The "recurrence" Grouping

   {{ex-6}} indicates a recurrence of every 2 days for 10 occurrences, starting
   at 3 p.m. on December 1, 2025 with an offset of -8:00 from UTC:

~~~~
{
  "recurrence-first": {
    "date-time-start": "2025-12-01T15:00:00-08:00:00"
  },
  "frequency": "ietf-schedule:daily",
  "interval": 2,
  "count": 10
}
~~~~
{: #ex-4 title="Simple Schedule with Recurrence"}

## The "recurrence-utc" Grouping

   {{ex-5}} indicates a recurrence from 8:00 AM to 9:00 AM every day, from
   December 1 to December 31, 2025 in UTC:

~~~~
{
  "recurrence-first": {
    "date-time-start": "2025-12-01T08:00:00Z",
    "duration": "3600";
  },
  "frequency": "ietf-schedule:daily",
  "interval": 1,
  "until": "2025-12-31T23:59:59Z"
}
~~~~
{: #ex-5 title="Simple Schedule with Recurrence in UTC"}

## The "recurrence-with-time-zone" Grouping

   {{ex-6}} indicates a recurrence of every 2 hours for 10 occurrences, lasting
   10 minutes, and starting at 3 p.m. on December 1, 2025 in New York:

~~~~
{
  "recurrence-first": {
    "date-time-start": "2025-12-01T15:00:00",
    "duration": "PT00:10:00",
    "time-zone-identifier": "America/New_York"
  },
  "frequency": "ietf-schedule:hourly",
  "interval": 2,
  "count": 10
}
~~~~
{: #ex-6 title="Simple Schedule with Recurrence with Time Zone Indication"}

## The "recurrence-utc-with-date-times" Grouping

   {{ex-7}} indicates a recurrence that occurs every two days starting at 9:00 AM
   and 3:00 PM for a duration of 30 minutes and 40 minutes respectively,
   from 2025-06-01 to 2025-06-30 in UTC:

~~~~
{
  "recurrence-first": {
    "date-time-start": "2025-06-01T09:00:00Z",
  },
  "frequency": "ietf-schedule:daily",
  "interval": 2,
  "until": "2025-06-30T23:59:59Z",
  "period-timeticks": [
    {
      "period-start": "3240000",
      "period-end": "3420000"
     },
     {
      "period-start": "5400000",
      "period-end": "5640000"
     }
   ]
}
~~~~
{: #ex-7 title="Example of Recurrence With Date Times"}

## The "recurrence-time-zone-with-date-times" Grouping

   {{ex-8}} indicates a recurrence that occurs every
   30 minutes and last for 15 minutes from 9:00 AM to 5:00 PM, and extra two occurrences
   at 6:00 PM and 6:30 PM with each lasting for 20 minutes on 2025-12-01 (New York):

~~~~
{
  "recurrence-first": {
    "date-time-start": "2025-12-01T09:00:00",
    "duration": "PT00:15:00",
    "time-zone-identifier": "America/New_York"
  },
  "frequency": "ietf-schedule:minutely",
  "interval": 30,
  "until": "2025-12-01T17:00:00Z",
  "period": [
    {
      "period-start": "2025-12-01T18:00:00",
      "duration": "PT00:20:00"
    },
    {
      "period-start": "2025-12-01T18:30:00",
      "duration": "PT00:20:00"
    }
   ]
}
~~~~
{: #ex-8 title="Example of Advanced Recurrence Schedule"}

## The "icalendar-recurrence" Grouping

   {{ex-9}} indicates 10 occurrences that occur at
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
{: #ex-9 title="Simple iCalendar Recurrence"}

   {{ex-10}} is an example of a recurrence that occurs on the last
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
{: #ex-10 title="Example of Advanced iCalendar Recurrence"}

  {{ex-11}} indicates a recurrence that occur every 20
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
{: #ex-11 title="Example of Advanced iCalendar Recurrence with Exceptions"}


# Examples of Using/Extending the "ietf-schedule" Module {#sec-ext}

   This non-normative section shows two examples for how the "ietf-schedule" module
   can be used or extended for scheduled events or attributes based on date and time.

## Example: Schedule Tasks to Execute Based on a Recurrence Rule {#features}

   Scheduled tasks can be used to execute specific actions based on certain recurrence rules (e.g.,
   every Friday at 8:00 AM). The following example module which "uses" the "icalendar-recurrence"
   grouping from "ietf-schedule" module shows how a scheduled task could be defined
   with different features used for options.

~~~~
{::include-fold ./yang/example-scheduled-backup.yang}
~~~~

## Example: Schedule Network Properties to Change Based on Date and Time {#augments}

   Network properties may change over a specific period of time or based on a
   recurrence rule, e.g., {{?I-D.ietf-tvr-use-cases}}.
   The following example module which augments the "recurrence-with-date-times"
   grouping from "ietf-schedule" module shows how a scheduled based attribute
   could be defined.

~~~~
{::include-fold ./yang/example-scheduled-link-bandwidth.yang}
~~~~

  {{ex-12}} shows a configuration example of a link's bandwidth that is
  scheduled between 2025-12-01 0:00 UTC to the end of 2025-12-31 with a daily
  schedule. In each day, the bandwidth value is scheduled to be 500 Kbps between
  1:00 AM to 6:00 AM and 800 Kbps between 10:00 PM to 11:00 PM. The bandwidth
  value that's not covered by the period above is 1000 Kbps by default.

~~~~
{::include-fold ./examples/example-scheduled-link-bandwidth.xml}
~~~~
{: #ex-12 title="Example of Scheduled Link's Bandwidth"}


# Acknowledgments
{:numbered="false"}

   This work is derived from the {{?I-D.ietf-opsawg-ucl-acl}}. There is a desire
   from the OPSAWG to see this model be separately defined for wide use in scheduling context.

   Thanks to Adrian Farrel, Wei Pan, Tianran Zhou, Joe Clarke, and Dhruv Dhody
   for their valuable comments and inputs to this work.

   Many thanks to the authors of {{?I-D.united-tvr-schedule-yang}}, {{?I-D.contreras-opsawg-scheduling-oam-tests}}, and {{?I-D.ietf-netmod-eca-policy}}
   for the constructive discussion during IETF#118.
