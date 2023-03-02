---
title: "A Policy-based Network Access Control"
abbrev: "A Policy-based NACL"
category: std

docname: draft-ma-opsawg-ucl-acl-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "Operations and Management"
workgroup: "OPSAWG"
keyword:
 - ACL
 - Policy-based
 - BYOD
 - Access control

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
    organization: Old Dog Consulting
    country: United Kingdom
    email: daniel@olddog.co.uk

normative:

informative:
  NIST-ABAC:
     title: Guide to Attribute Based Access Control (ABAC) Definition and Considerations
     author:
       -
         fullname: Vincent C. Hu
     target: https://www.iana.org/assignments/media-types
     date: January 2014

  RADIUS-Types:
     title: RADIUS Types
     author:
       -
         organization: "IANA"
     target: http://www.iana.org/assignments/radius-types
     date: false

--- abstract

   This document defines a YANG module for policy-based network access
   control, which provides consistent and efficient enforcement of
   network access control policies based on group identity.  In
   addition, this document defines a mechanism to ease the maintenance
   of the mapping between a user-group ID and a set of IP/MAC addresses
   to enforce policy-based network access control.

   Also, the document defines a common schedule YANG module which is
   designed to be applicable for policy activation based on date and
   time conditions.

   In addition, the document defines a RADIUS attribute that is used to
   communicate the user group identifier as part of identification and
   authorization information.

--- middle

# Introduction

   With the increased adoption of remote access technologies (e.g.,
   Virtual Private Networks (VPNs)) and Bring Your Own Device (BYOD)
   policies, enterprises adopted more flexibility related to how, where,
   and when employees work and collaborate.  However, more flexibility
   comes with increased risks.  Enabling office flexibility (e.g.,
   mobility across many access locations) for large-scale employees
   induces a set of challenges compared to conventional network access
   management approaches.  Examples of such challenges are listed below:

   *  Endpoints do not have a stable IP address.  For example, Wireless
      LAN (WLAN) and VPN clients, as well as back-end Virtual Machine
      (VM)-based servers, can move; their IP addresses could change as a
      result.  This means that relying on IP/transport fields (e.g., the
      5-tuple) is inadequate to ensure consistent and efficient security
      policy enforcement.  IP address-based policies may not be flexible
      enough to accommodate endpoints with volatile IP addresses.

   *  With the massive adoption of teleworking, there is now a need to
      apply different security policies to the same set of users under
      different circumstances (e.g., prevent relaying attacks against a
      local attachment point to the Enterprise network).  For example,
      network access might be granted based upon criteria such as users'
      access location, source network reputation, users' role, time-of-
      day, type of network device used (e.g., corporate issued device
      versus personal device), device's security posture, etc.  This
      means the network needs to recognize the users' identity and their
      current context, and map the users to their correct access
      entitlement to the network.

   This document defines a common schedule YANG module which is designed
   to be applicable for policy activation based on date and time
   condition.  This model can be used in other scheduling contexts.

   The document also defines a YANG module for policy-based Network
   Access Control, which extends the IETF Access Control
   Lists (ACLs) module defined in {{!RFC8519}}.  This module defined in
   {{sec-UCL}} is meant to ensure consistent enforcement of ACL policies
   based on the group identity. In addition, this document defines a
   mechanism to establish a mapping between the user-group ID and common
   IP packet headers and other enclosed packet data (e.g., MAC address)
   to execute the policy-based access control.

   Last, the document defines a RADIUS attribute that is used to
   communicate the user group identifier as part of identification and
   authorization information ({{sec-radius}}).

   As the ACL notion has been generalized, not to be device-specific,
   but also be defined at network/administrative domain
   levels {{?I-D.dbb-netmod-acl}}, the YANG module for policy-based network
   access control defined in this document does not limit how it can be
   used.


# Conventions and Definitions

{::boilerplate bcp14-tagged}

   The meanings of the symbols in tree diagrams are defined in
   {{?RFC8340}}.

   The document uses the terms defined in {{!RFC8519}}.

   In the current version of the draft, the term "endpoint" refers also
   to a host device or end user that actually connect to a network. While
   host device here refers to servers, IoTs and other devices owned by the
   enterprise.

