---
title: "A YANG Data Model and RADIUS Extension for Policy-based Network Access Control"
abbrev: "A Policy-based Network Access Control"
category: std

docname: draft-ietf-opsawg-ucl-acl-latest
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

  RADIUS-Types:
     title: RADIUS Types
     author:
       -
         organization: "IANA"
     target: http://www.iana.org/assignments/radius-types
     date: false

--- abstract

   This document defines a YANG data model for policy-based network access
   control, which provides consistent and efficient enforcement of
   network access control policies based on group identity.  Moreover, this document defines a mechanism to ease the maintenance
   of the mapping between a user group identifier and a set of IP/MAC addresses
   to enforce policy-based network access control.

   In addition, the document defines a RADIUS attribute that is used to
   communicate the user group identifier as part of identification and
   authorization information.

--- middle

# Introduction {#intro}

   With the increased adoption of remote access technologies (e.g.,
   Virtual Private Networks (VPNs)) and Bring Your Own Device (BYOD)
   policies, enterprises adopted more flexibility related to how, where,
   and when employees work and collaborate.  However, more flexibility
   comes with increased risks.  Enabling office flexibility (e.g.,
   mobility across many access locations) introduces a set of challenges
   for large-scale enterprises compared to conventional network access management approaches.
   Examples of such challenges are listed below:

   *  Endpoints do not have a stable IP address.  For example, Wireless
      LAN (WLAN) and VPN clients, as well as back-end Virtual Machine
      (VM)-based servers, can move; their IP addresses could change as a
      result.  This means that relying on IP/transport fields (e.g., the
      5-tuple) is inadequate to ensure consistent and efficient security
      policy enforcement.  IP address-based policies may not be flexible
      enough to accommodate endpoints with volatile IP addresses.

   *  With the massive adoption of teleworking, there is a need to
      apply different security policies to the same set of users under
      different circumstances (e.g., prevent relaying attacks against a
      local attachment point to the enterprise network).  For example,
      network access might be granted based upon criteria such as users'
      access location, source network reputation, users' role, time-of-
      day, type of network device used (e.g., corporate issued device
      versus personal device), device's security posture, etc.  This
      means that the network needs to recognize the users' identity and their
      current context, and map the users to their correct access
      entitlement to the network.

   This document defines a YANG data model ({{sec-UCL}}) for policy-based network access control,
   which extends the IETF Access Control Lists (ACLs) module defined in {{!RFC8519}}.
   This module can be used to ensure consistent enforcement of ACL policies
   based on the group identity.

   The ACL concept has been generalized to be device-nonspecific, and can be
   defined at network/administrative domain level {{?I-D.ietf-netmod-acl-extensions}}. To
   allow for all applications of ACLs, the YANG module for policy-based network
   ACL defined in {{sec-UCL}} does not limit how it can be used.

   This document also defines a mechanism to establish a mapping between (1) the
   user group identifier (ID) and (2) common IP packet header fields and other
   encapsulating packet data (e.g., MAC address) to execute the policy-based access control.

   Additionally, the document defines a Remote Authentication Dial-in
   User Service (RADIUS) {{!RFC2865}} attribute that is used to
   communicate the user group identifier as part of identification and
   authorization information ({{sec-radius}}).

   Although the document cites MAC addresses as an example in some sections, the
   document does not make assumptions about which identifiers are used to trigger ACLs.
   These examples should not be considered as recommendations. Readers should be
   aware that MAC-based ACLs can be bypassed by flushing out the MAC address.
   Other implications related to the change of MAC addresses are discussed in
   {{?I-D.ietf-madinas-use-cases}}.

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
   * 2023-01-19 --> the actual date of the publication of this document

# Conventions and Definitions

{::boilerplate bcp14-tagged}

   The meanings of the symbols in tree diagrams are defined in
   {{?RFC8340}}.

   The document uses the following definition in {{?RFC3198}}:

   * policy

   The document uses the following definitions and acronyms defined in {{!RFC8519}}:

   * Access Control Entry (ACE)

   * Access Control List (ACL)

   The following definitions and acronyms are used throughout this document:

   * User group based Control List (UCL) model:
   : A YANG data model for policy-based network access
     control that specifies an extension to the "ietf-access-control-list" model {{!RFC8519}}.
     It allows policy enforcement based on the group identity, which can be used
     both at the network device level and at the network/administrative domain level.

   * Endpoint:
   : refers to an end-user, host device, or application that actually connects to a network.
     An end-user is defined as a person. A host device provides compute, memory,
     storage and networking capabilities and connects to the network without any user intervention. Host devices refer to servers, IoTs and other devices owned
     by the enterprise. An application is a software program used for a specific service.

