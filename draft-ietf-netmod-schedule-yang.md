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
   the module includes a set of recurrence related groupings with varying levels of complexity
   (i.e., from basic to advanced). It also defines groupings for validating requested
   schedules and reporting scheduling status.

--- middle

# Introduction {#intro}

This document defines a common schedule YANG module ("ietf-schedule") that can
be used in several scheduling contexts, e.g., (but not limited to)
{{?I-D.ietf-opsawg-ucl-acl}}, {{?I-D.contreras-opsawg-scheduling-oam-tests}},
and {{?I-D.ietf-tvr-schedule-yang}}. The module includes a set of reusable groupings which
are designed to be applicable for scheduling purposes such as event, policy,
services or resources based on date and time. It also defines groupings for validating
requested schedules and reporting scheduling status.

This document does not make any assumption about the nature of actions that are
triggered by the schedules. Detection and resolution of any schedule conflicts
are beyond the scope of this document.

{{sec-mib}} discusses the relationship with the Management Information Base (MIB)
managed objects for scheduling management operations defined in {{!RFC3231}}.

{{usage}} describes a set of examples to illustrate the use of the common schedule groupings ({{sec-grp}}).
And {{sec-ext}} provides sample modules to exemplify how future modules can use the extensibility
provisions in "ietf-schedule" ({{sec-schedule}}). Also, {{ex-framework}} provides
an example of using "ietf-schedule" module for scheduled use of resources framework (e.g., {{?RFC8413}}).

## Editorial Note (To be removed by RFC Editor)

   Note to the RFC Editor: This section is to be removed prior to publication.

   This document contains placeholder values that need to be replaced with finalized
   values at the time of publication.  This note summarizes all of the
   substitutions that are needed.  No other RFC Editor instructions are specified
   elsewhere in this document.

   Please apply the following replacements:

   * XXXX --> the assigned RFC number for this draft
   * 2025-01-27 --> the actual date of the publication of this document

# Conventions and Definitions

{::boilerplate bcp14-tagged}

   The meanings of the symbols in tree diagrams are defined in
   {{?RFC8340}}.

This document uses the YANG terminology defined in {{Section 3 of !RFC7950}}.

The document makes use of the following terms:

Recurrence Rule:
: Refers to a rule or repeating pattern for recurring events. See also {{Section 3.8.5.3 of !RFC5545}} for a comprehensive iCalendar recurrence rule specification.

Frequency:
: Characterizes the type of a recurrence rule. Values are taken from "FREQ" rule in {{Section 3.3.10 of !RFC5545}}.
: For example, repeating events based on an interval of a second or more are
  classified as recurrence with a frequency value of "SECONDLY". Frequency values defined as identities
  in the YANG module are used in lowercase.

iCalendar:
: Refers to Internet Calendaring per {{!RFC5545}}.

Interval:
: Refers to an integer that specifies at which interval a recurrence rule repeats. Values are taken from "INTERVAL" rule in {{Section 3.3.10 of !RFC5545}}.
: For example, "1", means every second for a secondly rule, every minute for a minutely rule, every hour for an hourly rule, etc.

System:
: Refers to an entity that hosts a schedule that is managed using the YANG module defined in this document.

#  Module Overview {#sec-overview}

##  Features {#sec-features}

   The "ietf-schedule" data model defines the recurrence related groupings using
   a modular approach. To that aim, a variety of representations of recurrence
   groupings ranging from basic to advanced (iclander like) are defined.
   To allow for different options, two features are defined in the data model:

   *  "basic-recurrence"
   *  "icalendar-recurrence"

   Refer to Sections {{sec-aug}} and {{features}} for the use of these features.

##  Types and Identities {#sec-types}

   The "ietf-schedule" module ({{sec-schedule}}) defines the following identities:

   * "schedule-type": Indicates the type of a schedule. The following types are defined so far:
      + one-shot: The schedule will trigger an action without the duration/end time being
       specified and then the schedule will disable itself ({{Section 3.3 of !RFC3231}}).
      + period: The schedule is a period-based schedule consisting either a start and end or a start and positive duration of time.
      + recurrence: This type is used for a recurrence-based schedule. A recurrence may be periodic (i.e., repeat over the same period, e.g., every five minutes) or not (i.e., repeat in a non-regular manner, e.g., every day at 8 and 9 AM).
   * "frequency-type": Characterizes the repeating interval rule of a recurrence schedule (secondly, minutely, etc.).
   * "schedule-state": Indicates the status of a schedule (enabled, disabled, conflicted, finished, etc.). This identity can also be used
     to manage the state of individual instances of a recurrence-based schedule.
   * "discard-action-type": Specifies the action for the responder to take (e.g., generate a warning or an error message) when
     a requested schedule cannot be accepted for any reason and is discarded.

