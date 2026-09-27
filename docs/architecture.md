# Architecture

## 1. Architecture Objective

The platform was designed to provide an automated, standardized, and repeatable approach for provisioning enterprise Linux bare-metal infrastructure.

The architecture brings together compute, network, storage, operating system deployment, configuration management, enterprise ITSM, and operational tooling into a single provisioning workflow. The objective is to reduce manual infrastructure activities, improve build consistency, accelerate server delivery, and provide an auditable lifecycle from application request through production readiness.

The reference architecture is designed around API-driven orchestration and modular automation, allowing individual infrastructure services to evolve independently while remaining part of a common provisioning workflow.

---

## 2. High-Level Architecture

The solution follows a layered architecture:

```text
Application Team
       |
       v
Web Portal
React / Angular
       |
       v
FastAPI / Python Orchestrator
       |
       +------------------+------------------+------------------+
       |                  |                  |                  |
       v                  v                  v                  v
 ServiceNow           Infoblox          Cisco UCS          NetApp ONTAP
   CMDB                DNS/IPAM            Compute             Storage
       |                  |                  |                  |
       +------------------+------------------+------------------+
                              |
                              v
                     PXE / DHCP / Kickstart
                              |
                              v
                      Linux Bare-Metal
                              |
                              v
                         Ansible
                              |
                              v
                 Enterprise Configuration
                              |
                              v
                  CMDB Registration /
                  Owner Notification
```

Jenkins is used as part of the development and validation lifecycle for automation components.

---

## 3. Architectural Components

### Web Portal

The web portal provides a self-service interface through which application teams can request infrastructure.

The request captures infrastructure requirements such as:

* Application
* Datacenter
* Landscape
* Network zone
* Component type
* Operating system
* Hardware requirements
* Storage requirements

The portal is designed to hide infrastructure complexity from application teams while enforcing standardized provisioning workflows.

### FastAPI and Python Orchestration

FastAPI provides the API layer and Python provides the primary orchestration logic.

The orchestration layer coordinates the provisioning lifecycle rather than embedding infrastructure-specific functionality into a single monolithic implementation.

Responsibilities include:

* Request validation
* Application and ownership validation
* Hostname generation
* Provisioning workflow coordination
* API integration
* State management
* Error handling
* Provisioning status tracking

This separation allows compute, network, storage, and configuration automation to remain modular.

---

## 4. ServiceNow CMDB Integration

ServiceNow CMDB acts as an enterprise source of application and ownership information.

The provisioning workflow can use CMDB information such as:

* Application name
* Application code
* Business owner
* Support owner
* Support group
* Cost center
* Existing infrastructure information

Using the CMDB as a source of truth helps ensure that infrastructure requests are associated with valid applications and ownership information before provisioning begins.

After successful provisioning, the resulting infrastructure information can be registered back into the CMDB.

---

## 5. Hostname Generation

Hostname generation is automated based on standardized enterprise naming conventions.

The naming logic can incorporate attributes such as:

* Platform
* Application code
* Component
* Instance number
* Landscape
* Network zone

For example:

```text
PHY + TCS + APP + 01 + DEV

phytcsapp01-dev
```

Automating hostname generation reduces naming inconsistencies and removes another manual step from the provisioning process.

The naming model can also be extended to support additional datacenters, application components, environments, and infrastructure types.

---

## 6. Network and DNS Automation

Network provisioning is integrated with enterprise IP address management and DNS services.

Infoblox APIs can be used to automate:

* IP address allocation
* DNS record creation
* Host record management
* Additional interface/IP requirements

Standard infrastructure requests can provision the required network records automatically.

Specialized workloads, such as database or clustered infrastructure, can request additional network identities where required.

This approach reduces manual DNS/IPAM activities and helps ensure that network information remains synchronized with the provisioning workflow.

---

## 7. Storage Provisioning

Storage provisioning is integrated with NetApp ONTAP APIs.

The workflow can automate the creation and configuration of storage resources including:

* Volumes
* LUNs
* Initiator groups
* Initiator registration
* LUN mapping

A standardized boot-storage model can be applied while allowing workload-specific storage requirements to be incorporated into the provisioning request.

The storage workflow is intentionally separated from compute provisioning so that storage services can be managed and evolved independently.

---

## 8. Cisco UCS Compute Provisioning

Cisco UCS/UCSM automation provides the compute provisioning layer.

The workflow can:

1. Discover available compute resources.
2. Identify suitable free blades.
3. Create or configure a service profile.
4. Configure network and storage connectivity.
5. Configure iSCSI initiator information where required.
6. Associate the service profile with the selected blade.
7. Power on the system.

This allows physical compute allocation to become part of the same automated provisioning workflow rather than requiring manual hardware configuration.

---

## 9. PXE and Operating System Provisioning

After compute, network, and storage prerequisites are established, the server enters the operating system provisioning stage.

PXE/DHCP is used to initiate network-based installation.

