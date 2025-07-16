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

Static Context Header Compression and fragmentation (SCHC) framework
provides both a header compression mechanism and an optional fragmentation mechanism. 
SCHC operation is based on a common static Context stored at the two (or more) ends of a communication.

This document defines a minimal architecture for SCHC deployments, providing guidance
for implementers and operators on the essential components and their interactions
required for effective SCHC operation. The minimal architecture addresses the
fundamental requirements for SCHC deployment while maintaining flexibility for
various use cases and environments.

--- middle

# Introduction        

The SCHC Working Group has developed the {{RFC8724}} SCHC technology
for Low-Power Wide-Area (LPWA) networks, providing efficient header compression
and fragmentation mechanisms. As SCHC adoption expands beyond its original scope,
there is a need to define a minimal architecture that identifies only the essential
elements required for proper SCHC operation.

A minimal architecture for SCHC defines the essential components, their relationships,
and the minimum requirements for a functional SCHC deployment. This architecture
serves as a foundation that can be extended based on specific deployment requirements
and use cases.

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

**SCHC Instance**: A logical component that implements the SCHC protocol,
             including header compression, fragmentation, and context management.

**SCHC Domain**: A network domain where SCHC instances share a common context for 
            compression and fragmentation operations.

**SCHC Domain Manager**: A logical component that manages the SCHC domain,
                   including context synchronization and profile distribution.

**Context Repository**: A logical component that stores and manages SCHC
                   contexts and rules used by SCHC entities.

**Profile**: A set of parameters and rules that define how SCHC operations
         are performed within a specific deployment scenario.

# Minimal Architecture Components

## Core Components

### SCHC Instance

A SCHC Instance is the fundamental component that implements the SCHC protocol
  as defined in {{RFC8724}}. A Network host MAY executes several SCHC Instances
  in its protocol stack. Every SCHC deployment MUST include at least two
  SCHC instances to establish a compression/decompression relationship.

The functional architecture of a SCHC Instance is depicted in the following figure:

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
* Context Minimal Manager
* Profile Manager


A SCHC Instance MAY implement:

* Fragmentation and Reassembly (F/R) functionality
* Dynamic context update mechanisms
* Performance monitoring and reporting

#### Header Compression and Decompression (C/D) engine

This component is responsible for compressing and decompressing headers
  using the SCHC protocol, as described in {{RFC8724}}. It applies the rules defined 
  in the SCHC Context.
  
The C/D engine MUST expose the following interface:

- `compress(buffer, context, profile)`: Compresses the provided buffer using the SCHC Context
  and the profile.
- `decompress(buffer, context, profile)`: Decompresses the provided buffer using the SCHC Context
  and the profile.

Internally, on compression, the C/D engine:

- delineates the fields using the parser identified in the SCHC Context.
- elects the appropriate compression rules based on the SCHC Context and the matching policy
   defined in the profile.
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
- `dispatch_decompress(buffer, context, profile)`: dispatch the compressed packet 
    to the correct recipient, .e.g. application or protocol routine.

**Dispatch scenarios**:

#### Case 1: The Dispatch Engine is integrated into the network stack / single SCHC instance.
  
~~~~~~
          Endpoint 1                          Endpoint 2    
+---------------------------+       +---------------------------+
|   App. A    |    App. B   |       |   App. A    |    App. B   |
+---------------------------+       +---------------------------+
|             |   ||   UDP          |             |   ||   UDP 
|             |   || DstPort        |             |   || DstPort   
|    HTTP     |   ||    ==          |    HTTP     |   ||    ==     
|             |   || 5678(CoAP)     |             |   || 5678(CoAP)
+-------------|-------------+       +-------------|-------------+
|    QUIC     |             |       |    QUIC     |             |
+-------------|     SCHC    |       +-------------|     SCHC    |
|    IPv6     |             |       |    IPv6     |             |
+---------------------------+       +---------------------------+
Ethertype ||         || Ethertype  Ethertype ||          || Ethertype
    ==    ||         ||   ==            ==   ||          ||   ==
  0x86DD  ||         ||  SCHC         0x86DD ||          ||  SCHC    
+---------------------------+       +---------------------------+
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

In this simple scenario, the Dispatch Engine is integrated into the network stack and there is 
 a predefined SCHC Instance for a specific protocol stack, such as CoAP over UDP over IPv6. 
 This is the classic case for SCHC over LPWAN networks, as described in {{RFC8724}}, {{RFC8824}},
 {{RFC9363}}.

The dispatching is done based on a identified header field, such as the an ethertype, 
 the IPv6 Next Header field, a specific UDP port. 

In the example above, 

* the Dispatch "intercepts" outbound packets whose UDP destination port is 5678, which is used by CoAP. 
 It then routes these packets to the SCHC Instance for CoAP over UDP over IPv6. The SCHC instance then 
 compresses the CoAP, UDP and IPv6 headers, and delivers the compressed packet to the Dispatch Engine, 
 which then sends it over the network, setting the appropriate SCHC ethertype in the link layer header.

* Packets received from the network that match the SCHC ethertype are processed in the reverse order. 
 The Dispatch Engine receives the SCHC packet and routes it to the SCHC Instance for CoAP over UDP over IPv6.
 The SCHC Instance then decompresses the packet and delivers it to the appropriate application or protocol routine.


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
+--------------------------------------------------------+
|   App. A     |    App. C   |    App. D   |    App. E   |
+--------------+-------------+-------------+-------------+
|     QUIC     |     CoAP    |    MQTT     |     HTTP    |
+--------------|-------------|-------------+-------------|
|                 Dispatch                 |             |
|                  Hook 1                  |             |
|               (dport 5768)               |             |
|                                          |             |
|                    UDP                   |     TCP     |
+------------------------------------------+-------------+
|                   IPv6                   |     IPv4    |
+------------------------------------------+-------------+
|                   MPLS                   |             |
+------------------------------------------+             |
|                         Ethernet                       |
+--------------------------------------------------------+

~~~~~~~



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
