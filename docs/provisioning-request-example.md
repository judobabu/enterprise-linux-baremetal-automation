# Provisioning Request Example

## Overview

The bare-metal provisioning platform begins with a standardized infrastructure request submitted by an application team.

The request captures the information required to determine the target infrastructure, generate the server identity, allocate network and storage resources, provision the compute platform, install the operating system, and apply enterprise configuration.

The objective is to minimize manual infrastructure coordination while maintaining governance through application ownership and CMDB validation.

## Example Request

A simplified example of a provisioning request is shown below.

```text
Application:
TCS Application

Application Owner:
Application owner defined in ServiceNow CMDB

Datacenter:
Primary Enterprise Datacenter

Landscape:
DEV

Network Zone:
Internal

Component:
APP

Operating System:
RHEL 9

CPU:
Enterprise-standard application profile

Memory:
Enterprise-standard application profile

Storage:
Standard OS boot storage

Additional Requirements:
Standard enterprise configuration
```

> The values above are illustrative and do not represent production configuration.

## Request Attributes

| Attribute               | Purpose                                             |
| ----------------------- | --------------------------------------------------- |
| Application             | Identifies the application requiring infrastructure |
| Application Owner       | Provides ownership and accountability               |
| Datacenter              | Determines the target infrastructure location       |
| Landscape               | Identifies DEV, TST, STG, or PRD                    |
| Network Zone            | Determines the required network/security segment    |
| Component               | Identifies APP, DB, or another infrastructure role  |
| Operating System        | Determines the OS deployment workflow               |
| CPU / Memory            | Determines the compute profile                      |
| Storage                 | Determines storage requirements                     |
| Additional Requirements | Captures application-specific infrastructure needs  |

## Validation Workflow

Before infrastructure is provisioned, the request is validated against enterprise application metadata.

```text
Provisioning Request
        |
        v
ServiceNow CMDB Validation
        |
        +---- Application exists?
        |
        +---- Owner validated?
        |
        +---- Environment validated?
        |
        +---- Component validated?
        |
        v
Infrastructure Planning
        |
        +---- Hostname
        +---- Network
        +---- Storage
        +---- Compute
        |
        v
Provisioning Workflow
```

## CMDB as the Source of Application Context

ServiceNow CMDB provides the application context required by the provisioning workflow.

The platform uses CMDB information to validate application ownership and relationships before infrastructure resources are allocated.

This helps prevent infrastructure from being provisioned against incomplete or invalid application information.

## Infrastructure Mapping

The request is translated into infrastructure actions.

| Request Information                   | Infrastructure Action              |
| ------------------------------------- | ---------------------------------- |
| Application + Component + Environment | Generate hostname                  |
| Datacenter + Network Zone             | Select network configuration       |
| Host identity                         | Allocate IP/DNS through Infoblox   |
| Storage requirement                   | Create and map NetApp storage      |
| Hardware requirement                  | Select available Cisco UCS compute |
| Operating System                      | Select PXE/Kickstart deployment    |
| Enterprise configuration              | Apply Ansible roles                |
| Application metadata                  | Register/update CMDB               |

## Example Hostname

For the example request, the platform could generate:

```text
phytcsapp01-dev
```

The hostname follows the standardized naming convention documented in the hostname design.

## Special Infrastructure Requirements

The request model can support additional infrastructure requirements when necessary.

Examples include:

* Additional network interfaces
* Database-specific networking
* Additional IP addresses
* Multiple storage paths
* Application-specific storage
* Specialized compute requirements

For example, a database or RAC-related request may require additional network identities beyond the standard provisioning profile.

## Standard vs. Custom Provisioning

The platform separates common enterprise infrastructure requirements from application-specific requirements.

### Standard

* Hostname
* IP/DNS
* Boot storage
* Compute allocation
* OS installation
* Enterprise authentication
* Security agents
* Monitoring
* Patch management
* Standard configuration

### Application-Specific

* Additional storage
* Additional IP addresses
* Database/RAC networking
* Application prerequisites
* Specialized hardware requirements

This approach allows the majority of provisioning requests to follow a consistent automated workflow while still supporting application-specific infrastructure needs.

## Request-to-Infrastructure Lifecycle

```text
Application Team
       |
       v
Standardized Request
       |
       v
CMDB / Ownership Validation
       |
       v
Infrastructure Planning
       |
       +------ Hostname
       +------ Network / DNS
       +------ Storage
       +------ Compute
       |
       v
OS Provisioning
       |
       v
Enterprise Configuration
       |
       v
CMDB Registration
       |
       v
Application Owner Notification
```

## Architectural Benefits

A standardized provisioning request provides:

* Consistent infrastructure requirements
* Reduced manual coordination
* CMDB-driven governance
* Repeatable provisioning
* Clear application ownership
* Support for multiple environments
* Support for multiple datacenters
* Integration with automated infrastructure workflows
* Reduced configuration errors

## Portfolio Note

This is a sanitized reference example based on enterprise infrastructure provisioning architecture. Production application names, internal identifiers, IP addresses, DNS zones, hardware identifiers, and other proprietary information have been intentionally excluded.
