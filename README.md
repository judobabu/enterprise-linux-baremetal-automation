# Enterprise Linux Bare-Metal Provisioning & Infrastructure Automation

An enterprise infrastructure automation platform designed to automate the end-to-end provisioning lifecycle of physical Linux servers.

The platform integrates **ServiceNow CMDB, FastAPI, Python, Infoblox, Cisco UCS, NetApp ONTAP, PXE/Kickstart, and Ansible** to provide a self-service workflow from application request through bare-metal provisioning, operating system deployment, enterprise configuration, and CMDB registration.

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
