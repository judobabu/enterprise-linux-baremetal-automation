# Architecture Decisions

## Overview

The provisioning platform was designed as an integration and orchestration layer rather than as a collection of independent infrastructure scripts.

Technology choices were driven by requirements such as API integration, automation, scalability, maintainability, enterprise integration, and operational consistency.

This document summarizes the major architectural decisions.

## Decision Summary

| Technology        | Architectural Role       | Primary Reason                              |
| ----------------- | ------------------------ | ------------------------------------------- |
| Python            | Orchestration            | Flexible integration and automation         |
| FastAPI           | API / Service Layer      | Lightweight API-driven architecture         |
| Ansible           | Configuration Management | Repeatable post-install configuration       |
| ServiceNow CMDB   | Application Metadata     | Central application and ownership context   |
| Infoblox          | Network / DNS            | Automated IP and DNS management             |
| Cisco UCS SDK/API | Compute                  | Automated bare-metal provisioning           |
| NetApp ONTAP API  | Storage                  | Automated storage provisioning              |
| PXE / Kickstart   | OS Deployment            | Automated operating-system installation     |
| Jenkins           | CI/CD                    | Automation testing and workflow integration |

## Python for Orchestration

### Decision

Python was selected as the primary orchestration language.

### Rationale

Python provides a strong ecosystem for infrastructure automation and integration with enterprise platforms.

The architecture benefits from:

* REST API support
* Vendor SDK availability
* Infrastructure automation libraries
* Ansible integration
* Rapid development
* Readable implementation
* Strong support for structured data formats

Python also provides a common implementation layer for coordinating heterogeneous infrastructure systems.

## FastAPI for the Service Layer

### Decision

FastAPI was used as the API layer for the provisioning platform.

### Rationale

The provisioning platform required a service interface that could receive structured requests and invoke the appropriate orchestration workflow.

FastAPI provides:

* REST API support
* Structured request models
* Automatic API documentation
* Python-native development
* Lightweight service architecture
* Easy integration with orchestration logic

The API layer creates a clear boundary between the web interface and infrastructure automation.

## Ansible for Post-Installation Configuration

### Decision

Ansible was used for post-installation server configuration.

### Rationale

Operating-system installation and enterprise configuration are separate concerns.

PXE/Kickstart establishes the operating system, while Ansible applies the enterprise configuration baseline.

This separation provides:

```text id="2c8n5v"
OS Deployment
      |
      v
Ansible Configuration
      |
      +---- Packages
      +---- Users / Access
      +---- AD Integration
      +---- Security
      +---- Monitoring
      +---- Patch Management
      +---- Application Prerequisites
```

Ansible also supports repeatability and reduces configuration drift.

## ServiceNow CMDB

### Decision

ServiceNow CMDB was used as the application metadata and ownership reference.

### Rationale

Infrastructure provisioning needs application context.

The CMDB provides information such as:

* Application identity
* Application ownership
* Environment
* Component relationships
* Infrastructure relationships

Using existing enterprise metadata reduces duplicate sources of truth.

## Infoblox API Integration

### Decision

Infoblox APIs were integrated into the provisioning workflow.

### Rationale

Network and DNS allocation are fundamental dependencies for server provisioning.

Automating these operations provides:

* Consistent IP allocation
* Automated DNS registration
* Reduced manual coordination
* Integration with hostname generation
* Improved provisioning repeatability

## Cisco UCS Integration

### Decision

Cisco UCS APIs/SDK capabilities were used to automate compute provisioning.

### Rationale

The enterprise environment contained multiple UCS domains and significant physical compute capacity.

Automation was therefore used to:

* Discover available compute
* Select an appropriate UCS domain
* Identify available blades
* Configure service profiles
* Configure connectivity
* Attach and power on compute resources

This makes physical server provisioning part of the automated lifecycle.

## NetApp ONTAP Integration

### Decision

NetApp ONTAP APIs were integrated for storage provisioning.

### Rationale

Storage allocation was another major dependency in the bare-metal provisioning process.

The integration enables automation of storage operations such as:

* Volume creation
* LUN creation
* Initiator configuration
* iGroup operations
* LUN mapping

This allows compute and storage provisioning to be coordinated through the same workflow.

## PXE / Kickstart

### Decision

PXE-based operating-system deployment was used as part of the provisioning workflow.

### Rationale

PXE allows physical servers to begin operating-system deployment without requiring manual installation media.

Kickstart or equivalent automated installation mechanisms provide repeatable OS deployment and reduce installation variance.

## Jenkins

### Decision

Jenkins was used to support automation workflows and testing.

### Rationale

Infrastructure automation requires validation of changes before they are introduced into operational workflows.

Jenkins provides a mechanism for:

* Automated testing
* Workflow execution
* Validation
* Build integration
* Repeatable automation processes

## Separation of Responsibilities

The overall architecture intentionally assigns different responsibilities to different technologies.

```text id="k1w7m3"
FastAPI / Python
       |
       | Orchestration
       v
Infrastructure APIs
       |
       +---- ServiceNow
       +---- Infoblox
       +---- NetApp
       +---- Cisco UCS
       |
       v
PXE / OS Deployment
       |
       v
Ansible
       |
       | Configuration
       v
Enterprise-Ready Server
```

This separation avoids making a single technology responsible for the entire infrastructure lifecycle.

## Architectural Principle

The primary architectural principle is:

> **Use each technology for the infrastructure responsibility it is best suited to perform, and use an orchestration layer to coordinate the complete lifecycle.**

This approach creates clear integration boundaries and allows individual infrastructure technologies to evolve independently.

## Portfolio Note

This document represents a sanitized architectural view based on enterprise infrastructure automation experience.