#  Sample Usage

   Access to some networks (e.g., enterprise networks) requires to
   recognize the users’ identities no matter how, where, and when they
   connect to the network resources.  Then, the network maps the
   (connecting) users to their access authorization rights.  Such rights
   are defined following local policies.  As discussed in {{intro}},
   because (1) there is a large number of users and (2) a user may have different
   source IP addresses in different network segments,
   deploying a network access control policy for each IP address or
   network segment is a heavy workload.  An alternate approach is to
   configure endpoint groups to classify users and enterprise devices
   and associate ACLs with endpoint groups so that endpoints in each
   group can share a group of ACL rules.  This approach greatly reduces
   the workload of the administrators and optimizes the ACL resources.

   The network ACLs can be provisioned on devices using specific
   mechanisms, such as {{!RFC8519}} or {{?I-D.ietf-netmod-acl-extensions}}.

   Different policies may need to be applied in different contextual situations.
   For example, companies may restrict (or grant) employees access to specific
   internal or external resources during work hours,
   while another policy is adopted during off-hours and weekends.  A
   network administrator may also require to enforce traffic shaping
   ({{Section 2.3.3.3 of ?RFC2475}}) and policing (
   {{Section 2.3.3.4 of ?RFC2475}}) during peak hours in order not to affect other data
   services.

#  Policy-based Network Access Control

##  Overview {#overview}

   The architecture of a system that provides real-time and consistent
   enforcement of access control policies is shown in {{arch}}. This architecture
   includes the following functional entities and interfaces:

   *  A service orchestrator which coordinates the overall service,
      including security policies.  The service may be connectivity or
      any other access to resources that can be hosted and offered by a network.

   *  A software-defined networking (SDN) {{?RFC7149}} {{?RFC7426}} controller which is
      responsible for maintaining endpoint-group based ACLs and mapping the
      endpoint-group to the associated attributes information (e.g., IP/MAC address).
      An SDN controller also behaves as a Policy Decision Point (PDP) {{?RFC3198}}
      and pushes the required access control policies to relevant Policy
      Enforcement Points (PEPs) {{?RFC3198}}.  A PDP is also known as
      "policy server" {{?RFC2753}}.

      An SDN controller may interact with an Authentication,
      Authorization and Accounting (AAA) {{?RFC3539}} server or a Network Access Server (NAS) {{?RFC7542}}.

   *  A Network Access Server (NAS) entity which handles authentication
      requests.  The NAS interacts with an AAA server to complete user
      authentication using protocols like RADIUS {{!RFC2865}}. When access is granted, the AAA
      server provides the group identifier (group ID) to which the user
      belongs when the user first logs onto the network. A new RADIUS attribute
      is defined in {{sec-radius}} for this purpose.

   *  The AAA server provides a collection of authentication, authorization,
      and accounting functions. The AAA server is responsible for centralized user
      information management. The AAA server is preconfigured with user
      credentials (e.g., user name and password), possible group identities
      and related user attributes (users may be divided into different
      groups based on different user attributes).

   *  The Policy Enforcement Point (PEP) is the central entity
      which is responsible for enforcing appropriate access control
      policies.  In some cases, a PEP may map incoming packets to their
      associated source or destination endpoint-group IDs, and acts on
      the endpoint-group ID based ACL policies, e.g., a NAS as the PEP
      or a group identifier could be carried in packet header (see
      {{Section 6.2.3 of ?I-D.ietf-nvo3-encap}}).  While in other cases,
      the SDN controller maps the group ID to the related common packet
      header and delivers IP/MAC address based ACL policies to the
      required PEPs.

      Multiple PEPs may be involved in a network.

      A PEP exposes a NETCONF interface {{!RFC6241}} to an SDN controller.

   {{arch}} provides the overall architecture and procedure for policy-
   based access control management.

