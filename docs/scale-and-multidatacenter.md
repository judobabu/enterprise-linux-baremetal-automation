# Infrastructure Scale & Multi-Datacenter Architecture

## Overview

The bare-metal provisioning platform was designed to operate across geographically distributed enterprise infrastructure.

The architecture supports standardized provisioning across multiple datacenters while allowing each location to maintain its own compute, network, and storage infrastructure.

The platform was designed around scale, repeatability, operational consistency, and centralized automation.

## Enterprise Scale

The infrastructure environment represented by this reference architecture included approximately:

| Infrastructure Area                       | Approximate Scale |
| ----------------------------------------- | ----------------: |
| Primary Datacenters                       |                 3 |
| Regional Datacenters                      |          Multiple |
| Cisco UCS Domains                         |                14 |
| Blades per UCS Domain                     |              ~120 |
| Applications represented in CMDB          |              600+ |
| Bare-Metal Servers over platform lifetime |            1,000+ |
| Bare-Metal Nodes Provisioned Annually     |             ~800+ |

These figures represent the approximate scale of the enterprise environment and are presented for architectural context.

## Multi-Datacenter Architecture

```text
                         ┌─────────────────────┐
                         │ Application Teams   │
                         └──────────┬──────────┘
                                    |
                                    v
                         ┌─────────────────────┐
                         │ Provisioning Portal │
                         │ FastAPI / Python    │
                         └──────────┬──────────┘
                                    |
                                    v
                         ┌─────────────────────┐
                         │ Orchestration Layer │
                         └──────────┬──────────┘
                                    |
              ┌─────────────────────┼─────────────────────┐
              |                     |                     |
              v                     v                     v
       ┌─────────────┐       ┌─────────────┐       ┌─────────────┐
       │ Primary DC 1│       │ Primary DC 2│       │ Primary DC 3│
       └──────┬──────┘       └──────┬──────┘       └──────┬──────┘
              |                     |                     |
       Compute / Network /     Compute / Network /   Compute / Network /
          Storage                  Storage                Storage
              |
              +-------------------+
                                  |
                                  v
                         Regional Datacenters
```

## Centralized Orchestration

The provisioning platform provides a consistent automation interface regardless of the target datacenter.

The application team does not need to understand the individual infrastructure differences between locations.

Instead, the request identifies the target datacenter and the orchestration layer determines the appropriate infrastructure resources.

```text
Application Request
        |
        v
Target Datacenter
        |
        v
Datacenter-Specific Infrastructure
        |
        +---- UCS Domain
        +---- Network
        +---- Storage
        +---- PXE
        |
        v
Standardized Server
```

## UCS Domain Scale

The architecture supports multiple Cisco UCS domains distributed across the enterprise environment.

With approximately 14 UCS domains and roughly 120 blades per domain, the provisioning workflow must account for:

* Domain selection
* Available compute capacity
* Blade availability
* Service-profile configuration
* Network connectivity
* Storage connectivity
* Hardware state
* Provisioning readiness

Automation reduces the dependency on manual capacity discovery and hardware selection.

## Application Scale

The platform supports infrastructure requests associated with hundreds of enterprise applications.

With 600+ applications represented in the CMDB, application metadata becomes an important control point for provisioning.

The workflow uses application information to determine:

* Ownership
* Environment
* Component
* Infrastructure requirements
* Provisioning context

This allows infrastructure provisioning to remain aligned with application lifecycle information.

## Provisioning Throughput

The platform supported provisioning activity at enterprise scale, with approximately 800+ bare-metal nodes provisioned annually.

At this scale, manual provisioning becomes difficult to standardize.

Automation provides:

* Repeatable workflows
* Reduced manual intervention
* Consistent configuration
* Faster infrastructure delivery
* Centralized workflow control
* Better operational visibility

## Standardization Across Locations

A major architectural objective is to maintain common provisioning standards across geographically distributed infrastructure.

