---
title: SCHC Minimal Architecture
# abbrev: SCHC-Min-Arch
docname: draft-lampin-schc-minimal-architecture-00
date: 2025-07-16


# stand_alone: true

ipr: trust200902
area: Internet
wg: SCHC Working Group
kw: Internet-Draft
cat: std
submissionType: IETF

coding: us-ascii
pi:    # can use array (if all yes) or hash here
  toc: yes
  sortrefs: yes   # defaults to yes
  symrefs: yes

author:
      -
        ins: Q. Lampin
        name: Quentin Lampin
        org: Orange

        street: Orange 3 Massifs - 22 Chemin du Vieux Chene
        city: Meylan
        code: 38240
        country: France
        email: quentin.lampin@orange.com
      -
        ins: M. Dumay
        name: Marion Dumay
        org: Orange
        street: Orange 3 Massifs - 22 Chemin du Vieux Chene
        city: Meylan
        code: 38240
        country: France
        email: marion.dumay@orange.com
      -
        ins: P. Surbayrole
        name: Philippe Surbayrole
        org: Orange
        street: Orange 3 Massifs - 22 Chemin du Vieux Chene
        city: Meylan
        code: 38240
        country: France
        email: philippe.surbayrole@orange.com
      -
        ins: A. Pelov
        name: Alexander Pelov
        org: IMT Atlantique
        street: 2 Rue de la Chataigneraie
        city: Cesson-Sévigné
        code: 35576
        country: France
        email: alexander.pelov@imt-atlantique.fr
      -
        ins: L. Toutain
        name: Laurent Toutain
        org: IMT Atlantique
        street: 2 Rue de la Chataigneraie
        city: Cesson-Sévigné
        code: 35576
        country: France
        email: laurent.toutain@imt-atlantique.fr

normative:
  RFC8724:
  RFC8824:
  
  DRAFT-ARCH:
    title: "SCHC Architecture"
    author:
      -
        name: Alexander Pelov
      -
        name: Pascal Thubert
      -
        name: Ana Minaburo
    date: 2025-02
    seriesinfo:
      Internet-Draft: draft-ietf-schc-architecture-04
  RFC2119:
  RFC8174:

informative:
  RFC9363:

entity:
        SELF: "[RFCXXXX]"

# --- note_IESG_Note
#
# bla bla bla

--- abstract

Static Context Header Compression and fragmentation (SCHC) framework provides 
both a header compression mechanism and an optional fragmentation mechanism. 
SCHC operation is based on a common static Context stored at the two (or more) 
ends of a communication.

This document defines a minimal architecture for SCHC deployments, providing 
guidance for implementers and operators on the essential components and their 
interactions required for effective SCHC operation. The minimal architecture
addresses the fundamental requirements for SCHC deployment while maintaining 
flexibility for various use cases and environments.

--- middle

# Introduction        

The SCHC Working Group has developed the {{RFC8724}} SCHC technology for 
 Low-Power Wide-Area (LPWA) networks, providing efficient header compression
 and fragmentation mechanisms. As SCHC adoption expands beyond its original 
 scope, there is a need to define a minimal architecture that identifies only 
 the essential elements required for proper SCHC operation.

A minimal architecture for SCHC defines the essential components, their 
 relationships, and the minimum requirements for a functional SCHC deployment. 
 This architecture
 serves as a foundation that can be extended based on specific deployment
 requirements and use cases.

This document provides:

* A definition of the minimal components required for SCHC operation
* The essential interactions between these components
* Problems and challenges addressed by each component

# Requirements Notation

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
 "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this
 document are to be interpreted as described in BCP 14 {{RFC2119}} {{RFC8174}} 
 when, and only when, they appear in all capitals, as shown here.

# Terminology

This section defines terminology and abbreviations used in this
 document. It borrows from the terminology of {{RFC8724}} and {{DRAFT-ARCH}}.
 
In the following, terms used in the terminology are assumed to be defined in the
context of the SCHC protocol unless specified otherwise, *.e.g* Endpoint refers
to a SCHC Endpoint, Instance refers to a SCHC Instance, and so on.


**Endpoint**: A network host capable of compressing and decompressing headers 
  and optionally fragmenting and reassembling packets. 

**Instance**: A logical component of an Endpoint that implements the SCHC 
  protocol, including header compression, fragmentation, and context management. 

**Profile**: A set of configurations that define how SCHC operations are 
  performed within a specific Instance. It includes parameters for the different
  SCHC components
