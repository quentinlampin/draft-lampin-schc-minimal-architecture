---
title: SCHC Minimal Architecture
# abbrev: SCHC-Min-Arch
docname: draft-lampin-schc-minimal-architecture-00
date: 2025-07-11
# date: 2025-07
# date: 2025

# stand_alone: true

ipr: trust200902
area: Internet
wg: SCHC Working Group
kw: Internet-Draft
cat: std

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
        # abbrev: Orange
        # street:
        # - Postfach 330440
        # - Bibliothekstr. 1
        street: Orange Labs - 22 Chemin du Vieux Chene
        city: Meylan
        code: 38240
        country: France
        phone: "+33-6-7891-5678"
        # facsimile: +49-421-218-7000
        email: quentin.lampin@orange.com

normative:
  RFC8724:
    title: "SCHC: Generic Framework for Static Context Header Compression and Fragmentation"
    author:
      - 
        name: Ana Minaburo
      -
        name: Laurent Toutain
    date: 2020-04
    seriesinfo:
      RFC: 8724
  RFC2119:
    title: Key words for use in RFCs to Indicate Requirement Levels
    author:
      -
        name: Scott Bradner
    date: 1997-03
    seriesinfo:
      RFC: 2119
  RFC8174:
    title: Ambiguity of Uppercase vs Lowercase in RFC 2119 Key Words
    author:
      -
        name: Barry Leiba
    date: 2017-05
    seriesinfo:
      RFC: 8174

informative:
  RFC9363:
    title: LPWAN Static Context Header Compression (SCHC) over LoRaWAN
    author:
      -
        name: Ivaylo Petrov
    date: 2023-03
    seriesinfo:
      RFC: 9363

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
there is a need to define a minimal architecture that can guide implementers
and operators in deploying SCHC effectively.

A minimal architecture for SCHC defines the essential components, their relationships,
and the minimum requirements for a functional SCHC deployment. This architecture
serves as a foundation that can be extended based on specific deployment requirements
and use cases.

This document provides:

* A definition of the minimal components required for SCHC operation
* The essential interactions between these components
* Guidance on deployment patterns and best practices
* Considerations for scalability and extensibility

# Requirements Notation

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
"SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in BCP 14 {{RFC2119}} {{RFC8174}} 
when, and only when, they appear in all capitals, as shown here.

# Terminology

This section defines terminology and abbreviations used in this
document. It extends the terminology of {{RFC8724}}.

SCHC Entity: A network node that implements SCHC functionality, capable of
             performing header compression/decompression and optionally
             fragmentation/reassembly.

SCHC Domain: A network domain where SCHC entities share common context
             and profiles for compression and fragmentation operations.

Context Repository: A logical component that stores and manages SCHC
                   contexts and rules used by SCHC entities.

Profile: A set of parameters and rules that define how SCHC operations
         are performed within a specific deployment scenario.

# Minimal Architecture Components

## Core Components

### SCHC Entity

A SCHC Entity is the fundamental component that implements the SCHC protocol
as defined in {{RFC8724}}. Every SCHC deployment MUST include at least two
SCHC entities to establish a compression/decompression relationship.

A SCHC Entity MUST implement:

* Header compression and decompression functionality
* Context and rule management
* Profile configuration capability

A SCHC Entity MAY implement:

* Fragmentation and reassembly functionality
* Dynamic context update mechanisms
* Performance monitoring and reporting

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

# Examples

## Example 1: Basic Point-to-Point Deployment

This example shows a minimal SCHC deployment between two entities:

```
  Device A                    Device B
  +----------+              +----------+
  |  SCHC    |<------------>|  SCHC    |
  |  Entity  |              |  Entity  |
  +----------+              +----------+
       |                         |
       v                         v
  [Context]                 [Context]
  [Profile]                 [Profile]
```

## Example 2: Hub-and-Spoke Deployment

This example shows a deployment with multiple devices connecting to a central hub:

```
  Device A     Device B     Device C
  +------+     +------+     +------+
  | SCHC |     | SCHC |     | SCHC |
  +------+     +------+     +------+
      |            |            |
      +------------+------------+
                   |
              +----------+
              |   SCHC   |
              |   Hub    |
              +----------+
                   |
            [Context Repository]
```

# Change Log

## Changes from -00 to -01

* Initial version
