# Hostname Design

## Overview

The bare-metal provisioning platform automatically generates standardized hostnames based on application, component, instance, and environment information.

The hostname is generated during the provisioning workflow before network, storage, compute, and operating system configuration begins.

## Hostname Pattern

The standardized naming pattern is:

```text
<physical/virtual><application><component><instance>-<environment>
```

Example:

```text
phytcsapp01-dev
```

### Example Breakdown

| Element     | Example | Description             |
| ----------- | ------- | ----------------------- |
| Platform    | `phy`   | Physical server         |
| Application | `tcs`   | Application identifier  |
| Component   | `app`   | Application component   |
| Instance    | `01`    | Instance number         |
| Environment | `dev`   | Development environment |

## Supported Environments

The naming workflow supports enterprise application landscapes such as:

* DEV
* TST
* STG
* PRD

The environment is derived from the provisioning request and validated against the application metadata maintained in the CMDB.

## Hostname Generation Flow

```text
Application Request
        |
        v
ServiceNow CMDB Validation
        |
        v
Application / Component / Environment
        |
        v
Hostname Generation
        |
        v
Hostname Uniqueness Validation
        |
        v
Infoblox DNS / IP Registration
        |
        v
Server Provisioning
```

## Design Considerations

The hostname generation process was designed to provide:

* Consistent enterprise naming standards
* Unique host identification
* Application and environment traceability
* Integration with DNS and IP management
* Compatibility with CMDB records
* Reduced manual naming errors
* Consistent naming across multiple datacenters

## Enterprise Integration

The generated hostname becomes a key identifier used across the provisioning lifecycle.

It is referenced by infrastructure automation components including:

* ServiceNow CMDB
* Infoblox DNS/IP management
* PXE/DHCP provisioning
* Ansible configuration management
* Monitoring and security platforms
* Application infrastructure records

## Multi-Datacenter Considerations

The hostname design is intended to remain consistent across geographically distributed datacenters.

Datacenter-specific information is maintained through the provisioning and infrastructure systems rather than making the hostname unnecessarily complex.

This allows the same naming strategy to be applied across multiple enterprise infrastructure locations.

## Architectural Outcome

Automated hostname generation provides a deterministic naming mechanism that connects application metadata with the physical infrastructure being provisioned.

This was an important part of the overall provisioning architecture because hostname generation occurs early in the workflow and the resulting identity is used throughout the remaining infrastructure lifecycle.

## Portfolio Note

This document represents a sanitized reference architecture based on enterprise infrastructure automation experience. Production-specific application names, hostnames, DNS zones, IP addresses, and other proprietary information have been intentionally excluded.
