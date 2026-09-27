# Enterprise Linux Bare-Metal Provisioning & Infrastructure Automation

An enterprise infrastructure automation platform designed to automate the end-to-end provisioning lifecycle of physical Linux servers.

The platform integrates **ServiceNow CMDB, FastAPI, Python, Infoblox, Cisco UCS, NetApp ONTAP, PXE/Kickstart, and Ansible** to provide a self-service workflow from application request through bare-metal provisioning, operating system deployment, enterprise configuration, and CMDB registration.

## Linux Infrastructure Engineering

Linux is a core foundation of this platform, supporting the complete lifecycle from bare-metal provisioning through production-ready system configuration. The solution is designed to provision enterprise Linux platforms such as **RHEL 8/9, AlmaLinux 8/9, and Ubuntu 20.04/22.04**, using automated PXE/DHCP and Kickstart-based installation workflows. Server requests are validated against enterprise application and ownership information, with automated hostname generation, network configuration, storage provisioning, and hardware assignment before the operating system is deployed.

Following OS installation, **Ansible-driven configuration automation** transforms the newly provisioned server into a standardized enterprise platform. This includes package installation, user and access configuration, Active Directory integration, security and threat-protection agents, vulnerability scanning, monitoring, patch-management tooling, system hardening, and application-specific prerequisites. The automation is designed to provide consistent builds across geographically distributed datacenters while reducing manual intervention and configuration drift.

The architecture also demonstrates how Linux infrastructure can be integrated with enterprise platforms and APIs, including **ServiceNow CMDB, Infoblox, Cisco UCS, NetApp ONTAP, FastAPI, Python, Ansible, and Jenkins**. The result is an end-to-end infrastructure provisioning workflow where compute, network, storage, operating system, and post-installation configuration are orchestrated as a single automated lifecycle rather than as independent manual tasks.

This project represents a **sanitized reference architecture inspired by enterprise infrastructure automation patterns**, demonstrating the architectural thinking, automation strategy, integration design, and operational considerations required to build and scale Linux infrastructure in large enterprise environments.


---

## 🏗️ Architecture Overview

The platform provides an automated infrastructure provisioning workflow across compute, network, storage, operating system, configuration management, and ITSM systems.

```text
                           APPLICATION TEAM
                                  │
                                  ▼
                         ┌─────────────────┐
                         │    Web Portal   │
                         │  React / Angular│
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │     FastAPI     │
                         │ API / Workflow  │
                         │   Orchestrator  │
                         └────────┬────────┘
                                  │
             ┌────────────────────┼────────────────────┐
             │                    │                    │
             ▼                    ▼                    ▼
       ┌───────────┐        ┌───────────┐       ┌───────────┐
       │ ServiceNow│        │ Infoblox  │       │ Cisco UCS │
       │   CMDB    │        │ DNS / IPAM│       │   UCSM    │
       └───────────┘        └───────────┘       └───────────┘
                                  │                    │
                                  │                    │
                                  └─────────┬──────────┘
                                            │
                                            ▼
                                    ┌──────────────┐
                                    │ NetApp ONTAP │
                                    │ Storage / LUN│
                                    └──────┬───────┘
                                           │
                                           ▼
                                    ┌──────────────┐
                                    │ PXE / DHCP / │
                                    │  Kickstart   │
                                    └──────┬───────┘
                                           │
                                           ▼
                                    ┌──────────────┐
                                    │ Linux Server │
                                    │ RHEL / Alma  │
                                    │    Ubuntu    │
                                    └──────┬───────┘
                                           │
                                           ▼
                                    ┌──────────────┐
                                    │   Ansible    │
                                    │ Post Install │
                                    │ Configuration│
                                    └──────┬───────┘
```