**Domain**: A network domain where SCHC instances share a common context 
  for compression and fragmentation operations.

**Domain Manager**: A logical component that manages the SCHC domain,
  including context synchronization and profile distribution.

**Context Repository**: A logical component that stores and manages SCHC
  contexts and rules used by SCHC domains.





# Minimal Architecture Components

## Core Components

### SCHC Instance

A SCHC Instance is the fundamental component that implements the SCHC protocol
  as defined in {{RFC8724}}. A Network host MAY executes several SCHC Instances
  in its protocol stack.

The functional architecture of a SCHC Instance is depicted in the following 
figure:

~~~~~~~~
+-------------------------------------------------------------+
|                    SCHC Instance                            |
+-------------------------------------------------------------+
|                                                             |
|  +----------------------+    +-------------------------+    |
|  |      C/D Engine      |    |      F/R Engine         |    |
|  |                      |    |                         |    |
|  | - compress(buffer)   |    | - fragment(datagram)    |    |
|  | - decompress(buffer) |    | - reassemble(fragments) |    |
|  +----------------------+    +-------------------------+    |
|                                                             |
|  +-----------------------------------------------------+    |
|  |                   Configuration                     |    |
|  +-----------------------------------------------------+    |
|  |                                                     |    |
|  |   +-------------------+    +-------------------+    |    |
|  |   |      Profile      |    |     Context       |    |    |
|  |   |                   |    |                   |    |    |
|  |   | - Dispatch Engine |    | - Set of Rules    |    |    |
|  |   |   configuration   |    |   (SoR)           |    |    |
|  |   | - matching        |    | - parser          |    |    |
|  |   |   policy          |    |   identification  |    |    |
|  |   | - device-specific |    |                   |    |    |
|  |   |   configuration   |    |                   |    |    |
|  |   +-------------------+    +-------------------+    |    |
|  +-----------------------------------------------------+    |
+-------------------------------------------------------------+
~~~~~~~~

A SCHC Instance MUST implement the following components:

* Header Compression and Decompression (C/D) engine
* Context Manager
* Profile Manager

Its configuration MUST include:

* a SCHC Context, which defines the set of C/D and F/R rules - or Set of Rules -
  and the parser to be used to delineate the header field.
* a SCHC Profile, which defines the configuration of the Dispatch Engine, the 
  rule matching policy, and the device-specific configuration.


A SCHC Instance MAY implement:

* Fragmentation and Reassembly (F/R) functionality
* Dynamic context update mechanisms
* Performance monitoring and reporting

#### Header Compression and Decompression (C/D) engine

This component is responsible for compressing and decompressing headers
  using the SCHC protocol, as described in {{RFC8724}}. It applies the rules 
  defined in the SCHC Context.
  
The C/D engine MUST expose the following interface:

- `compress(buffer, context, profile)`: Compresses the provided buffer using the
   SCHC Context and the profile.
- `decompress(buffer, context, profile)`: Decompresses the provided buffer using
   the SCHC Context and the profile.

Internally, on compression, the C/D engine:

- delineates the fields using the parser identified in the SCHC Context.
- elects the appropriate compression rules based on the SCHC Context and the 
  matching policy defined in the profile.
- applies the compression rules to the fields of the header.
- generates the compressed SCHC packet.
  
On decompression, the C/D engine:

- identifies the C/D rule based on the SCHC compressed packet.
- applies the decompression rules to reconstruct the original header.
- reconstructs the original packet from the decompressed header and payload.

#### Fragmentation and Reassembly (F/R)

This component is responsible for fragmenting larger packets into smaller
  fragments and reassembling them at the receiving end. It is optional in
  the minimal architecture but recommended for scenarios where packet sizes
  exceed the maximum transmission unit (MTU) of the underlying network.

### Dispatch Engine

The Dispatch Engine is responsible for delivering compressed packets to the
  correct SCHC Instance. It ensures that the compressed packets are sent to
  the appropriate destination and that the decompressed packets are delivered
  to the correct application or protocol routine.

The Dispatch Engine MUST provide the following functionality:

- `dispatch_compress(buffer, admission_rules)`: dispatch a packet to the 
    appropriate SCHC Instance based on packet admission rules.
- `dispatch_decompress(buffer, context, profile)`: dispatch the compressed 
    packet to the correct recipient, .e.g. application or protocol routine.

The rationale for the Dispatch Engine is to allow multiple SCHC Instances to 
 coexist on the same network host, each handling different protocols or 
 applications. Additionally, it also allows the coexistence of regular traffic 
 to coexist with traffic compressed by SCHC.
  
  To do so, The Dispatch Engine provides a mechanism to route packets to the 
  appropriate SCHC Instance based on supplied admission rules.