#  Sample Usage

   Access to some networks (e.g., Enterprise Networks) requires to
   recognize the users' identities no matter how, where, and when they
   connect to the network resources.  Then, the network maps the
   (connecting) users to their access authorization rights.  Such rights
   are defined following local policies.  As discussed in the
   introduction, because there is a large number of users and the source
   IP addresses of the same user are in different network segments,
   deploying a network access control policy for each IP address or
   network segment is heavy workload.  An alternative approach is to
   configure endpoint groups to classify users and enterprise devices
   and associate ACLs with endpoint groups so that endpoint in each
   group can share a group of ACL rules.  This approach greatly reduces
   the workload of the administrators and optimizes the ACL resources.
   The Network ACLs (NACLs) can be provisioned on devices using specific
   mechanisms, such as {{!RFC8519}} or {{?I-D.dbb-netmod-acl}}.

   Network access control policies may need to vary over time.  For
   example, companies may restrict/grant employees access to specific
   resources (internal and/or external resources) during work hours,
   while another policy is adopted during off-hours and weekends.  A
   network administrator may also require to enforce traffic shaping
   (Section 2.3.3.3 of {{?RFC2475}}) and policing (Section 2.3.3.4 of
   {{?RFC2475}}) during peak hours in order not to affect other data
   services.

#  Policy-based Network Access Control

##  Overview {#overview}

   To provide real-time and consistent enforcement of access control
   policies, the following functional entities and interfaces are
   involved:

   *  A Service Orchestrator which coordinates the overall service,
      including security policies.  The service may be connectivity or
      any other resources that can be hosted and offered by a network.

   *  The SDN Controller is responsible for maintaining endpoint-group
      based ACLs and mapping the endpoint-group to the associated
      attributes information (e.g., IP/MAC address).  The SDN Controller
      also works as the Policy Decision Point (PDP) {{?RFC3198}} and pushes
      the required access control policies to relevant PEPs (Policy
      Enforcement Points) that need them.  A PDP is also known as
      "policy server" {{?RFC2753}}.

      The SDN Controller may interact with AAA (Authentication,
      Authorization and Accounting) server or NAS.

   *  A Network Access Server (NAS) entity which handles authentication
      requests.  The NAS interacts with an AAA server to complete user
      authentication using protocols like Remote Authentication Dial-in
      User Service (RADIUS) {{!RFC2865}}. When access is granted, the AAA
      server provides the group identifier (group ID) to which the user
      belongs when the user first logs onto the network. A new attribute
      is defined in {{sec-radius}}.

   *  The AAA server provides a collection of authentication, authorization
      and accounting functions and is responsible for centralized user
      information management. The AAA server is preconfigured with user
      credentials (e.g., user name and password), possible group identities
      and related user attributes (users may be divided into different
      groups based on different user attributes).

   *  The Policy Enforcement Point (PEP) {{?RFC3198}} is the central entity
      which is responsible for enforcing appropriate access control
      policies.  In some cases, A PEP may map incoming packets to their
      associated source or destination endpoint-group IDs, and acts on
      the endpoint-group ID based ACL policies, e.g., a NAS as the PEP
      or a group identifier could be carried in packet header (see
      Section 6.2.3 in {{?I-D.ietf-nvo3-encap}}).  While in other cases,
      the SDN controller maps group ID to the related common packet
      header and delivers IP/MAC address based ACL policies to the
      required PEPs.

      Multiple PEPs can be involved in a network.

      A PEP exposes a NETCONF interface to the SDN Controller {{!RFC6241}}.

   {{arch}} provides the overall architecture and procedure for policy-
   based access control management.

~~~~
                                         +------------+
                                         |Orchestrator|
                                         +------+-----+
       Service                                  | (Step 1)
      ------------------------------------------+-------------
      ------------------------------------------+-------------
       Network                                  |
                                 (Step 4)       |
       +-------+        +--------+     +--------+-----------+
       |User #1+--+     | AAA    |     |    SDN Controller  |
       +-------+  |     | Server +-----+        (PDP)       |
                  |     +----+---+     +--------+-----------+
                  |          |                  |
                  |          |           +---------------+(Step 5)
        (Step 2)  |          |(Step 3)   |               |
                  |          |           |               |
                  |        +-+-----------+---------------+------------+
                  |        | +----------------------+   +------------+|
       +-------+  +--------+ | Network Access Server|   |firewall,etc||
       |User #2+-----------+ |       (NAS)          |   +------------+|
       +-------+           | +----------------------+                 |
                           |                     (PEP)                |
                           +------------------------------------------+