It describes technology roles and architectural decisions without exposing proprietary source code, credentials, internal endpoints, or production-specific configuration.

## Architecture Trade-offs

Enterprise infrastructure architecture involves balancing automation, flexibility, operational control, maintainability, and integration complexity.

The platform design considered these trade-offs when determining where automation should be applied.

### Centralized vs. Distributed Automation

**Approach:** Centralized orchestration with distributed infrastructure integrations.

```text id="r5m8k2"
             Central Orchestration
                     |
       ┌─────────────┼─────────────┐
       v             v             v
    Datacenter A  Datacenter B  Datacenter C
```

**Trade-off:**

Centralizing the workflow provides consistent provisioning and governance, while datacenter-specific integrations preserve the ability to work with local infrastructure.

This avoids duplicating the entire provisioning application for each datacenter.

### Automation vs. Flexibility

**Approach:** Standard provisioning profiles with controlled exceptions.

Most requests follow standardized infrastructure profiles, while specialized requirements can trigger additional workflows.

**Trade-off:**

Highly standardized workflows improve consistency, but enterprise applications sometimes require custom infrastructure.

The architecture therefore supports controlled customization without turning every request into a unique provisioning process.

### API Integration vs. Manual Operations

**Approach:** API-driven infrastructure provisioning.

**Trade-off:**

API integration requires additional development and maintenance for each infrastructure platform.

However, once integrated, repetitive manual operations can be replaced with consistent automated workflows.

This is particularly valuable when provisioning occurs at significant enterprise volume.

### Orchestration vs. Platform Ownership

**Approach:** The orchestration layer coordinates infrastructure platforms but does not replace their native responsibilities.

For example:

```text id="u8c4n1"
Orchestration
     |
     +---- ServiceNow owns CMDB data
     +---- Infoblox owns IP/DNS
     +---- NetApp owns storage
     +---- Cisco UCS owns compute
     +---- Ansible owns configuration
```

**Trade-off:**

Keeping platform ownership separate increases integration complexity, but it prevents the orchestration layer from becoming responsible for capabilities that already belong to established enterprise systems.

### Automation vs. Validation

**Approach:** Validate important infrastructure state before advancing the workflow.

**Trade-off:**

Additional validation introduces processing steps and can make workflows more complex.

However, validation reduces the risk of continuing with incomplete or failed infrastructure provisioning.

### Build vs. Integrate

**Approach:** Integrate with existing enterprise platforms wherever practical.

The architecture uses existing capabilities from systems such as ServiceNow, Infoblox, NetApp, Cisco UCS, and Ansible.

**Trade-off:**

Integration creates dependencies on external platforms and their APIs.

The benefit is avoiding unnecessary duplication of capabilities that are already established within the enterprise environment.

## Key Architectural Principle

The overall design favors:

```text id="e3v9c6"
Standardize
    +
Automate
    +
Integrate
    +
Validate
    +
Allow Controlled Exceptions
```

This balance enables the platform to provide repeatable infrastructure provisioning while remaining practical for complex enterprise environments.

## Portfolio Perspective

These trade-offs illustrate the architectural considerations involved in building an enterprise provisioning platform.

The objective was not simply to automate individual infrastructure tasks, but to create a maintainable orchestration model that could operate across multiple infrastructure domains and datacenters.

## Executive Architecture Summary

The provisioning platform can be summarized as a layered architecture:

```text id="m7c2v8"
┌──────────────────────────────────────────────┐
│              Application Teams               │
└──────────────────────┬───────────────────────┘
                       |
                       v
┌──────────────────────────────────────────────┐
│              Web UI / FastAPI                │
│                 Python                       │
└──────────────────────┬───────────────────────┘
                       |
                       v
┌──────────────────────────────────────────────┐
│          Infrastructure Orchestration        │
└──────────────────────┬───────────────────────┘
                       |
        ┌──────────────┼──────────────┐
        |              |              |
        v              v              v
   ServiceNow      Network/DNS     Storage
      CMDB          Infoblox       NetApp
        |                              |
        └──────────────┬───────────────┘
                       |
                       v
                  Cisco UCS
                    Compute
                       |
                       v
                  PXE / OS
                       |
                       v
                   Ansible
                 Configuration
                       |
                       v
                Enterprise Server
```

### Core Architectural Decisions

**1. API-driven orchestration**

A Python/FastAPI layer provides a consistent interface for infrastructure provisioning.

**2. Existing enterprise systems as domain authorities**

ServiceNow, Infoblox, NetApp, Cisco UCS, and Ansible retain responsibility for their respective domains.

**3. Automated infrastructure lifecycle**

Network, storage, compute, operating-system deployment, and post-install configuration are coordinated through a single workflow.

**4. Standardization with controlled flexibility**

Common provisioning profiles provide consistency while allowing specialized infrastructure requirements.

**5. Validation at infrastructure boundaries**

The workflow validates critical operations before proceeding to dependent stages.

**6. Multi-datacenter scalability**

The same provisioning model can operate across geographically distributed infrastructure while maintaining datacenter-specific integrations.

## Architect's Perspective

The central design objective was to transform a traditionally multi-team, manually coordinated infrastructure process into a standardized platform workflow.

The resulting architecture connects:

```text id="v2x6p4"
Application Context
        +
Infrastructure APIs
        +
Automation
        +
Configuration Management
        =
Repeatable Enterprise Provisioning
```

The architecture demonstrates how application requirements can be translated into coordinated infrastructure operations while maintaining governance, standardization, and operational control.

## Portfolio Note

This is a sanitized reference architecture based on enterprise infrastructure architecture and automation experience.

Production implementation details, credentials, internal endpoints, application names, network information, and proprietary source code have been intentionally excluded.