~~~~
                                         .------------.
                                         |Orchestrator|
                                         '------+-----'
       Service                                  | (Step 1)
      --------------------------------------------------------
                                                |
       Network                                  |
                                 (Step 4)       |
       .-------.        .--------.     .--------+-----------.
       |User #1+--+     | AAA    |     |    SDN Controller  |
       '-------'  |     | Server +-----+        (PDP)       |
                  |     '----+---'     '--------+-----------'
                  |          |                  |
                  |          |           +------+--------+(Step 5)
        (Step 2)  |          |(Step 3)   |               |
                  |          |           |               |
                  |        .-+-----------+---------------+------------.
                  |        | .----------------------. .--------------.|
       .-------.  +--------+ | Network Access Server| |firewall, etc.||
       |User #2+-----------+ |       (NAS)          | '--------------'|
       '-------'           | '----------------------'                 |
                           |                     (PEP)                |
                           '------------------------------------------'
~~~~
{: #arch title="An Architecture for Group-based Policy Management" artwork-align="center"}

   In reference to {{arch}}, the following typical flow is experienced:

   Step 1:
   :  Administrators (or a service orchestrator) configure an SDN
      controller with network-level ACLs using the YANG module defined
      in {{sec-UCL}}. An example is provided in {{controller-ucl}}.

   Step 2:
   :  When a user first logs onto the network, he/she is
      required to be authenticated (e.g., using user name and password)
      at the NAS.

   Step 3:
   :  The authentication request is then relayed to the AAA server
      using a protocol such as RADIUS {{!RFC2865}}. It is assumed that the
      AAA server has been appropriately configured to store user credentials,
      e.g., user name, password, group information, and other user attributes.
      This document does not restrict what authentication method is used. Administrators
      may refer to, e.g., {{Section 7.3 of ?I-D.dekok-radext-deprecating-radius}}
      for authentication method recommendations.
      If the authentication request succeeds, the user is placed in a
      user group the identity of which is returned to the network access server
      as the authentication result (see {{sec-radius}}).
      If the authentication fails, the user is not assigned any user
      group, which also means that the user has no access; or the user
      is assigned a special group with very limited access permissions
      for the network (as a function of the local policy). ACLs are
      enforced so that flows from that IP address are discarded
      (or rate-limited) by the network.
      In some implementations, AAA server can be integrated with an SDN controller.

   Step 4:
   :  Either the AAA server or the NAS notifies an SDN controller
      of the mapping between the user group ID and related common packet
      header attributes (e.g., IP/MAC address).

   Step 5:
   :  Either group or IP/MAC address based access control policies
      are maintained on relevant PEPs under the SDN controller's management.
      Whether the PEP enforces the group or IP/MAC address based ACL is
      implementation specific. Both types of ACL policy may exist on
      the PEP. {{PEP-ucl}} and {{PEP-acl}} elaborate on each case.

##  Endpoint Group

###  User Group

   The user group is determined by a set of predefined policy criteria
   (e.g., source IP address, geolocation data, time of day, or device certificate).
   It uses an identifier (user group ID) to represent the collective identity of
   a group of users. Users may be moved to different user-groups if their
   composite attributes, environment, and/or local enterprise policy change.


   A user is authenticated, and classified at the AAA server, and
   assigned to a user group.  A user's group membership may change as
   aspects of the user change.  For example, if the user group
   membership is determined solely by the source IP address, then a
   given user's group ID will change when the user moves to a new
   IP address that falls outside of the range of addresses of the
   previous user group.

   This document does not make any assumption about how user groups are
   defined.  Such considerations are deployment specific and are out of
   scope.  However, and for illustration purposes, {{ug-example}} shows
   an example of how user group definitions may be characterized. User
   groups may share several common criteria.  That is, user group
   criteria are not mutually exclusive.  For example, the policy
   criteria of user groups R&D Regular and R&D BYOD may share the same
   set of users that belong to the R&D organization, and differ only in
   the type of clients (firm-issued clients vs. users' personal
   clients).  Likewise, the same user may be assigned to different user
   groups depending on the time of day or the type of day (e.g.,
   weekdays versus weekends), etc.

| Group Name | Group ID | Group Description |
| R&D        |   foo-10 |  R&D employees                 |
| R&D BYOD   |   foo-11 |  Personal devices of R&D employees |
| Sales      |   foo-20 |  Sales employees               |
| VIP        |   foo-30 |  VIP employees                 |
{: #ug-example title='User Group Example'}

###  Device Group

   The device-group ID is an identifier that represents the collective
   identity of a group of enterprise end devices.  An enterprise device
   could be a server that hosts applications or software that deliver
   services to enterprise users.  It could also be an enterprise IoT
   device that serve a limited purpose, e.g., a printer that allows
   users to scan, print and send emails. {{dg-example}} shows an example
   of how device-group definitions may be characterized.

   | Group Name | Group ID | Group Description |
   | Workflow   |   bar-40     |  Workflow  resource servers   |
   | R&D Resource |   bar-50     | R&D resource servers |
   |Printer Resource|   bar-60     | Printer resources |
   {: #dg-example title='Device-Group Example'}

   Users accessing an enterprise device should be strictly controlled.
   Matching abstract device group ID instead of specified addresses in
   ACL polices helps shield the consequences of address change (e.g.,
   back-end VM-based server migration).

### Application Group

   An application group is a collection of applications that share a common access control policies.
   A device may run multiple applications, and different policies might need to be
   applied to the applications and device. A single application may need to run on
   multiple devices/VMs/containers, the abstraction of application group eases the
   process of application migration. For example, the policy does not depend on the transport coordinates (i.e., 5-tuple).
   {{ag-example}} shows an example of how application-group definitions may be characterized.

   | Group Name | Group ID | Group Description |
   | Audio/Video Streaming  |   baz-70   |  Audio/Video conferecing application |
   | Instant messaging |   baz-80   | Messaging application |
   | document collaboration |  baz-90  | Real-time document editing application |
   {: #ag-example title='Application-Group Example'}

#  Modules Overview

##  The UCL Extension to the ACL Model

   This module specifies an extension to the "ietf-access-control-list" model {{!RFC8519}}. This extension adds
   endpoint groups so that an endpoint group identifier can be matched upon, and also
   enable access control policy activation based on date and time conditions.

   {{ucl-tree}} provides the tree structure of the "ietf-ucl-acl" module.

~~~~
{::include ./yang/ietf-ucl-acl-tree.txt}
~~~~
{: #ucl-tree title="UCL Extension" artwork-align="center"}

   The first part of the data model augments the "acl" list in the
   "ietf-access-control-list" model {{!RFC8519}} with a "endpoint-groups" container
   having a list of "endpoint group" inside, each entry has a "group-id" that uniquely
   identifies the endpoint group and a "group-type" parameter to specify the endpoint group type.

> "group-id" is defined as a string rather than uint to accommodate deployments which require some identification hierarchy within a domain. Such a hierarchy is meant to ease coordination within an administrative domain. There might be cases where a domain needs to tag packets with the group they belong to. The tagging does not need to mirror exactly the "group id" used to populate the policy. How the "group-id" string is mapped to the tagging or field in the packet header in encapsulation scenario is outside the scope of this document. Augmentation may be considered in the future to cover encapsulation considerations.

   The second part of the data model augments the "matches" container in the IETF
   ACL model {{!RFC8519}} so that a source and/or destination endpoint group index
   can be referenced as the match criteria.

   The third part of the data model augments the "ace" list in the "ietf-access-control-list"
   model {{!RFC8519}} with date and time specific parameters to allow ACE to be
   activated based on a date/time condition. Two types of time range are defined,
   which reuse "recurrence" and "period" groupings defined in the "ietf-schedule"
   YANG module in {{!I-D.ma-opsawg-schedule-yang}}, respectively. Note that the data model augments the definition of
   "recurrence" grouping with a "duration" data node to specify the duration of
   time for each occurrence the policy activation is triggered.


#  YANG Modules

##  The "ietf-ucl-acl" YANG Module {#sec-UCL}

   This module imports types and groupings defined in the "ietf-schedule" YANG
   module in {{!I-D.ma-opsawg-schedule-yang}}. It also augments the "ietf-access-control-list" module defined in {{!RFC8519}}.

~~~~
<CODE BEGINS> file "ietf-ucl-acl@2023-01-19.yang"
{::include ./yang/ietf-ucl-acl.yang}
<CODE ENDS>
~~~~

# User Access Control Group ID RADIUS Attribute {#sec-radius}

   The User-Access-Group-ID RADIUS attribute is
   defined with a globally unique name.  The definition of the attribute
   follows the guidelines in {{Section 2.7.1 of !RFC6929}}.  This attribute
   is used to indicate the user group ID to be used by the NAS.  When
   the User-Access-Group-ID RADIUS attribute is present in the RADIUS
   Access-Accept, the system applies the related access control to the
   users after it authenticates.

   The User-Access-Group-ID Attribute is of type "string" as defined in
   {{Section 3.5 of !RFC8044}}.

   The User-Access-Group-ID Attribute MAY appear in a RADIUS Access-
   Accept packet.  It MAY also appear in a RADIUS Access-Request packet
   as a hint to the RADIUS server to indicate a preference.  However,
   the server is not required to honor such a preference. If more than
   one instance of the User-Access-Group-ID Attribute appears in a RADIUS
   Access-Accept packet, this means that the user is a member of many groups.

   The User-Access-Group-ID Attribute MAY appear in a RADIUS CoA-Request
   packet.

   The User-Access-Group-ID Attribute MAY appear in a RADIUS Accounting-
   Request packet. Specifically, this may be used by a NAS to acknowledge that the attribute
   was received in the RADIUS Access-Request and the NAS is enforcing that policy.

   The User-Access-Group-ID Attribute MUST NOT appear in any other
   RADIUS packet.

   The User-Access-Group-ID Attribute is structured as follows:

~~~~
   Type
     TBA1

   Length
     This field indicates the total length, in octets, of all fields of
     this attribute, including the Type, Length, Extended-Type, and the
     "Value". The Length MUST be at most 67 octets.

   Data Type
     string ({{Section 3.5 of !RFC8044}})

   Value
      This field contains the user group ID.
~~~~


#  RADIUS Attributes

   {{rad-att}} provides a guide as what type of RADIUS packets
   that may contain User-Access-Group-ID Attribute, and in what
   quantity.

|Access-Request	|Access-Accept	|Access-Reject	|Challenge	| Attribute     |
| 0+            |  0+          | 0            |    0     | User-Access-Group-ID     |
|Accounting-Request|	CoA-Request|	CoA-ACK	|CoA-NACK		| Attribute     |
|    0+            | 0+         | 0       | 0        | User-Access-Group-ID     |
{: #rad-att title='Table of Attributes'}

Notation for {{rad-att}}:

   0
   :  This attribute MUST NOT be present in packet.

   0+
   : Zero or more instances of this attribute MAY be present in packet.

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

   There are a number of data nodes defined in the "ietf-ucl-acl" YANG module that are
   writable, creatable, and deletable (i.e., config true, which is the
   default).  These data nodes may be considered sensitive or vulnerable
   in some network environments.  Write operations to these data nodes
   could have a negative effect on network and security operations. These are the
   subtrees and data nodes and their sensitivity/vulnerability:

   * /acl:acls/acl:acl/uacl:endpoint-groups/uacl:endpoint-group:
   : This list specifies all the endpoint group entries. Unauthorized write access to this
     list can allow intruders to modify the entries so as to forge an endpoint
     group that does not exist or maliciously delete an existing endpoint group,
     which could be used to craft an attack.

   * /acl:acls/acl:acl/acl:aces/acl:ace/acl:matches/uacl:endpoint-group:
   : This subtree specifies a source and/or endpoint group index as match criteria in the
     ACEs. Unauthorized write access to this data node may allow intruders to
     modify the group identity so as to permit access that should not be
     permitted, or deny access that should be permitted.

  Some of the readable data nodes in the "ietf-ucl-acl" YANG module may
  be considered sensitive or vulnerable in some network environments. It
  is thus important to control read access (e.g., via get, get-config,
  or notification) to these data nodes. These are the subtrees and data
  nodes and their sensitivity/vulnerability:

   * /acl:acls/acl:acl/acl:aces/acl:ace/uacl:time-range:
  : This subtree specifies when the access control entry rules are in effect. An
    unauthorized read access of the list will allow the attacker to determine
    which rules are in effect, to better craft an attack.


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
        URI: urn:ietf:params:xml:ns:yang:ietf-ucl-acl
        Registrant Contact: The IESG.
        XML: N/A, the requested URI is an XML namespace.
~~~~

   This document registers the following YANG modules in the "YANG Module Names"
   registry {{!RFC6020}}.

~~~~
        name:               ietf-ucl-acl
        namespace:          urn:ietf:params:xml:ns:yang:ietf-ucl-acl
        prefix:             uacl
        maintained by IANA: N
        reference:          RFC XXXX
~~~~

##  RADIUS

   This document requests IANA to assign a new RADIUS attribute type from the IANA
   registry "Radius Attribute Types" {{RADIUS-Types}}:

| Value    | Description          | Data Type | Reference     |
| TBA1 | User-Access-Group-ID | string    | This-Document |
{: #rad-reg title='RADIUS Attribute'}


--- back

# Examples Usage

## Configuring the Controller Using Group based ACL {#controller-ucl}

   Let's consider an organization that would like to restrict the access of R&D
   employees that bring personally owned devices (BYOD) into the workplace.

   The access requirements are as follows:

   * Permit traffic from R&D BYOD of employees, destined to R&D employees'
     devices every work day from 8:00:00 to 18:00:00 UTC, starting in January 1st, 2025.

   * Deny traffic from R&D BYOD of employees, destined to finance servers
     located in the enterprise DC network starting at 8:30:00 of January 20,
     2025 with an offset of -08:00 from UTC (Pacific Standard Time) and ending
     at 18:00:00 in Pacific Standard Time on December 31, 2025.

   The following example illustrates the configuration of an SDN controller
   using the group-based ACL:

~~~~
{::include-fold ./examples/controller-ucl.xml}
~~~~

## Configuring a PEP Using Group-based ACL {#PEP-ucl}

   This section illustrates an example to configure a PEP  using
   the group-based ACL.

   The PEP which enforces group-based ACL may acquire group identities
   from the AAA server if working as a NAS authenticating both the
   source endpoint and the destination endpoint users. Another case for
   a PEP enforcing a group-based ACL is to obtain the group identity of
   the source endpoint directly from the packet field
   {{?I-D.smith-vxlan-group-policy}}. This example does not intend to be exhaustive.

   Assume the mapping between device group ID and IP addresses is
   predefined or acquired via device authentication. The following example
   shows ACL configurations delivered from the controller to the PEP. This
   example is consistent with the example presented in {{controller-ucl}}.

~~~~
{::include-fold ./examples/PEP-ucl.xml}
~~~~

## Configuring the PEP Using Address-based ACL {#PEP-acl}

   The section illustrates an example of configuring a PEP using
   IP address based ACL. IP address based access control policies could
   be applied to the PEP that may not understand the group information,
   e.g., firewall.

   Assume an employee in the R&D department accesses the network
   wirelessly from a non-corporate laptop using IP address 192.0.2.10.
   The SDN controller associates the user group to which the employee
   belongs with the user address according to step 1 to 4 in {{overview}}.

   Assume the mapping between device group ID and IP addresses is
   predefined or acquired via device authentication. The following
   example shows IPv4 address based ACL configurations delivered from
   the controller to the PEP. This example is consistent with the example
   presented in {{controller-ucl}}.

~~~~
{::include-fold ./examples/PEP-acl.xml}
~~~~

The following shows an example of the same policy but with a destination IPv6 prefix.

~~~~
{::include-fold ./examples/PEP-acl-ipv6.xml}
~~~~


# Changes between Revisions

  v00 - v01

  *  Change the document title and add a reference to "policy"

  *  Split the definition of schedule YANG module into a seperate document

  *  Add reference to draft-dekok-radext-deprecating-radius for authentication method recommendations

  *  Change endpoint group-id as a string, and fix related examples accordingly

  *  Use typedef to ease leafref of the node

  *  Tweaks to the RADIUS section and add a restriction to the length based on comments from RADEXT

  *  Add IPv6 examples

  *  Editorial changes

# Acknowledgments
{:numbered="false"}

   This work has benefited from the discussions of User-group-based
   Security Policy over the years.  In particular, {{?I-D.you-i2nsf-user-group-based-policy}}
   and {{?I-D.yizhou-anima-ip-to-access-control-groups}} provide mechanisms to
   establish a mapping between the IP address/prefix of users and access
   control group IDs.

   Jianjie You, Myo Zarny, Christian Jacquenet, Mohamed Boucadair, and
   Yizhou Li contributed to an earlier version of {{?I-D.you-i2nsf-user-group-based-policy}}.
   We would like to thank the authors of that draft on modern network access
   control mechanisms for material that assisted in thinking about this document.

   The authors would like to thank Joe Clarke, Bill Fenner, Benoit
   Claise, Rob Wilton, David Somers-Harris, Alan Dekok, and Heikki Vatiainen for their valuable comments
   and great input to this work.