~~~~
{: #arch title="An Architecture for Group-based Policy Management" artwork-align="center"}

   In reference to {{arch}}, the following typical flow is experienced:

   Step 1:  Administrators (or the Orchestrator) configure an SDN
      controller with network-level ACLs using the YANG module defined
      in {{sec-UCL}}. An example of this is provided in {{controller-ucl}}.

   Step 2:  When a user first logs onto the network, the user is
      required to be authenticated (e.g., using user name and password)
      at the NAS.

   Step 3:  The authentication request is then relayed to the AAA server
      using protocols like RADIUS {{!RFC2865}}. It is assumed that the
      AAA server has been appropriately configured to store user credentials,
      e.g., user name, password, group information and other user attributes.
      If the authentication request succeeds, the user is placed in a
      user-group which is returned to the network access server as the
      authentication result (see {{sec-radius}}).
      If the authentication fails, the user is not assigned any user-
      group, which also means that the user has no access; or the user
      is assigned a special group with very limited access permissions
      for the network (as a function of the local policy). ACLs are
      enforced so that flows from that IP address are discarded
      (or rate-limited) by the network.  In some implementations, AAA
      server can be integrated with an SDN controller.

   Step 4:  Either the AAA server or the NAS notify the SDN controller
      the mapping between the user-group ID and related common packet
      header attributes (e.g., IP/MAC address).

   Step 5:  Either group or IP/MAC address based access control policies
      are maintained on relevant PEPs under the controller's management.
      Whether the PEP enforces the group or IP/MAC address based ACL is
      implementation specific. Either type of ACL policies may exist on
      the PEP. {{PEP-ucl}} and {{PEP-acl}} elaborate on each case.

##  Endpoint Group

###  User Group

   The user-group ID is an identifier that represents the collective
   identity of a group of users.  It is determined by a set of
   predefined policy criteria (e.g., source IP address, geolocation
   data, time of day, or device certificate).  Users may be moved to
   different user-groups if their composite attributes, environment,
   and/or local enterprise policy change.

   A user is authenticated, and classified at the AAA server, and
   assigned to a user-group.  A user's group membership may change as
   aspects of the user change.  For example, if the user-group
   membership is determined solely by the source IP address, then a
   given user's user-group ID will change when the user moves to a new
   IP address that falls outside of the range of addresses of the
   previous user-group.

   This document does not make any assumption about how user groups are
   defined.  Such considerations are deployment specific and are out of
   scope.  However, and for illustration purposes, {{ug-example}} shows
   an example of how user-group definitions may be characterized. User-
   groups may share several common criteria.  That is, user-group
   criteria are not mutually exclusive.  For example, the policy
   criteria of user-groups R&D Regular and R&D BYOD may share the same
   set of users that belong to the R&D organization, and differ only in
   the type of clients (firm-issued clients vs. users' personal
   clients).  Likewise, the same user may be assigned to different user-
   groups depending on the time of day or the type of day (e.g.,
   weekdays versus weekends), etc.

| Group Name | Group ID | Group Role |
| R&D        |     10     |  R&D employees                 |
| R&D BYOD   |     11     |  Personal devices of R&D employees |
| Sales      |     20     |  Sales employees               |
| VIP        |     30     |  VIP employees                 |
{: #ug-example title='User-Group Example'}


###  Device Group

   The device-group ID is an identifier that represents the collective
   identity of a group of enterprise end devices.  An enterprise device
   could be an server that hosts applications or software that deliver
   services to enterprise users.  It could also be an enterprise IoT
   device that serve a limited purpose, e.g., a printer that allows
   users to scan, print and send emails. {{dg-example}} shows an example
   of how device-group definitions may be characterized.

   | Group Name | Group ID | Group Type |
   | Workflow   |     40     |  Workflow  resource servers   |
   | R&D Resource |     50     | R&D resource servers |
   |Sales Resource|     54     | Sales resource servers |
   {: #dg-example title='Device-Group Example'}


   Users accessing to enterprise device should be strictly controlled.
   Matching abstract device group ID instead of specified addresses in
   ACL polices helps shield the consequences of address change (e.g.,
   back-end Virtual Machine (VM)-based server migration).

#  YANG Modules

##  The Schedule YANG Module

   This module defines a common schedule YANG module.  It is inspired
   from the "period of time" and "recurrence rule" format defined in
   {{?RFC5545}}.

   This module is defined as a standalone module rather than in the UCL
   module with the intention that the time/date definition can be
   reused.

###  Module Overview

   {{schedule-tree}} provides an overview of the tree structure of the "ietf-
   schedule" module.

~~~~
{::include ./yang/ietf-schedule-tree.txt}
~~~~
{: #schedule-tree title="UCL Tree Diagram" artwork-align="center"}

###  The YANG Module

   This module imports types defined in {{!I-D.ietf-netmod-rfc6991-bis}}.

~~~~
<CODE BEGINS>
file="ietf-schedule@2023-01-19.yang"
{::include ./yang/ietf-schedule.yang}
<CODE ENDS>
~~~~


### Examples

   Here are some examples to illustrate the use of the period and recurrence formats defined as
   YANG groupings. The data is provided in JSON.

#### Period of Time

   The period starting at 08:00:00 UTC, on January 1, 2023 and ending at 18:00:00 UTC
   on December 31, 2025:

~~~~
{
  "period-of-time": {
    "explicit-start": "2023-01-01T08:00:00Z",
    "explicit-end": "2025-12-01T18:00:00Z"
  }
}
~~~~

   The period starting at 08:00:00 UTC, on January 1, 2023 and lasting 15 days and
   5 hours and 20 minutes:

~~~~
{
  "period-of-time": {
    "start": "2023-01-01T08:00:00Z",
    "duration": "P15DT05:20:00"
  }
}
~~~~

   The period starting at 08:00:00 UTC, on January 1, 2023 and lasting 20 weeks:

~~~~
{
  "period-of-time": {
    "start": "2023-01-01T08:00:00Z",
    "duration": "P20W"
  }
}
~~~~

#### Recurrence Rule

  Every day in December

~~~
{
  "recurrence": {
    "freq": "daily",
    "bymonth": "12"
  }
}
~~~

   10 occurrences that occur every last Saturday of the month:

~~~~
{
  "recurrence": {
    "freq": "monthly",
    "count": "10",
    "byday": {
      "direction": "-1",
      "weekday": "saturday"
    }
  }
}
~~~~

   The last workday of the month, until December 25, 2023:

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
    "bysetpos": "-1"
  }
}
~~~~

   Every other week on Tuesday and Sunday, the week starts from Monday:

~~~
{
  "recurrence": {
    "freq": "weekly",
    "interval": "2",
    "byday": [
      { "weekday": "tuesday" },
      { "weekday": "sunday" }
    ],
    "wkst": "monday"
  }
}
~~~


##  The UCL Extension to the ACL Model {#sec-UCL}

###  Module Overview

   {{ucl-tree}} provides the tree strcuture of the "ietf-ucl-acl" module.

~~~~
{::include ./yang/ietf-ucl-acl-tree.txt}
~~~~
{: #ucl-tree title="UCL Extension" artwork-align="center"}

   This module specifies an extension to the IETF ACL model {{!RFC8519}}
   such that the UCL group index can be referenced by augmenting the
   "match" data node.

###  The YANG Module

   This module imports types defined in {{!RFC6991}}, {{!RFC8194}}, and
   {{!RFC8519}}.

~~~~
<CODE BEGINS>
file="ietf-ucl-acl@2023-01-19.yang"
{::include ./yang/ietf-ucl-acl.yang}
<CODE ENDS>
~~~~

# User Access Control Group ID RADIUS Attribute {#sec-radius}

   The User-Access-Group-ID RADIUS attribute and its embedded TLVs are
   defined with globally unique names.  The definition of the attribute
   follows the guidelines in Section 2.7.1 of {{!RFC6929}}.  This attribute
   is used to indicate the user-group ID to be used by the NAS.  When
   the User-Access-Group-ID RADIUS attribute is present in the RADIUS
   Access-Accept, the system applies the related access control to the
   users after it authenticates.

   The value fields of the Attribute are encoded in clear and not
   encrypted as, for example, Tunnel- Password Attribute {{?RFC2868}}.

   The User-Access-Group-ID Attribute is of type "string" as defined in
   Section 3.5 of {{!RFC8044}}.

   The User-Access-Group-ID Attribute MAY appear in a RADIUS Access-
   Accept packet.  It MAY also appear in a RADIUS Access-Request packet
   as a hint to the RADIUS server to indicate a preference.  However,
   the server is not required to honor such a preference.

   The User-Access-Group-ID Attribute MAY appear in a RADIUS CoA-Request
   packet.

   The User-Access-Group-ID Attribute MAY appear in a RADIUS Accounting-
   Request packet.

   The User-Access-Group-ID Attribute MUST NOT appear in any other
   RADIUS packet.

   The User-Access-Group-ID Attribute is structured as follows:

~~~~
   Type

      241

   Length

      This field indicates the total length, in octets, of all fields of
      this attribute, including the Type, Length, Extended-Type, and the
      "Value".

   Extended-Type

      TBA1

   Value

      This field contains the user group ID.
~~~~

   The User-Access-Group-ID Attribute is associated with the following
   identifier: 241.TBA1.

#  RADIUS Attributes

   The following table provides a guide as what type of RADIUS packets
   that may contain User-Access-Group-ID Attribute, and in what
   quantity.

~~~~
Access- Access- Access-  Challenge Acct.    #        Attribute
Request Accept  Reject             Request
 0+      0+      0        0         0+      241.TBA1 User-Access-Group-ID

CoA-Request CoA-ACK CoA-NACK #        Attribute
  0+          0       0      241.TBA2 User-Access-Group-ID

   The following table defines the meaning of the above table entries:

   0  This attribute MUST NOT be present in packet.
   0+ Zero or more instances of this attribute MAY be present in packet.
~~~~

# Security Considerations

##  YANG

   The YANG modules specified in this document defines schema for data
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

   There are a number of data nodes defined in the "ietf-ucl-acl" YANG module that are
   writable, creatable, and deletable (i.e., config true, which is the
   default).  These data nodes may be considered sensitive or vulnerable
   in some network environments.  Write operations to these data nodes
   could have a negative effect on network and security operations.

   * TBC
   * TBC

    Some of the readable data nodes in the "ietf-ucl-acl" YANG module may
    be considered sensitive or vulnerable in some network environments. It
    is thus important to control read access (e.g., via get, get-config,
    or notification) to these data nodes. These are the subtrees and data
    nodes and their sensitivity/vulnerability:

    *  <list subtrees and data nodes and state why they are sensitive>
    *  <list subtrees and data nodes and state why they are sensitive>

##  RADIUS

   RADIUS-related security considerations are discussed in {{!RFC2865}}.

   This document targets deployments where a trusted relationship is in
   place between the RADIUS client and server with communication
   optionally secured by IPsec or Transport Layer Security (TLS)
   {{?RFC6614}}.

#  IANA Considerations

##  YANG

   This document registers the following URIs in the "IETF XML Registry" {{!RFC3688}}.

~~~~
        URI: urn:ietf:params:xml:ns:yang:ietf-schedule
        Registrant Contact: The IESG.
        XML: N/A, the requested URI is an XML namespace.

        URI: urn:ietf:params:xml:ns:yang:ietf-ucl-acl
        Registrant Contact: The IESG.
        XML: N/A, the requested URI is an XML namespace.
~~~~

   This document registers the following YANG modules in the "YANG Module Names"
   registry {{!RFC6020}}.

~~~~
        name:               ietf-schedule
        namespace:          urn:ietf:params:xml:ns:yang:ietf-schedule
        prefix:             schedule
        maintained by IANA: N
        reference:          RFC XXXX

        name:               ietf-ucl-acl
        namespace:          urn:ietf:params:xml:ns:yang:ietf-ucl-acl
        prefix:             uacl
        maintained by IANA: N
        reference:          RFC XXXX
~~~~

##  RADIUS

   This document requests IANA to assign a new RADIUS attribute types from the IANA
   registry "Radius Attribute Types" {{RADIUS-Types}}:

| Value    | Description          | Data Type | Reference     |
| 241.TBA1 | User-Access-Group-ID | string    | This-Document |
{: #rad-reg title='RADIUS Attribute'}


--- back

# Example Usage

## Configuring the Controller Using Group based ACL {#controller-ucl}

   Let's consider an organization that would like to restrict the access of R&D
   employees that bring personally owned devices (BYOD) into the workplace.

   The access requirements are as follows:

   * Permit traffic from R&D BYOD of employees, destined to R&D employees'
     devices every work day from 8:00 to 18:00.

   * Deny traffic from R&D BYOD of employees, destined to finance servers
     located in the enterprise DC network starting at 8:30:00 of January 20,
     2023 with an offset of -08:00 from UTC (Pacific Standard Time) and ending
     at 18:00:00 in Pacific Standard Time on December 31, 2023.

   The following example illustrates the configuration of the SDN controller
   using the group-based ACL:

~~~~
{::include ./examples/controller-ucl.xml}
~~~~

## Configuring the PEP Using Group based ACL {#PEP-ucl}

   This section illustrates an example to configure a PEP  using
   the group-based ACL.

   The PEP which enforces group-based ACL may acquire group identities
   from the AAA server if working as a NAS authenticating both the
   source endpoint and the destination endpoint users. Another case for
   a PEP enforcing a group-based ACL is to obtain the group identity of
   the source endpoint directly from the packet field
   {{?I-D.smith-vxlan-group-policy}}. This does not intend to be exhaustive.

   Assume the mapping between device group ID and IP addresses is
   predefined or acquired via device authentication. The following example
   shows ACL configurations delivered from the controller to the PEP. This
   example is consistent with the example presented in {{controller-ucl}}.

~~~~
{::include ./examples/PEP-ucl.xml}
~~~~

## Configuring the PEP Using Address based ACL {#PEP-acl}

   The section illustrates an example of configuring a PEP using
   IP address based ACL. IP address based access control policies could
   be applied to the PEP that may not understand the group information,
   e.g., firewall.

   Assume an employee in the R&D department accesses the network
   wirelessly from a non-corporate laptop using IP address 192.168.1.10.
   The SDN controller associates the user group to which the employee
   belongs with the user address according to step 1 to 4 in {{overview}}.

   Assume the mapping between device group ID and IP addresses is
   predefined or acquired via device authentication. The following
   example shows IPv4 address based ACL configurations delivered from
   the controller to the PEP. This example is consistent with the example
   presented in {{controller-ucl}}.

~~~~
{::include ./examples/PEP-acl.xml}
~~~~

# Changes between Revisions

   v00 - v01

   *  Define a common schedule yang module and reuse in UCL yang module
      to support time/date-based activation condition.

   *  Either group-based or address-based ACL policies could be enforced
      at PEP, and allow group-based ACL policies maintained at the
      network controller.

   *  Optimize the process in section 4.1.

   *  Extend ACL module to support a generalized endpoint-group to cover
      both end users (e.g., enterprise employees) and enterprise hosts
      (e.g., IoT devices or servers);

   *  Simplify the definition of group in UCL model with only the most
      necessary group ID retained.

# Acknowledgments
{:numbered="false"}

   This work has benefited from the discussions of User-group-based
   Security Policy over the years.  In particular, {{?I-D.you-i2nsf-user-group-based-policy}}
   and {{?I-D.yizhou-anima-ip-to-access-control-groups}} provide mechanisms to
   establish the mapping between the IP address/prefix of user and access
   control group ID.

   Jianjie You, Myo Zarny, Christian Jacquenet, Mohamed Boucadair, and
   Yizhou Li contributed to an earlier version of {{?I-D.you-i2nsf-user-group-based-policy}}.
   We would like to thank the authors of that draft on modern network access
   control mechanisms for material that assisted in thinking about this document.

   The authors would like to thank Joe Clarke, Bill Fenner, Benoit
   Claise, Rob Wiltion, David Somers-Harris for their valuable comments
   and great input to this work.
