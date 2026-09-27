# Provisioning Workflow

## Overview

The provisioning workflow is designed to automate the complete lifecycle of an enterprise Linux bare-metal server, starting with an application team's infrastructure request and ending with a fully configured server registered in the enterprise CMDB.

The workflow coordinates application metadata, physical compute, network services, storage, operating system deployment, and post-installation configuration through a combination of APIs, Python orchestration, PXE/Kickstart, and Ansible automation.

The objective is to provide a standardized and repeatable provisioning experience while minimizing manual infrastructure operations.

---

## End-to-End Workflow

```text
Application Team
       |
       v
Server Request
       |
       v
ServiceNow CMDB Validation
       |
       v
Request Validation
       |
       v
Hostname Generation
       |
       +----------------------+
       |                      |
       v                      v
Network Provisioning      Storage Provisioning
Infoblox                  NetApp ONTAP
       |                      |
       +----------+-----------+
                  |
                  v
          Cisco UCS Provisioning
                  |
                  v
          PXE / DHCP / Kickstart
                  |
                  v
            Linux Installation
                  |
                  v
          Ansible Configuration
                  |
                  v
       Enterprise Security /
       Monitoring / Patching
                  |
                  v
           CMDB Registration
                  |
                  v
       Application Owner Notification
```

---

## 1. Application Infrastructure Request

The workflow begins when an application team requests a new bare-metal server through the self-service web portal.

The request captures the infrastructure characteristics required to build the server.

Typical request attributes include:

* Application
* Datacenter
* Landscape
* Network zone
* Component type
* Operating system
* Hardware requirements
* Storage requirements

Supported landscape examples include:

```text
DEV
TST
STG
PRD
```

Network zones can distinguish between internal and externally accessible or DMZ-oriented infrastructure requirements.

Component codes can identify the intended workload, such as:

```text
APP
DB
```

The portal provides a controlled interface so that application teams do not need to understand the underlying compute,
