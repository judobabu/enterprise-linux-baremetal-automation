## End-to-End Provisioning Example

The following example illustrates how a single application request moves through the automated infrastructure provisioning platform.

### 1. Application Request

An application team requests a development application server with:

```text
Application: TCS Application
Environment: DEV
Component: APP
Network: Internal
Operating System: RHEL 9
Storage: Standard OS storage
Compute: Bare Metal
```

### 2. CMDB Validation

The platform queries ServiceNow CMDB to validate:

* Application existence
* Application ownership
* Environment information
* Component information
* Infrastructure relationships

Only validated requests proceed to infrastructure provisioning.

### 3. Hostname Generation

The platform generates a standardized hostname based on the request attributes.

Example:

```text
phytcsapp01-dev
```

The generated hostname becomes the primary infrastructure identity used throughout the provisioning workflow.

### 4. Network Provisioning

The platform determines the appropriate network configuration based on the datacenter, environment, and network zone.

Infoblox APIs are used for automated IP and DNS management.

Depending on the server profile, records such as the following may be required:

```text
iscsi1
iscsi2
intg
```

Additional addresses can be allocated when required by specialized infrastructure such as database or RAC configurations.

### 5. Storage Provisioning

The storage workflow communicates with NetApp ONTAP to prepare the required storage.

A standard operating-system profile may use:

```text
Boot LUN:        300 GB
Backend Volume:  500 GB
Auto-grow:       Enabled
```

The workflow can create the required storage objects, configure initiators, and map the LUN to the target compute system.

### 6. Compute Provisioning

Cisco UCS infrastructure is evaluated for available capacity.

The automation can:

1. Select the appropriate UCS domain.
2. Identify an available blade.
3. Create or configure the service profile.
4. Configure required iSCSI/network parameters.
5. Attach the blade.
6. Power on the system.

### 7. OS Provisioning

The server boots through the enterprise PXE environment.

The provisioning workflow uses the server's network identity and hardware information to initiate the appropriate operating-system deployment.

Supported enterprise operating systems include:

* RHEL 8/9
* AlmaLinux 8/9
* Ubuntu 20.04/22.04

### 8. Ansible Post-Installation

After the operating system is installed, Ansible applies the enterprise configuration baseline.

Typical activities include:

* Required package installation
* User and access configuration
* Active Directory integration
* Security and threat-protection agents
* Vulnerability scanning
* Patch-management agents
* Monitoring agents
* Standard system configuration
* Application prerequisites

### 9. CMDB Registration

After successful provisioning, the infrastructure information is registered or updated in ServiceNow CMDB.

The resulting record provides a relationship between:

```text
Application
    |
    +---- Environment
    |
    +---- Component
    |
    +---- Host
    |
    +---- Infrastructure
```

### 10. Application Owner Notification

Once the provisioning workflow completes successfully, the application owner can be notified that the infrastructure is ready for use.

## Complete Flow

```text
Application Request
        |
        v
ServiceNow CMDB Validation
        |
        v
Hostname Generation
        |
        v
Infoblox Network / DNS
        |
        v
NetApp ONTAP Storage
        |
        v
Cisco UCS Compute
        |
        v
PXE / OS Installation
        |
        v
Ansible Configuration
        |
        v
CMDB Registration
        |
        v
Application Owner
```

## Architecture Perspective

The key architectural principle is that the provisioning platform coordinates multiple infrastructure domains through a single automated workflow.

Instead of treating compute, network, storage, operating system, and configuration management as independent manual activities, the platform provides an orchestration layer that connects them into a repeatable lifecycle.

This model can be extended to additional infrastructure platforms while maintaining the same application-driven provisioning experience.