**Dispatch scenarios**:

#### Case 1: The Dispatch Engine is integrated into the network stack and a single SCHC Instance is used.
  
~~~~~~
          Endpoint 1                          Endpoint 2    
+-------------+-------------+       +-------------+-------------+
|   App. A    |    App. B   |       |   App. A    |    App. B   |   
+-------------+-------------+       +-------------+-------------+   
|    HTTP     |     CoAP    |       |    HTTP     |     CoAP    |   
+-------------+-------------+       +-------------+-------------+   
|  QUIC/UDP   |     UDP     |       |  QUIC/UDP   |     UDP     |      
+-------------+-------------+       +-------------+-------------+
|            IPv6           |       |            IPv6           |   
+-----+-------+-------------+       +-------------+-------------+
|    ^           ^      |   |       |    ^           ^      |   |
|    |  Dispatch |  UDP Dest|       |    |  Dispatch |  UDP Src |
|    |   Engine  | Port 5678|       |    |   Engine  | Port 5678|
|    |           |      |   |       |    |           |      |   |   
+----+-----------+------+---+       +----+-----------+----------+
    IPv6         |     IPv6             IPv6         |     IPv6    
  datagram       |   datagram         datagram       |   datagram
     | +---------+------+-----+          | +---------+------+-----+
     | |     SCHC Instance    |          | |     SCHC Instance    |
     | +---------+------+-----+          | +---------+------+-----+
     | |         |      |     |          | |         |      |     |
     | |+------------+  |     |          | |+------------+  |     |
     | || SCHC Inst. |  |     |          | || SCHC Inst. |  |     |
     | || Decompress.|  |     |          | || Decompress.|  |     |
     | |+------------+  V     |          | |+------------+  V     |
     | |    ^  +------------+ |          | |    ^  +------------+ |
     | |    |  | SCHC Inst. | |          | |    |  | SCHC Inst. | |
     | |    |  | Compress.  | |          | |    |  | Compress.  | |
     | |    |  +------------+ |          | |    |  +------------+ |
     | |    |        |        |          | |    |        |        |
     | +----+--------+--------+          | +----+--------+--------+
     |     SCHC     SCHC                 |     SCHC     SCHC 
     |    Packet   packet                |    Packet   packet
     |      |        |                   |      |        |
     v      |        V                   V      |        V    
+---------------------------+       +---------------------------+   
|       Ethertype Ethertype |       |       Ethertype Ethertype |
|         == SCHC  := SCHC  |       |         == SCHC  := SCHC  |
|                           |       |                           |
| Link layer, e.g. Ethernet |       | Link layer, e.g. Ethernet |   
+---------------------------+       +---------------------------+   
|  Network Interface Card   |       |  Network Interface Card   |    
+---------------------------+       +---------------------------+   
|       Physical Layer      |       |       Physical Layer      |   
+---------------------------+       +---------------------------+   
            | |                                  | |
            | |                                  | |
            | +----------------------------------+ |
            +--------------------------------------+
              
~~~~~~

In this simple scenario, the Dispatch Engine is integrated into
 the network stack and there is a unique predefined SCHC Instance for a specific 
 protocol stack, such as CoAP over UDP over IPv6. This is the classic case for 
 SCHC over LPWAN networks, as described in {{RFC8724}}, {{RFC8824}}, 
 {{RFC9363}}.

The dispatching is done based on a identified header field, such as the an 
 ethertype, the IPv6 Next Header field, a specific UDP port, etc.
 
This implementation scenario therefore assumes that the endpoint Operating 
 System (OS) implements the SCHC protocol as part of its network stack and that 
 SCHC is allocated the appropriate ethertype, IPv6 Next Header value or UDP 
 port from IANA.

In the example above, 

* On Endpoint 1, the Dispatch "intercepts" outbound packets whose UDP 
  destination port is 5678, which is used by CoAP. It then routes these packets 
  to the SCHC Instance for CoAP over UDP over IPv6. The SCHC instance 
  then compresses the CoAP, UDP and IPv6 headers, and calls the Link Layer
  interface to send the compressed packet over the network, setting the
  appropriate SCHC ethertype in the link layer header.

* On Endpoint 2, incoming packets whose SCHC ethertype is set to the SCHC value
  are routed to the SCHC Instance for CoAP over UDP over IPv6. The SCHC Instance
  decompresses the SCHC packets and delivers them to the IPv6 layer.