##  Scheduling Groupings {#sec-grp}

   The "ietf-schedule" module ({{sec-schedule}}) defines the following groupings:

   * "generic-schedule-params" ({{sec-gen}})
   * "period-of-time" ({{sec-period}})
   * "recurrence-basic" ({{sec-rec}})
   * "recurrence-utc" ({{sec-rec-utc}})
   * "recurrence-with-time-zone" ({{sec-rec-tz}})
   * "recurrence-utc-with-date-times" ({{sec-rec-utc-dt}})
   * "recurrence-time-zone-with-date-times" ({{sec-rec-tz-dt}})
   * "icalendar-recurrence" ({{sec-ical-rec}})
   * "schedule-status" and "schedule-status-with-name" ({{sec-schedule-status}})

<!--
   {{schedule-tree}} provides an overview of the tree structure of
   the "ietf-schedule" module in terms of its groupings.

~~~~
{::include ./yang/tree/overall.txt}
~~~~
{: #schedule-tree title="Overall Schedule Tree Structure"}

   Each of these groupings is presented in the following subsections.
-->

   Examples are provided in {{usage}}.

### The "generic-schedule-params" Grouping {#sec-gen}

   A system accepts and handles schedule requests, which may help further
   automate the scheduling process of events, policy, services, or resources
   based on date and time. The "generic-schedule-params" grouping ({{gsp-tree}})
   specifies a set of configuration parameters that are used by a system for
   validating requested schedules.

~~~~
{::include ./yang/tree/sch-generic-params.txt}
~~~~
{: #gsp-tree title="Generic Schedule Configuration Tree Structure"}

   The "description" includes a description of the schedule. No constraint is imposed
   on the structure nor the use of this parameter.

   The "time-zone-identifier" parameter, if provided, specifies the
   time zone reference {{!RFC7317}} of the local date and time values.

   The "validity" parameter specifies the date and time after which a schedule
   will not be considered as valid. It determines the latest time that a schedule
   can be started to execute by a system and takes precedence over similar attributes
   that are provided at the schedule instance itself.

   The "max/min-allowed-start" parameters specify the maximum/minimum scheduled
   start date and time, a requested schedule will be rejected if the first
   occurrence of the schedule starts later/earlier than the configured values.

   The "max-allowed-end" parameter specifies the maximum allowed end time of
   the last occurrence. A requested schedule will be rejected if the end time
   of last occurrence starts later than the configured "max-allowed-end" value.

   The "discard-action" parameter specifies the action if a requested schedule
   cannot be accepted for any reason and is discarded. Possible reasons include,
   but are not limited to, the requested schedule failing to satisfy the guards in this grouping,
   conflicting with existing schedules, or being out-of-date.

   These parameters apply to all schedules on a system and are meant
   to provide guards against stale configuration, too short schedule requests
   that would prevent validation by admins of some critical systems, etc.

### The "period-of-time" Grouping {#sec-period}

   The "period-of-time" grouping ({{pt-tree}}) represents a time period using
   either a start date and time ("period-start") and end date and time ("period-end"), or a
   start date and time ("period-start") and a non-negative time duration ("duration"). For the first
   format, the start of the period MUST be no later the end of the period. If neither an end date and time
   nor a duration is indicated, the period is considered to last forever or as a one-shot schedule.

   The "period-description" includes a description of the period. No constraint is imposed
   on the structure nor the use of this parameter.

~~~~
{::include ./yang/tree/period-grp.txt}
~~~~
{: #pt-tree title="Period of Time Grouping Tree Structure"}

### The "recurrence-basic" Grouping {#sec-rec}

  The "recurrence-basic" grouping ({{rec-grp-tree}}) specifies a simple recurrence rule which starts immediately and repeats forever.

~~~~
{::include ./yang/tree/rec-grp.txt}
~~~~
{: #rec-grp-tree title="recurrence Grouping Tree Structure"}

  The frequency parameter ("frequency") which is mandatory, identifies the type of recurrence rule. For example,
  a "daily" frequency value specifies repeating events based on an interval of a day or more.

  Consistent with {{Section 3.3.10 of !RFC5545}}, the interval ("interval") represents at which interval the recurrence rule repeats. For example,
  within a daily recurrence rule, an interval value of "8" means every eight days.
  The default value is "1", meaning every second for a secondly recurrence rule,
  every minute for a minutely rule, every hour for an hourly rule, every day for a
  daily rule, and so on. Note that per {{Section 4.13 of ?I-D.ietf-netmod-rfc8407bis}}, no "default" substatement is used here because there are cases (e.g., profiling) where the use of the default is problematic.

  The "recurrence-description" includes a description of the period. No constraint is imposed
  on the structure nor the use of this parameter.

### The "recurrence-utc" Grouping {#sec-rec-utc}

   The "recurrence-utc" grouping ({{rec-utc-grp-tree}}) uses the "recurrence-basic"
   grouping and specifies a simple recurrence rule in UTC format.

~~~~
{::include ./yang/tree/rec-utc-grp.txt}
~~~~
{: #rec-utc-grp-tree title="recurrence-utc Grouping Tree Structure"}

   The "start-time-utc" indicates the start time in UTC format.

   The "duration" parameter specifies, in units of seconds, the time period of the first occurrence. Unless specified otherwise (e.g., described in the "description" statement), the "duration" also applies to subsequent recurrence instances. When unspecified, each occurrence is considered as
   immediate completion or hard to compute an exact duration.


Note that the "interval" and "duration" cover two distinct properties of a schedule event.
The interval specifies when a schedule will occur, combined with the frequency parameter; while the duration indicates how long
an occurrence will last. This document allows the interval between occurrences is shorter than the duration of each occurrence, e.g., a recurring event is scheduled to start every day for a duration of 2 days.

  The repetition can be scoped by a specified end time or by a count of occurrences,
  indicated by the "recurrence-end" choice. The value of the "count" node MUST be greater than 1, the "start-time-utc" value always counts
  as the first occurrence.

   The "recurrence-utc" grouping is designed to be reused in scheduling contexts
   where machine readability is more desirable.

### The "recurrence-with-time-zone" Grouping {#sec-rec-tz}

   The "recurrence-with-time-zone" grouping ({{rec-tz-grp-tree}}) uses the
   "recurrence-basic" grouping and specifies a simple recurrence rule with a time zone.

~~~~
{::include ./yang/tree/rec-tz-grp.txt}
~~~~
{: #rec-tz-grp-tree title="recurrence-with-time-zone Grouping Tree Structure"}

   The "recurrence-first" container includes "start-time" and "duration" parameters
   to specify the start time and period of the first occurrence. Unless specified otherwise (e.g., described in the "description" statement), the
   "duration" also applies to subsequent recurrence instances. When unspecified, each occurrence is considered as
   immediate completion or hard to compute an exact duration. It also includes a
   "time-zone-identifier" parameter which MUST be specified if the date
   and time value is neither reported in the format of UTC nor time zone offset
   to UTC.

  The repetition can be scoped by a specified end time or by a count of occurrences,
  indicated by the "recurrence-end" choice. The value of the "count" node MUST be greater than 1, the "start-time" value always counts
  as the first occurrence.

   Unlike the definition of "recurrence-utc" grouping ({{sec-rec-utc}}),
   "recurrence-with-time-zone" is intended to promote human readability over
   machine readability.

### The "recurrence-utc-with-date-times" Grouping {#sec-rec-utc-dt}

   The "recurrence-utc-with-date-times" grouping ({{rec-utc-dt-grp-tree}}) uses
   the "recurrence-utc" grouping ({{sec-rec-utc}}) and adds a "period-timeticks"
   list to define an aggregate set of repeating occurrences.

~~~~
{::include ./yang/tree/rec-utc-dt-grp.txt}
~~~~
{: #rec-utc-dt-grp-tree title="recurrence-utc-with-date-times Grouping Tree Structure"}

  The recurrence instances are specified by the union of occurrences defined
  by both the recurrence rule and "period-timeticks" list. Duplicate instances
  are ignored. The value of the "period-start" instance MUST NOT exceed the
  value indicated by the value of "frequency" instance, i.e., the timeticks
  value must not exceed 100 in a secondly recurrence rule, and it must not
  exceed 6000 in a minutely recurrence rule, and so on.

### The "recurrence-time-zone-with-date-times" Grouping {#sec-rec-tz-dt}

  The "recurrence-time-zone-with-date-times" grouping ({{rec-tz-dt-grp-tree}}) uses
  the "recurrence-with-time-zone" grouping ({{sec-rec-tz}}) and
  adds a "period" list to define an aggregate set of repeating occurrences.

~~~~
{::include ./yang/tree/rec-tz-dt-grp.txt}
~~~~
{: #rec-tz-dt-grp-tree title="recurrence-time-zone-with-date-times Grouping Tree Structure"}

  The recurrence instances are specified by the union of occurrences defined
  by both the recurrence rule and "period" list. Duplicate instances
  are ignored.

### The "icalendar-recurrence" Grouping {#sec-ical-rec}

  The "icalendar-recurrence" grouping ({{ical-grp-tree}}) uses the
  "recurrence-time-zone-with-date-times" grouping ({{sec-rec-tz-dt}}) and define
  more data nodes to enrich the definition of recurrence. The structure of the
  "icalendar-recurrence" grouping refers to the definition of recurrence
  component defined in {{Sections 3.3.10 and 3.8.5 of !RFC5545}}.

~~~~
{::include ./yang/tree/ical-grp.txt}
~~~~
{: #ical-grp-tree title="icalendar-recurrence Grouping Tree Structure"}

   An array of the "bysecond" (or "byminute", "byhour") specifies a list of
   seconds within a minute (or minutes within an hour, hours of the day). For
   example, within a "minutely" recurrence rule, the values of "byminute" node
   "10" and "20" means the occurrences generates at the 10th and 20th minute
   within an hour, reducing the number of recurrence instances from all minutes.

   The parameter "byday" specifies a list of days of the week, with an optional
   direction which indicates the nth occurrence of a specific day within
   the "monthly" or "yearly" frequency instance. Valid values of "direction" are 1 to 5 or -5 to -1 within a "monthly" recurrence rule; and 1 to 53 or -53 to -1 within a "yearly" recurrence rule. For example, within a "monthly" rule,
   the "weekday" with a value of "monday" and the "direction" with a value of "-1"
   represents the last Monday of the month.

   An array of the "bymonthday" (or byyearday", "byyearweek", or "byyearmonth") specifies a list of
   days of the month (or days of the year, weeks of the year, or months of the year).
   For example, within a "yearly" recurrence rule, the values of "byyearmonth"
   instance "1" and "2" means the occurrences generates in January and February,
   increasing the "yearly" recurrence from every year to every January and February
   of the year.

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

### The "schedule-status" and "schedule-status-with-name" Groupings {#sec-schedule-status}

   The "schedule-status" and "schedule-status-with-name" groupings ({{sche-status-tree}}) define common parameters
   for scheduling management/status exposure. The "schedule-status-with-name" grouping has the same
   structure as "schedule-status" but with an additional parameter to identify a schedule "schedule-id". Both
   structures are defined in the module to allow for better modularity and flexibility.

~~~~
{::include ./yang/tree/schedule-status.txt}
~~~~
{: #sche-status-tree title="Schedule Status with and without Name Groupings Tree Structure"}

   The "schedule-id" parameter is useful to uniquely identify a schedule in
   a network device or controller if multiple scheduling contexts exists.

   The "state" parameter is defined to configure/expose the scheduling state,
   depending on the use of the grouping. For a recurrence-based schedule, it
   represents the state of the overall recurrence. The "identityref" type is used for this
   parameter to allow extensibility in future modules.

   The "version" parameter is used to track the current schedule version
   information. The version can be bumped by the entity who create the schedule.
   The "last-update" parameter identifies when the schedule was last modified.
   In some contexts, this parameter can be used to track the configuration of a
   given schedule. In such cases, the "version" may not be used.

   The "schedule-type" parameter identifies the type of the current schedule.
   The "counter", "last-occurrence", and "upcoming-occurrence" data nodes are
   only avaliable when the "schedule-type" is "recurrence".

   "local-time" reports the actual local time as seen by the entity that
   host a schedule. This paramter can be used by a controller to infer the offset to UTC.

   "last-failed-occurrence" and "failure-counter" report the last failure that occurred and
   the count of failures for this schedule. Unless new parameters/operations are defined to allow the count of failures to be reset,
   "failure-counter" is reset by default only when the schedule starts.

   The current groupings capture common parameters that are applicable
   to typical scheduling contexts known so far. Future modules can define other
   useful parameters as needed. For example, in a scheduling context with multiple
   system sources to feed the schedules, the "source" and "precedence" parameters
   may be needed to reflect how schedules from different sources should be prioritised.

##  Features Use and Augmentations {#sec-aug}

   {{features}} provides an example about how the features defined in {{sec-features}} can be used. Implementations
   may support a basic recurrence rule or an advanced one as needed, by declaring
   different features. Whether only one or both features are supported is implementation
   specific and depend on specific scheduling context.

   The common schedule groupings ({{sec-grp}}) can also be augmented to support specific needs. As an example,
   {{augments}} demonstrates how additional parameters can be added to comply with specifc schedule needs.

#  Some Usage Restrictions

   There are some restrictions that need to be followed when using groupings defined
   in the "ietf-schedule" YANG module ({{sec-grp}}):

   *  The instant in time represented by "period-start" MUST be before the
      "period-end" for "period-of-time" grouping ({{sec-period}}).
   *  The combination of the day, month, and year represented for date and time
      values MUST be valid. See {{Section 5.7 of ?RFC3339}} for the maxinum day
      number based on the month and year.
   *  The second for date and time values MUST have the value "60" at the end of months in which a leap
      second occurs.
   *  Schedules received with a starting time in the past with respect to
      current time SHOULD be ignored. Some implementation MAY omit the past occurrences and
      start immediately (e.g., for a period-based schedule) or starts from the
      date and time when the recurrence pattern is first satisfied from the current time (e.g., for a recurrence-based schedule).

# Relationship to the DISMAN-SCHEDULE-MIB {#sec-mib}

{{!RFC3231}} specifies a Management Information Base (MIB) used to
schedule management operations periodically or at specified dates and times.

Although no data nodes are defined in this document, {{mapping}} lists
how the main objects in the DISMAN-SCHEDULE-MIB can be mapped to YANG
parameters.


|MIB Object|	YANG|
|----------|---|
|schedLocalTime| local-time |
|schedType | schedule-type|
|schedName| schedule-id |
|schedOwner| Not Supported  |
|schedDescr| description |
|schedInterval|interval   |
|schedWeekDay| weekday  |
|schedMonth|  byyearmonth |
|schedDay| bymonthday  |
|schedHour|  byhour|
|schedMinute| byminute  |
|schedContextName| Not Supported   |
|schedAdminStatus|   state|
|schedOperStatus|  state|
|schedFailures| failure-counter |
|schedLastFailure| Not Supported   |
|schedLastFailed|   last-failed-occurrence|
|schedStorageType|  Not Supported    |
|schedVariable| Not applicable |
|schedValue| Not applicable |
|schedTriggers|  counter/failure-counter   |
{: #mapping title="YANG/MIB Mapping"}

#  The "ietf-schedule" YANG Module {#sec-schedule}

   This module imports types defined in {{!RFC6991}} and {{!RFC7317}}.

~~~~
<CODE BEGINS> file "ietf-schedule@2024-04-16.yang"
{::include-fold ./yang/ietf-schedule.yang}
<CODE ENDS>
~~~~

# Security Considerations

   This section is modeled after the template described in Section 3.7 of {{?I-D.ietf-netmod-rfc8407bis}}.

   The "ietf-schedule" YANG module specified in this document defines schema for data
   that is designed to be accessed via YANG-based management protocols, such
   as NETCONF {{?RFC6241}} or RESTCONF {{?RFC8040}}.  These protocols have to use
   a secure transport layer (e.g., SSH {{?RFC4252}}, TLS {{?RFC8446}}, and QUIC {{?RFC9000}})
   and have to use mutual authentication.

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

   Modules that use the groupings that are defined in this document
   should identify the corresponding security considerations. For
   example, reusing some of these groupings will expose privacy-related
   information (e.g., 'node-example').

   Care must be taken when defining recurrences occurring very often and
   frequent that can be an additional source of attacks by keeping the system
   permanently busy with the management of scheduling.

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
        maintained by IANA? N
        reference:          RFC XXXX
~~~~

--- back

# Examples of Scheduling Format Representation {#usage}

   This section provides some examples to illustrate the use of the
   period and recurrence formats defined in {{sec-schedule}}. The following
   modules are used for illustration purposes:

~~~~
{::include-fold ./yang/example-sch-usage.yang}
~~~~

   For each example, only the message body is provided with
   JSON used for encoding per the guidance in {{?RFC7951}}.

## The "generic-schedule-params" Grouping

{{ex-0}} illustrates the example of a requested schedule that needs to start no earlier than
08:00 AM, January 1, 2025 and end no later than 8:00 PM, January 31, 2025 (Beijing time).
Schedule requests that fail to meet the requirements are ignored by the system as indicates by
"discard-action".

~~~~
{
  "example-sch-usage-1:generic-schedule-params": {
    "time-zone-identifier": "China/Beijing",
    "min-allowed-start": "2025-01-01T08:00:00",
    "max-allowed-end": "2025-01-31T20:00:00",
    "discard-action": "ietf-schedule:silently-discard"
  }
}
~~~~
{: #ex-0 title="Generic Parameters with 'max-allowed-end' for Schedule Validation"}

To illustrate the difference between "max-allowed-end" and "validity" parameters,
{{ex-00}} shows the example of a requested schedule that needs to start no earlier than
08:00 AM, January 1, 2025 (Paris time). Schedule requests that fail to meet the
requirements are discarded with warning messages. The requested schedule may end
after 8:00 PM, January 31, 2025, but any occurrences that are generated
after that time would not be considered as valid.

~~~~
{
  "example-sch-usage-1:generic-schedule-params": {
    "time-zone-identifier": "France/Paris",
    "validity": "2025-01-31T20:00:00",
    "min-allowed-start": "2025-01-01T08:00:00",
    "discard-action": "ietf-schedule:warning"
  }
}
~~~~
{: #ex-00 title="Generic Parameters with 'validity' for Schedule Validation"}

## The "period-of-time" Grouping

   {{ex-1}} shows an example of a period that starts at 08:00:00 UTC, on January 1, 2025 and ends at 18:00:00 UTC
   on December 31, 2027.

~~~~
{
  "example-sch-usage-2:period-of-time": {
    "period-start": "2025-01-01T08:00:00Z",
    "period-end": "2027-12-31T18:00:00Z"
  }
}
~~~~
{: #ex-1 title="Simple Start/End Schedule"}

   An example of a period that starts at 08:00:00 UTC, on January 1, 2025 and lasts 15 days and
   5 hours and 20 minutes is encoded as shown in {{ex-2}}.

~~~~
{
  "example-sch-usage-2:period-of-time": {
    "period-start": "2025-01-01T08:00:00Z",
    "duration": "P15DT05:20:00"
  }
}
~~~~
{: #ex-2 title="Simple Schedule with Duration"}

   An example of a period that starts at 2:00 A.M. in Los Angeles on November 19,
   2025 and lasts 20 weeks is depicted in {{ex-3}}.

~~~~
{
  "example-sch-usage-2:period-of-time": {
    "period-start": "2025-11-19T02:00:00",
    "time-zone-identifier": "America/Los_Angeles",
    "duration": "P20W"
  }
}
~~~~
{: #ex-3 title="Simple Schedule with Time Zone Indication"}

## The "recurrence-basic" Grouping

   {{ex-6}} indicates a recurrence of every 2 days which starts immediately and repeats forever:

~~~~
{
  "example-sch-usage-3:recurrence-basic": {
    "recurrence-description": "forever recurrence rule",
    "frequency": "ietf-schedule:daily",
    "interval": 2
  }
}
~~~~
{: #ex-4 title="Simple Schedule with Recurrence"}

## The "recurrence-utc" Grouping

   {{ex-5}} indicates a recurrence from 8:00 AM to 9:00 AM every day, from
   December 1 to December 31, 2025 in UTC:

~~~~
{
  "example-sch-usage-4:recurrence-utc": {
    "recurrence-first": {
      "start-time-utc": "2025-12-01T08:00:00Z",
      "duration": 3600
    },
    "frequency": "ietf-schedule:daily",
    "interval": 1,
    "utc-until": "2025-12-31T23:59:59Z"
  }
}
~~~~
{: #ex-5 title="Simple Schedule with Recurrence in UTC"}

## The "recurrence-with-time-zone" Grouping

   {{ex-6}} indicates a recurrence of every 2 hours for 10 occurrences, lasting
   10 minutes, and starting at 3 p.m. on December 1, 2025 in New York:

~~~~
{
  "example-sch-usage-5:recurrence-with-time-zone": {
    "recurrence-first": {
      "start-time": "2025-12-01T15:00:00",
      "duration": "PT00:10:00",
      "time-zone-identifier": "America/New_York"
    },
    "frequency": "ietf-schedule:hourly",
    "interval": 2,
    "count": 10
  }
}
~~~~
{: #ex-6 title="Simple Schedule with Recurrence with Time Zone Indication"}

## The "recurrence-utc-with-date-times" Grouping

   {{ex-7}} indicates a recurrence that occurs every two days starting at 9:00 AM
   and 3:00 PM for a duration of 30 minutes and 40 minutes respectively,
   from 2025-06-01 to 2025-06-30 in UTC:

~~~~
{
  "example-sch-usage-6:recurrence-utc-with-date-times": {
    "recurrence-first": {
      "start-time-utc": "2025-06-01T09:00:00Z"
    },
    "frequency": "ietf-schedule:daily",
    "interval": 2,
    "utc-until": "2025-06-30T23:59:59Z",
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
}
~~~~
{: #ex-7 title="Example of Recurrence With Date Times"}

## The "recurrence-time-zone-with-date-times" Grouping

   {{ex-8}} indicates a recurrence that occurs every
   30 minutes and last for 15 minutes from 9:00 AM to 5:00 PM, and extra two occurrences
   at 6:00 PM and 6:30 PM with each lasting for 20 minutes on 2025-12-01 (New York):

~~~~
{
  "example-sch-usage-7:recurrence-time-zone-with-date-times": {
    "recurrence-first": {
      "start-time": "2025-12-01T09:00:00",
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
}
~~~~
{: #ex-8 title="Example of Advanced Recurrence Schedule"}

## The "icalendar-recurrence" Grouping

   {{ex-9}} indicates 10 occurrences that occur at
   8:00 AM (EST), every last Saturday of the month starting in January 2024:

~~~~
{
  "example-sch-usage-8:icalendar-recurrence": {
    "recurrence-first": {
      "start-time": "2024-01-27T08:00:00",
      "time-zone-identifier": "America/New_York"
    },
    "frequency": "ietf-schedule:monthly",
    "count": 10,
    "byday": [
      {
        "direction": [
          -1
        ],
        "weekday": "saturday"
      }
    ]
  }
}
~~~~
{: #ex-9 title="Simple iCalendar Recurrence"}

   {{ex-10}} is an example of a recurrence that occurs on the last
   workday of the month until December 25, 2025, from January 1, 2025:

~~~~
{
  "example-sch-usage-8:icalendar-recurrence": {
    "recurrence-first": {
      "start-time": "2025-01-01"
    },
    "frequency": "ietf-schedule:monthly",
    "until": "2025-12-25",
    "byday": [
      {
        "weekday": "monday"
      },
      {
        "weekday": "tuesday"
      },
      {
        "weekday": "wednesday"
      },
      {
        "weekday": "thursday"
      },
      {
        "weekday": "friday"
      }
    ],
    "bysetpos": [
      -1
    ]
  }
}
~~~~
{: #ex-10 title="Example of Advanced iCalendar Recurrence"}

   {{ex-11}} indicates a recurrence that occurs every 20
   minutes from 9:00 AM to 4:40 PM (UTC), with the occurrence starting at 10:20 AM
   being excluded on 2025-12-01:

~~~~
{
  "example-sch-usage-8:icalendar-recurrence": {
    "recurrence-first": {
      "start-time": "2025-12-01T09:00:00Z"
    },
    "until": "2025-12-01T16:40:00Z",
    "frequency": "ietf-schedule:minutely",
    "byminute": [
      0,
      20,
      40
    ],
    "byhour": [
      9,
      10,
      11,
      12,
      13,
      14,
      15,
      16
    ],
    "exception-dates": [
      "2025-12-01T10:20:00Z"
    ]
  }
}
~~~~
{: #ex-11 title="Example of Advanced iCalendar Recurrence with Exceptions"}

## The "schedule-status" Grouping

   {{ex-12}} indicates the scheduled recurrence status of {{ex-11}} at the time
   of 12:15 PM, 2025-12-01 (UTC):

~~~~
{
  "example-sch-usage-1:schedule-status": {
    "state": "ietf-schedule:enabled",
    "version": 1,
    "schedule-type": "ietf-schedule:recurrence",
    "counter": 9,
    "last-occurrence": [
      "2025-12-01T12:00:00Z"
    ],
    "upcoming-occurrence": [
      "2025-12-01T12:20:00Z"
    ]
  }
}
~~~~
{: #ex-12 title="Example of a Schedule Status"}

  At the time of 12:15 PM, 2025-12-01 (UTC), the recurring event occurred at
  (note that occurrence at 10:20 AM is excluded):
  9:00, 9:20, 9:40, 10:00, 10:40, 11:00, 11:20, 11:40, 12:00.
  The last occurrence was at 12:00, the upcoming one is at 12:20.

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
   recurrence rule, e.g., {{?RFC9657}}.
   The following example module which augments the "recurrence-utc-with-date-times"
   grouping from "ietf-schedule" module shows how a scheduled based attribute
   could be defined.

~~~~
{::include-fold ./yang/example-scheduled-link-bandwidth.yang}
~~~~

  {{ex-13}} shows a configuration example of a link's bandwidth that is
  scheduled between 2025-12-01 0:00 UTC to the end of 2025-12-31 with a daily
  schedule. In each day, the bandwidth value is scheduled to be 500 Kbps between
  1:00 AM to 6:00 AM and 800 Kbps between 10:00 PM to 11:00 PM. The bandwidth
  value that's not covered by the period above is 1000 Kbps by default.

~~~~
{::include-fold ./examples/example-scheduled-link-bandwidth.xml}
~~~~
{: #ex-13 title="Example of Scheduled Link's Bandwidth"}

# Examples of Using "ietf-schedule" Module for Scheduled Use of Resources Framework {#ex-framework}

   This section exemplifies how the architecture for supporting scheduled
   reservation of Traffic Engineering (TE) resources in {{?RFC8413}} might leverage the "period-of-time"
   grouping defined in the "ietf-schedule" module to implement scheduled use of
   resources.

   The following example module shows how a scheduled link capacity reservation
   could be defined.

~~~~
{::include-fold ./yang/example-scheduled-capacity-reservation.yang}
~~~~

   {{Section 4 of ?RFC8413}} defines the reference architecture for scheduled use
   of resources, the service requester sends a request to a Path Computation Element (PCE) and includes the
   parameters of the Label Switched Path (LSP) that the requester wishes to supply, the configuration
   example to provide the scheduled resource is shown in {{ex-14}}.

~~~~
{::include-fold ./examples/example-scheduled-capacity-reservation.xml}
~~~~
{: #ex-14 title="Example of Scheduled Link's Bandwidth Reservation"}

# Acknowledgments
{:numbered="false"}

   This work is derived from the {{?I-D.ietf-opsawg-ucl-acl}}. There is a desire
   from the OPSAWG to see this model be separately defined for wide use in scheduling context.

   Thanks to Adrian Farrel, Wei Pan, Tianran Zhou, Joe Clarke, Steve Baillargeon, and Dhruv Dhody
   for their valuable comments and inputs to this work.

   Many thanks to the authors of {{?I-D.ietf-tvr-schedule-yang}}, {{?I-D.contreras-opsawg-scheduling-oam-tests}}, and {{?I-D.ietf-netmod-eca-policy}}
   for the constructive discussion during IETF#118.

   Other related efforts were explored in the past, e.g., {{?I-D.liu-netmod-yang-schedule}}.

   Thanks to Reshad Rahman for the great YANGDOCTORS review.