Kickstart or equivalent automated installation mechanisms provide standardized operating system deployment.

Supported Linux platforms in the reference implementation include:

* RHEL 8 / 9
* AlmaLinux 8 / 9
* Ubuntu 20.04 / 22.04

The objective is to produce a predictable baseline operating system before handing the server to configuration management.

---

## 10. Ansible Post-Installation Automation

Ansible is used to transform the newly installed operating system into an enterprise-ready server.

Post-installation automation can include:

* Package installation
* User and access configuration
* Active Directory integration
* Security and threat-protection agents
* Vulnerability scanning agents
* Monitoring agents
* Patch-management tooling
* Standard system configuration
* Security hardening
* Application prerequisites

Using configuration management after OS deployment provides a consistent and repeatable configuration model across servers.

---

## 11. Enterprise Configuration Lifecycle

The architecture separates operating system installation from post-installation configuration.

This creates two distinct lifecycle stages:

```text
OS Provisioning
      |
      v
Base Linux Installation
      |
      v
Configuration Management
      |
      v
Enterprise-Ready Server
```

This separation makes the platform easier to maintain and allows OS provisioning and configuration automation to evolve independently.

---

## 12. Multi-Datacenter Architecture

The provisioning platform is designed to support geographically distributed infrastructure environments.

The architecture can accommodate multiple primary and regional datacenters while maintaining common provisioning standards.

Datacenter-specific characteristics such as:

* Compute domains
* Network configuration
* IP address pools
* Storage resources
* PXE infrastructure

can be abstracted behind the orchestration layer.

This allows the same provisioning model to be applied across different infrastructure locations without exposing infrastructure complexity to application teams.

---

## 13. Scalability

The architecture is designed for enterprise-scale provisioning.

The reference environment supported:

* Multiple datacenters
* Multiple Cisco UCS domains
* Hundreds of applications
* Large numbers of physical servers
* Repeated provisioning workflows throughout the year

The use of API-driven automation and modular configuration management enables infrastructure provisioning to scale without requiring a proportional increase in manual operational effort.

---

## 14. Reliability and Failure Handling

Infrastructure provisioning involves multiple external systems, so the architecture considers failure boundaries between individual services.

Examples include:

* CMDB validation failure
* IP/DNS allocation failure
* Storage provisioning failure
* Compute provisioning failure
* PXE installation failure
* Ansible configuration failure

The orchestration layer should maintain provisioning state and provide clear status information so that failures can be identified and investigated without repeating successfully completed stages unnecessarily.

Idempotent automation is preferred wherever practical to reduce the risk of inconsistent infrastructure state during retries.

---

## 15. Security Considerations

Security is incorporated into the provisioning lifecycle rather than treated as a separate final step.

The architecture considers:

* API authentication and authorization
* Secure handling of infrastructure credentials
* Role-based access to provisioning functions
* Network-zone awareness
* OS hardening
* Security agents
* Vulnerability scanning
* Access control
* Auditability of provisioning actions

Production credentials and proprietary infrastructure details are intentionally excluded from this portfolio implementation.

---

## 16. CI/CD and Automation Quality

Jenkins can be used to validate automation components before they are introduced into the provisioning workflow.

Typical validation activities include:

```text
Code Change
    |
    v
Build / Validation
    |
    v
Automated Testing
    |
    v
Automation Validation
    |
    v
Deployment
```

This approach applies DevOps practices to infrastructure automation and helps reduce the risk associated with changes to provisioning workflows.

---

## 17. Architectural Design Principles

The platform follows several key principles:

* **Automation first** — minimize repetitive manual infrastructure activities.
* **API-driven integration** — integrate enterprise platforms through supported APIs.
* **Separation of concerns** — keep compute, network, storage, OS, and configuration workflows modular.
* **Standardization** — use common provisioning and configuration patterns.
* **Idempotency** — support safe retries wherever practical.
* **Source of truth** — leverage enterprise CMDB and IPAM systems.
* **Security by design** — incorporate security into the provisioning lifecycle.
* **Scalability** — design for multiple datacenters and large infrastructure environments.
* **Observability** — provide provisioning status and actionable failure information.

---

## 18. Architectural Outcome

The resulting platform transforms bare-metal provisioning from a sequence of manually coordinated infrastructure tasks into an integrated enterprise workflow.

```text
Application Request
        |
        v
Validation
        |
        v
Compute + Network + Storage
        |
        v
OS Deployment
        |
        v
Enterprise Configuration
        |
        v
CMDB Registration
        |
        v
Application Ready
```

The architecture demonstrates how Linux infrastructure, physical compute, networking, storage, configuration management, ITSM, and automation can be combined into a scalable self-service infrastructure platform.

---

## Portfolio Note

This repository is a **sanitized reference architecture** based on enterprise infrastructure engineering and architecture patterns. Production credentials, internal network information, proprietary source code, customer-specific application information, and confidential infrastructure details are intentionally excluded.