Note that in this example, regular HTTP over QUIC traffic is also present on the
 same Endpoint. The Dispatch Engine is able to discriminate those packets from 
 packets that are compressed by SCHC, as the HTTP over QUIC packets do not
 not match the admission rules defined in the SCHC profile, here
 `UDP Destination Port == 5678`.


#### Case 2: The Dispatch engine lives outside of the network stack.

In this case, the Dispatch Engine is a separate component that
 interacts with multiple SCHC Instances. It is responsible for routing packets
 to the appropriate SCHC Instance based on the packet type and defined
 admission rules. 

 - On Linux, this can be implemented using netfilter hooks or similar mechanisms
  to intercept packets and route them to and from the appropriate SCHC Instance. 

 - On macOS, the Dispatch Engine can be implemented as a kernel extension or user-space
   application that make use of PF, the native packet filter.

 The exact implementation details of the Dispatch Engine will depend on the Operating System,
 which therefore is not specified in this document. However, a description of packets criteria
 and admission rules is provided in the SCHC profile, which is used by the Dispatch Engine
to determine how to route packets.

~~~~~~~
                         Endpoint
+------------------------------------------+
|   App. A     |    App. B   |    App. C   |
+--------------+-------------+-------------+
|     QUIC     |     CoAP    |     HTTP    |
+--------------|-------------+-------------|
|   Dispatch      Dispatch   |             |         Hooks
|    Hook 1    .   Hook 3    |             +           |
|  (dport 443)  (dport 5768) |             |           |
|              .             |             |  +------------------+
|             UDP            |     TCP     |  | Dispatch Engine  |
+----------------------------+-------------+  +------------------+
|             IPv6           |     IPv4    |   1 .     . 2
+----------------------------+-------------+     .     .
|  Dispatch    .   Dispatch  |             | +------------+ 
|   Hook 2          Hook 4   |             | |SCHC Inst.#1|
|   label      .    label    |             | +------------+
| 0xabcd0180      0xabce0180 |             | |   .     .  |
|                            |             | |   v     .  |
|             MPLS           |             | |compress .  |
+----------------------------+             | |         v  |
|          Ethertype                       | |  decompress|
|              ==                          | +------------+
|            0x8847                        |
|                  Ethernet                |
+------------------------------------------+

~~~~~~~

In the example above, the Dispatch Engine is implemented as a hook that intercepts
  packets based on their UDP destination port. In this instance, it routes packets with
  a destination port of 5768 to the SCHC Instance for CoAP over UDP. The Dispatch Engine
  then compresses the CoAP, UDP, and IPv6 headers, adds a MPLS header with appropriate tag
  and sends the compressed packet over the network.

When receiving packets, the Dispatch Engine checks the SCHC ethertype and MPLS label and routes
  matching packets (MPLS label == 0xabcd0180 && UDP destination port == 5768) them to the
  appropriate SCHC Instance based on the defined admission rules in the profile.


### Context Management

Context management is responsible for maintaining the shared state between
SCHC entities. This includes:

* Context synchronization between entities
* Rule lifecycle management
* Profile distribution and updates

## Optional Components

### Context Repository

A Context Repository provides centralized storage and management of SCHC
contexts and profiles. While not mandatory for minimal deployments, it
becomes essential for larger deployments requiring centralized management.

### Management Interface

A Management Interface provides operational control and monitoring
capabilities for SCHC deployments. This may include:

* Configuration management
* Performance monitoring
* Troubleshooting tools

# Deployment Patterns

## Point-to-Point Deployment

The simplest SCHC deployment involves two SCHC entities communicating
directly with each other. This pattern is suitable for:

* Simple device-to-server communications
* Gateway-to-cloud deployments
* Testing and development environments

## Hub-and-Spoke Deployment

In this pattern, multiple SCHC entities connect to a central hub entity.
This is common in:

* IoT deployments with multiple sensors
* Network gateway scenarios
* Hierarchical network topologies

## Distributed Deployment

For larger deployments, SCHC entities may be distributed across multiple
network domains with centralized context management.

# Security Considerations

Security considerations for SCHC minimal architecture include:

* Context integrity and authenticity
* Profile distribution security
* Protection against context manipulation attacks

# IANA Considerations

This document has no IANA actions.

# Acknowledgments

The authors would like to thank the SCHC Working Group for their
contributions and feedback on this document.

--- back

# Change Log

## Changes from -00 to -01

* Initial version