```text
                 Enterprise Standards
                         |
             ┌───────────┼───────────┐
             |           |           |
             v           v           v
          DC-1        DC-2        DC-3
             |           |           |
             v           v           v
        Standardized Provisioning
             |
             v
       Consistent Server Lifecycle
```

Location-specific infrastructure remains encapsulated within the datacenter integration layer.

This allows the provisioning workflow to remain consistent while infrastructure capacity and topology can differ between locations.

## Scalability Considerations

The architecture was designed so that infrastructure capacity could grow independently of the provisioning interface.

Additional compute capacity, UCS domains, storage resources, or datacenters can be incorporated without changing the fundamental application request model.

The separation between:

* Request
* Orchestration
* Infrastructure integration
* Datacenter resources

provides an architectural boundary for future expansion.

## Operational Resilience

A distributed infrastructure environment requires the provisioning workflow to account for infrastructure availability and failures.

The orchestration layer can validate infrastructure availability before proceeding and stop or report a workflow when a required dependency is unavailable.

This provides a controlled provisioning process rather than allowing partial infrastructure configuration to go unnoticed.

## Architectural Outcome

The multi-datacenter architecture demonstrates how infrastructure automation can scale beyond a single server, application, or datacenter.

The combination of centralized orchestration and location-specific infrastructure integrations provides a repeatable model for provisioning enterprise infrastructure across geographically distributed environments.

## Portfolio Note

This document represents a sanitized reference architecture based on enterprise infrastructure architecture and automation experience.

Infrastructure counts are approximate and are provided to demonstrate architectural scale. Internal datacenter names, hostnames, IP addresses, application identifiers, hardware identifiers, and proprietary configuration details have been intentionally excluded.

## Capacity and Scaling Model

The provisioning architecture separates application demand from physical infrastructure capacity.

This allows infrastructure capacity to expand while maintaining a consistent provisioning interface for application teams.

### Scaling Layers

```text id="v4k9p2"
                    Application Demand
                           |
                           v
                  Provisioning Requests
                           |
                           v
                   Orchestration Layer
                           |
             ┌─────────────┼─────────────┐
             |             |             |
             v             v             v
         Datacenter      UCS Domain    Storage
          Capacity        Capacity     Capacity
             |             |             |
             └─────────────┼─────────────┘
                           |
                           v
                   Provisioned Servers
```

## Compute Capacity

Cisco UCS domains provide a scalable compute layer.

The provisioning workflow can evaluate available capacity across the appropriate UCS domain before assigning a physical blade.

At enterprise scale, this avoids relying on manual spreadsheets or individual administrators to determine which hardware is available.

Conceptually:

```text id="6j3s8x"
Target Datacenter
       |
       v
Available UCS Domains
       |
       v
Available Compute Capacity
       |
       v
Select Suitable Blade
       |
       v
Configure Service Profile
```

## Datacenter Expansion

A new datacenter can be incorporated by adding the required infrastructure integrations and capacity.

```text id="x7d2m4"
Existing Platform
      |
      +---- Datacenter 1
      +---- Datacenter 2
      +---- Datacenter 3
      |
      v
New Datacenter
      |
      +---- Network Integration
      +---- Storage Integration
      +---- Compute Integration
      +---- PXE Integration
      |
      v
Existing Orchestration Model
```

The application-facing provisioning workflow remains largely unchanged.

## Application Growth

As the number of applications increases, CMDB-driven provisioning becomes increasingly important.

The platform uses application metadata to maintain consistent provisioning context.

```text id="n2f8w6"
More Applications
       |
       v
More Provisioning Requests
       |
       v
Same Standardized Request Model
       |
       v
Same Orchestration Workflow
       |
       v
Scalable Infrastructure
```

This allows infrastructure automation to scale with application demand without creating a separate manual process for each application.

## Provisioning Volume

The platform supported approximately 800+ bare-metal nodes per year.

At this volume, automation provides an operational model where the provisioning process can be repeated consistently rather than depending on individual administrators to execute each step manually.

The architecture therefore focuses on:

* Standard workflows
* Reusable infrastructure profiles
* Automated resource allocation
* API-driven integrations
* Validation between provisioning stages
* Centralized orchestration

## Horizontal Scaling Model

The platform can be viewed as a horizontally scalable architecture:

```text id="p8v1c5"
                  Orchestration Layer
                         |
        ┌────────────────┼────────────────┐
        |                |                |
        v                v                v
    Datacenter A     Datacenter B     Datacenter C
        |                |                |
        v                v                v
    UCS / Storage     UCS / Storage    UCS / Storage
        |                |                |
        v                v                v
     Servers          Servers          Servers
```

The provisioning workflow remains standardized while infrastructure resources scale underneath it.

## Architectural Principle

The key scaling principle is:

> **Scale infrastructure capacity without increasing provisioning complexity for the application team.**

This is achieved by keeping the application request model and orchestration workflow independent from the physical infrastructure topology.

## Portfolio Perspective

The architecture demonstrates how infrastructure automation can evolve from individual server provisioning into an enterprise platform capable of supporting multiple datacenters, hundreds of applications, multiple UCS domains, and high annual provisioning volume.

## Reliability and Failure Handling

At enterprise scale, infrastructure provisioning must account for failures across multiple independent systems.

The architecture therefore treats major provisioning operations as controlled stages with validation and failure boundaries.

### Failure Boundary Model

```text id="n8q4w2"
Request
   |
   v
CMDB Validation
   |
   v
Network
   |
   +---- Failure ---> Stop / Report
   |
   v
Storage
   |
   +---- Failure ---> Stop / Report
   |
   v
Compute
   |
   +---- Failure ---> Stop / Report
   |
   v
OS Deployment
   |
   +---- Failure ---> Stop / Report
   |
   v
Configuration
   |
   +---- Failure ---> Stop / Report
   |
   v
CMDB Registration
```

A failed dependency should prevent the workflow from blindly continuing into subsequent stages.

## Validation Before Progression

Each major provisioning stage should produce a validated result before the next stage begins.

For example:

```text id="t5c7r1"
Allocate IP
    |
    v
Validate IP
    |
    v
Configure Storage
    |
    v
Validate Storage
    |
    v
Configure Compute
    |
    v
Validate Compute
    |
    v
Start OS Deployment
```

This approach reduces the risk of creating partially configured infrastructure.

## Infrastructure Availability

Before provisioning begins, the orchestration layer can evaluate whether required infrastructure resources are available.

Examples include:

* UCS compute capacity
* Storage availability
* Network/IP availability
* PXE availability
* Required integration services

If a required resource is unavailable, the request can be stopped or reported rather than creating an incomplete server.

## Retry and Recovery

Transient infrastructure failures may require controlled retry or recovery behavior.

The architecture can distinguish between:

**Transient failures**

Examples:

* Temporary API connectivity issue
* Temporary service unavailability
* Network communication timeout

**Configuration failures**

Examples:

* Invalid provisioning parameters
* Invalid CMDB information
* Invalid infrastructure profile

**Capacity failures**

Examples:

* No available UCS blade
* Insufficient storage capacity
* No suitable network resources

Different failure types can be handled through appropriate operational procedures rather than treating every failure identically.

## Operational State Tracking

A provisioning request can maintain a high-level state throughout its lifecycle.

```text id="r6x2k9"
REQUESTED
    |
VALIDATING
    |
PROVISIONING
    |
CONFIGURING
    |
REGISTERING
    |
COMPLETED
```

If an error occurs:

```text id="8q1m5v"
PROVISIONING
      |
      v
    FAILED
      |
      v
Operational Review / Recovery
```

This provides a clear operational boundary for troubleshooting.

## Multi-Datacenter Resilience

Because infrastructure is distributed across multiple datacenters, the provisioning architecture avoids assuming that all infrastructure services are located in a single physical location.

The target datacenter determines the infrastructure resources and integrations used for the request.

This provides
