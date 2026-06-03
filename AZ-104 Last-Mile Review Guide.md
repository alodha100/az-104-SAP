# AZ-104 Last-Mile Review Guide

## Microsoft Azure Administrator Associate

Purpose: Use this as a last-mile revision sheet. It does not replace hands-on practice. AZ-104 rewards people who understand how Azure services fit together and can choose the correct configuration under constraints like security, cost, least privilege, availability, and operational effort.

## Disclaimer

This guide and practice question set are designed as supplemental study material for the AZ-104: Microsoft Azure Administrator exam.

They are intended to help reinforce concepts, improve exam readiness, and highlight common decision-making patterns seen in Azure administration scenarios.

These materials do not replace:

> hands-on Azure experience  
> Microsoft Learn content  
> official documentation  
> labs and practical exercises  

Azure services, portal experiences, CLI commands, pricing models, and exam objectives may change over time. Always validate critical information against the latest Microsoft documentation and official AZ-104 skills outline.

The practice questions included are inspired by real-world Azure administration patterns and Microsoft-style exam reasoning, but they are not official Microsoft exam questions and should not be treated as dumps or guaranteed exam content.

Focus on understanding:

> why a solution is correct  
> why the alternatives are wrong  
> tradeoffs between security, cost, availability, operational effort, and governance

Passing AZ-104 is less about memorization and more about making the correct administrative decision under constraints.

## Current exam domains (May 2026)

1. Manage Azure identities and governance
2. Implement and manage storage
3. Deploy and manage Azure compute resources
4. Implement and manage virtual networking
5. Monitor and maintain Azure resources

---

## 1. Identity and Governance

### Core concepts students must know

#### Microsoft Entra ID

- Cloud identity provider for Azure and Microsoft 365.
- Formerly Azure Active Directory.
- Manages users, groups, devices, applications, and authentication.

#### Azure RBAC

- Controls access to Azure resources.
- Uses role assignments.
- Scope can be:
  - Management group
  - Subscription
  - Resource group
  - Resource

RBAC assignment = Security principal + Role definition + Scope

Security principal:

- User
- Group
- Service principal
- Managed identity

Common RBAC roles:

- Owner: full access, including assigning roles.
- Contributor: full resource management, but cannot assign roles.
- Reader: view only.
- User Access Administrator: manage access assignments.
- Storage Blob Data Reader/Contributor: data-plane access to blobs.
- Virtual Machine Contributor: manage VMs, but not network/security dependencies in all cases.

Exam tip: If the question says “least privilege,” avoid Owner unless absolutely required.

#### Azure roles vs Microsoft Entra roles

- Azure RBAC roles manage Azure resources.
- Entra roles manage identity tasks.
- Example:
  - Contributor: Azure RBAC role.
  - Global Administrator: Entra role.
  - User Administrator: Entra role.

#### Management groups

- Organize subscriptions hierarchically.
- Useful for applying governance across many subscriptions.
- Azure Policy and RBAC can be assigned at management group scope.

#### Subscriptions

- Billing and management boundary.
- Each subscription belongs to one Entra tenant.
- Resources are deployed into subscriptions.

#### Resource groups

- Logical containers for Azure resources.
- A resource can belong to only one resource group.
- Resource groups have a region, but resources inside can be in different regions.
- Deleting a resource group deletes the resources inside it.

#### Azure Policy

- Enforces rules and compliance.
- Can audit, deny, modify, or deploy configurations.
- Used for governance, not permissions.
- Examples:
  - Require tags.
  - Restrict allowed regions.
  - Restrict VM SKUs.
  - Enforce diagnostic settings.
  - Deploy Microsoft Defender settings.

RBAC vs Azure Policy:

- RBAC controls who can do something.
- Policy controls what can be done.

#### Resource locks

- Prevent accidental deletion or modification.
- Lock types:
  - CanNotDelete: authorized users can read/modify but not delete.
  - ReadOnly: authorized users can read only.
- Locks apply regardless of RBAC permissions.
- Locks can be applied to subscription, resource group, or resource.

#### Tags

- Metadata for resources.
- Used for cost management, ownership, automation, and governance.
- Tags are not automatically inherited unless enforced by policy.

#### Managed identities

- Provide Azure resources with an identity in Entra ID.
- Avoid storing credentials in code.
- Types:
  - System-assigned: tied to one resource; deleted with the resource.
  - User-assigned: standalone identity; can be shared by multiple resources.

### Common Pitfalls

- Contributor cannot assign RBAC roles.
- Azure Policy does not grant access.
- Locks do not replace RBAC.
- Entra roles and Azure RBAC roles are different.
- Tags are useful for billing and organization but do not secure resources.

---

## 2. Storage

### Azure Storage account services

- Blob storage: object storage.
- File storage: SMB/NFS file shares.
- Queue storage: messaging.
- Table storage: NoSQL key-value.
- Disk storage: managed disks for VMs.

### Storage account performance

- Standard: HDD-backed, general-purpose.
- Premium: SSD-backed, lower latency.

### Storage account redundancy

- LRS: Three synchronous copies within a single Azure datacenter in one region. Cheapest.
- ZRS: Zone-redundant storage. Copies across availability zones in one region.
- GRS: Geo-redundant storage. LRS in primary region plus LRS in secondary region.
- RA-GRS: Read-access geo-redundant storage. Like GRS, but secondary region is readable.
- GZRS: Geo-zone-redundant storage. ZRS in primary plus LRS in secondary.
- RA-GZRS: GZRS with readable secondary.

Exam tip: If the question requires resilience across availability zones, choose ZRS or GZRS. If it requires read access during regional outage, choose RA-GRS or RA-GZRS.

### Blob access tiers

- Hot: frequent access.
- Cool: infrequent access, lower storage cost, higher access cost.
- Cold: less frequent than cool.
- Archive: cheapest storage, offline access, rehydration required.

### Blob types

- Block blobs: general object storage, most common.
- Append blobs: logging scenarios.
- Page blobs: random read/write, used by VHDs.

### Blob lifecycle management

- Automatically move blobs between tiers.
- Can delete old blobs.
- Based on age, last modified, or last accessed.

### Shared access signatures

- SAS provides delegated access to storage resources.
- Types:
  - User delegation SAS: secured with Entra credentials; recommended for Blob.
  - Service SAS: secured with storage account key.
  - Account SAS: broader account-level access.
- SAS can define:
  - Allowed services
  - Permissions
  - Start and expiry time
  - IP range
  - Protocol

### Stored access policy

- Can be used to manage service SAS for containers, file shares, queues, or tables.
- Allows revocation/change without regenerating every SAS.

### Storage account keys

- Provide full access to storage account.
- Two keys exist for rotation.
- Avoid giving account keys to users/apps when SAS or Entra auth is possible.

### Azure Files

- Managed file shares.
- Supports SMB and NFS.
- Can be mounted by Windows, Linux, and macOS.
- Can integrate with AD DS or Entra Kerberos in some scenarios.

### Azure File Sync

- Syncs Azure file shares with Windows Server.
- Enables cloud tiering.
- Useful for branch office file servers.

### Storage security

- Disable public access if not needed.
- Use private endpoints for private access.
- Use firewall rules and virtual network rules.
- Prefer Entra ID auth where supported.
- Use Defender for Storage for threat detection.
- Use encryption at rest; enabled by default.
- Azure-managed keys are used by default.
- Customer-managed keys use Azure Key Vault.

### Data transfer

- AzCopy: command-line tool for copying data to/from Azure Storage. Supports blobs and files.
- Import/Export service: used for offline transfer of large datasets using disks.

### Common Pitfalls

- Archive tier requires rehydration before access.
- Storage account keys are powerful and should be protected.
- Blob public access can be disabled at account level.
- Private endpoint gives private IP access to the storage account.
- Service endpoint keeps traffic on Microsoft backbone but does not assign private IP to the service.
- ZRS protects against zone failure, not regional failure.
- GRS protects against regional failure, but secondary is not readable unless RA-GRS.

---

## 3. Compute

### Virtual machines

- Infrastructure as a Service.
- You manage OS, patches, installed software, and configuration.

### VM sizes

- Define CPU, memory, disk, and network capacity.
- Families:
  - B-series: burstable.
  - D-series: general purpose.
  - E-series: memory optimized.
  - F-series: compute optimized.
  - L-series: storage optimized.
  - N-series: GPU.

### Availability options

#### Availability set

- Protects against rack/hardware failures within a datacenter.
- Uses fault domains and update domains.

#### Availability zone

- Physically separate datacenters within a region.
- Better protection than availability sets.

#### Virtual Machine Scale Set

- Deploy and manage multiple identical VMs.
- Supports autoscale.
- Good for horizontal scaling.

### Fault domain

- Group of hardware sharing power/network.
- Protects against hardware failure.

### Update domain

- Group of VMs rebooted together during maintenance.
- Protects during planned maintenance.

### Managed disks

- Azure-managed block storage for VMs.
- Disk types:
  - Standard HDD
  - Standard SSD
  - Premium SSD
  - Premium SSD v2
  - Ultra Disk
- OS disk: contains operating system.
- Data disk: additional storage.
- Temporary disk: non-persistent local storage.

### Snapshots

- Point-in-time copy of a managed disk.
- Useful for backup or creating new disks.

### Images

- Used to create VMs from a generalized or specialized configuration.
- Azure Compute Gallery helps manage and replicate images.

### VM extensions

- Post-deployment configuration.
- Examples:
  - Custom Script Extension
  - Desired State Configuration
  - Azure Monitor Agent
  - Dependency Agent

### Custom Script Extension

- Runs scripts on a VM after deployment.
- Useful for installing software or configuration.

### Run Command

- Execute commands inside a VM from Azure without direct RDP/SSH.

### Azure Bastion

- Secure RDP/SSH to VMs through the Azure portal or native client.
- Does not require public IP on the VM.
- Deployed into a subnet named `AzureBastionSubnet`.

### App Service

- Platform as a Service for web apps and APIs.
- Supports deployment slots.
- Scaling:
  - Scale up: change pricing tier.
  - Scale out: increase instance count.
- App Service Plan determines compute resources.

### Containers

- Azure Container Instances: simple serverless container execution. Good for quick tasks or simple containers.
- Azure Kubernetes Service: managed Kubernetes. More complex; used for orchestrated container workloads.

### ARM templates and Bicep

- Infrastructure as Code.
- ARM: JSON-based.
- Bicep: cleaner declarative syntax that compiles to ARM.
- Used for repeatable deployments.

### Common Pitfalls

- Availability sets and availability zones are not the same.
- A VM cannot be added to an availability set after creation.
- Bastion requires `AzureBastionSubnet`.
- VMSS is for scale-out identical VM instances.
- App Service deployment slots are useful for staging and swap.
- Custom Script Extension runs inside the VM.
- Run Command is useful when you cannot directly connect to a VM.

---

## 4. Virtual Networking

### Virtual network

- Private network boundary in Azure.
- Contains subnets.
- Address space uses CIDR notation.

### Subnet

- Segment of a VNet.
- Resources like VMs, private endpoints, and Bastion are placed into subnets.
- Some services require dedicated subnet names:
  - `AzureBastionSubnet`
  - `GatewaySubnet`
  - `AzureFirewallSubnet`

### Network Security Group

- Filters inbound and outbound traffic.
- Can be associated with subnet or network interface.
- Rules include:
  - Priority
  - Source
  - Source port
  - Destination
  - Destination port
  - Protocol
  - Action
- Lower priority number wins.
- Default rules exist.
- NSGs are stateful. Return traffic for allowed inbound/outbound flows is automatically allowed.

### Application Security Group

- Logical grouping of VM NICs.
- Used inside NSG rules to simplify management.

NSG vs ASG:

- NSG contains the rules.
- ASG groups the targets/sources.

### Route tables and UDR

- User-defined routes control traffic flow.
- Can route traffic to:
  - Virtual appliance
  - Virtual network gateway
  - Internet
  - None
- Often used with firewalls or NVAs.

### VNet peering

- Connects VNets privately over Microsoft backbone.
- Non-transitive by default.
- If VNet A peers with B and B peers with C, A does not automatically reach C.
- Can enable gateway transit in hub-spoke designs.

### VPN Gateway

- Encrypted connectivity between Azure and on-premises.
- Types:
  - Site-to-site VPN
  - Point-to-site VPN
  - VNet-to-VNet VPN
- Requires `GatewaySubnet`.

### ExpressRoute

- Private dedicated connection to Microsoft cloud.
- Does not use the public Internet.
- More predictable performance than VPN.

### Private Endpoint

- Provides private IP access to a specific Azure service.
- Uses Azure Private Link.
- Service appears inside your VNet.
- Often requires private DNS configuration.
- Public network access to the service can be disabled when using Private Endpoint.

### Service Endpoint

- Extends VNet identity to Azure services.
- Traffic stays on Microsoft backbone.
- Service still uses public endpoint.
- Easier than private endpoint but less isolated.

### Private Endpoint vs Service Endpoint

Private Endpoint:

- Private IP in your VNet.
- More secure/private.
- Requires private DNS.

Service Endpoint:

- No private IP for service.
- Simpler.
- Still targets public service endpoint.

### Load Balancer

- Layer 4 TCP/UDP load balancing.
- Internal or public.
- Uses health probes and load balancing rules.

### Application Gateway

- Layer 7 HTTP/HTTPS load balancing.
- Supports:
  - SSL termination
  - URL-based routing
  - Host-based routing
  - Web Application Firewall

### Azure Firewall

- Managed stateful firewall.
- Supports application and network rules.
- Deployed into `AzureFirewallSubnet`.
- Often used in hub-spoke architecture.

### Traffic Manager

- DNS-based global traffic distribution.
- Does not proxy traffic.
- Routing methods include priority, weighted, performance, geographic.

### Azure Front Door

- Global HTTP/HTTPS application delivery.
- Layer 7.
- Supports acceleration, WAF, routing, TLS termination.

### DNS

- Azure DNS hosts public DNS zones.
- Private DNS zones are used for internal name resolution.
- Private endpoints commonly need private DNS zones.

### Common Pitfalls

- NSG rules use lower priority numbers first.
- VNet peering is not transitive.
- Private endpoints usually require private DNS.
- Service endpoints do not give private IPs to services.
- Load Balancer is Layer 4.
- Application Gateway is Layer 7 regional.
- Front Door is Layer 7 global.
- Traffic Manager is DNS-based.
- VPN Gateway requires `GatewaySubnet`.
- Bastion requires `AzureBastionSubnet`.
- Azure Firewall requires `AzureFirewallSubnet`.

---

## 5. Monitoring and Maintenance

### Azure Monitor

- Central monitoring platform.
- Collects metrics and logs.
- Used for alerts, dashboards, workbooks, and analysis.

### Metrics

- Numeric time-series data.
- Lightweight.
- Near real-time.
- Examples:
  - CPU percentage
  - Disk reads
  - Network in/out

### Logs

- Queryable data stored in Log Analytics workspace.
- Uses Kusto Query Language, KQL.
- Useful for deeper analysis.

### Log Analytics workspace

- Stores logs.
- Query using KQL.
- Used by Azure Monitor, Microsoft Sentinel, Defender, and other services.

### Diagnostic settings

Send resource logs and metrics to:

- Log Analytics workspace
- Storage account
- Event Hub
- Partner solution

### Activity Log

- Subscription-level control-plane events.
- Answers: who did what, when, and where.
- Examples:
  - Resource created
  - Resource deleted
  - Role assignment changed

### Resource logs

- Logs generated by Azure resources.
- Must often be enabled via diagnostic settings.

### Azure Monitor Agent

- Modern agent for collecting data from VMs.
- Uses Data Collection Rules.

### Data Collection Rules

- Define what data to collect and where to send it.
- Used with Azure Monitor Agent.

### Alerts

- Notify or trigger action when conditions are met.
- Signal types:
  - Metric
  - Log query
  - Activity Log
  - Resource health
  - Service health

### Action groups

Define alert responses. Can send:

- Email
- SMS
- Push notification
- Voice
- Webhook
- Automation runbook
- Azure Function
- Logic App

### Service Health

- Azure service incidents, planned maintenance, health advisories.
- Best for Azure platform issues affecting regions/services.

### Resource Health

- Health of individual Azure resources.
- Example: VM unavailable due to platform event.

### Network Watcher

Network troubleshooting and diagnostics. Tools include:

- IP flow verify
- Next hop
- Connection troubleshoot
- Packet capture
- NSG flow logs
- Topology

### Backup

- Azure Backup protects VMs, files, SQL, and other workloads.
- Recovery Services vault commonly used for VM backup.
- Backup policy defines frequency and retention.
- Soft delete helps protect against accidental deletion.

### Azure Site Recovery

- Disaster recovery and replication.
- Used to fail over workloads to another region/site.
- Not the same as Azure Backup.

### Update management

- Azure Update Manager helps manage OS updates.
- Can schedule and assess updates.

### Advisor

Recommendations for:

- Cost
- Security
- Reliability
- Operational excellence
- Performance

### Cost Management

- Budgets
- Alerts
- Cost analysis
- Reservations
- Savings plans

### Common Pitfalls

- Activity Log is for subscription-level management events.
- Log Analytics is used for querying logs with KQL.
- Metrics are not the same as logs.
- Diagnostic settings are needed to export many logs.
- Action groups define what happens when an alert fires.
- Service Health is about Azure service issues.
- Resource Health is about a specific resource.
- Azure Backup is for backup/restore.
- Azure Site Recovery is for disaster recovery/failover.

---

## High-Value Comparison Tables

### RBAC vs Policy vs Locks

| Feature | Purpose | Example |
|---|---|---|
| RBAC | Controls who can access resources | Allow Alice to manage VMs |
| Azure Policy | Controls what configurations are allowed | Only allow resources in West Europe |
| Locks | Prevent deletion or modification | Prevent accidental deletion of a production resource group |

### Service Endpoint vs Private Endpoint

| Feature | Service Endpoint | Private Endpoint |
|---|---|---|
| Access model | Extends VNet identity to Azure service | Adds private IP for service in your VNet |
| Endpoint | Service keeps public endpoint | Uses Private Link |
| Privacy | Good, but less private | More secure/private |
| Complexity | Simpler setup | Requires DNS planning |

### Load Balancer vs Application Gateway vs Traffic Manager vs Front Door

| Service | Layer | Scope | Best for |
|---|---:|---|---|
| Azure Load Balancer | Layer 4 | Regional | TCP/UDP VM traffic |
| Application Gateway | Layer 7 | Regional | HTTP/HTTPS, URL/host routing, WAF |
| Traffic Manager | DNS | Global | DNS-based endpoint selection |
| Azure Front Door | Layer 7 | Global | Global HTTP/HTTPS acceleration, WAF, TLS, routing |

### Availability Set vs Availability Zone vs VMSS

| Option | Purpose | Notes |
|---|---|---|
| Availability Set | Protects against hardware/update failures in a datacenter | Uses fault/update domains |
| Availability Zone | Protects against datacenter failure within a region | Stronger availability design |
| VM Scale Set | Manages multiple VM instances | Supports autoscale |

### Azure Backup vs Azure Site Recovery

| Service | Purpose | Use case |
|---|---|---|
| Azure Backup | Backup and restore | Recovery from deletion/corruption |
| Azure Site Recovery | Disaster recovery | Replication and failover |

### Activity Log vs Resource Logs vs Metrics

| Type | Scope | Example |
|---|---|---|
| Activity Log | Subscription/control-plane events | VM deleted |
| Resource Logs | Resource-level operations | NSG flow logs |
| Metrics | Numeric time-series data | CPU percentage |

---

## Azure CLI Commands Students Should Recognize

### Login and account

```bash
az login
az account show
az account list
az account set --subscription <subscription-id>
```

### Resource groups

```bash
az group create --name rg-demo --location westeurope
az group list
az group delete --name rg-demo
```

### VMs

```bash
az vm create \
  --resource-group rg-demo \
  --name vm1 \
  --image Ubuntu2204 \
  --admin-username azureuser \
  --generate-ssh-keys

az vm start --resource-group rg-demo --name vm1
az vm stop --resource-group rg-demo --name vm1
az vm deallocate --resource-group rg-demo --name vm1
az vm delete --resource-group rg-demo --name vm1
```

### Networking

```bash
az network vnet create \
  --resource-group rg-demo \
  --name vnet1 \
  --address-prefix 10.0.0.0/16 \
  --subnet-name subnet1 \
  --subnet-prefix 10.0.1.0/24

az network nsg create --resource-group rg-demo --name nsg1

az network nsg rule create \
  --resource-group rg-demo \
  --nsg-name nsg1 \
  --name AllowHTTP \
  --priority 100 \
  --destination-port-ranges 80 \
  --access Allow \
  --protocol Tcp \
  --direction Inbound
```

### Storage

```bash
az storage account create \
  --name <unique-name> \
  --resource-group rg-demo \
  --location westeurope \
  --sku Standard_LRS

az storage container create \
  --name data \
  --account-name <storage-account-name>
```

### RBAC

```bash
az role assignment create \
  --assignee <user-or-principal-id> \
  --role "Reader" \
  --scope <scope>
```

### Policy

```bash
az policy assignment create \
  --name allowed-locations \
  --policy <policy-definition-id> \
  --scope <scope>
```

### Monitor

```bash
az monitor metrics list \
  --resource <resource-id> \
  --metric "Percentage CPU"
```

### Locks

```bash
az lock create \
  --name lock-prod \
  --lock-type CanNotDelete \
  --resource-group rg-prod
```

---

## PowerShell Commands Students Should Recognize

### Connect

```powershell
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <subscription-id>
```

### Resource groups

```powershell
New-AzResourceGroup -Name rg-demo -Location westeurope
Get-AzResourceGroup
Remove-AzResourceGroup -Name rg-demo
```

### VMs

```powershell
New-AzVM
Start-AzVM
Stop-AzVM
Remove-AzVM
```

### Storage

```powershell
New-AzStorageAccount
Get-AzStorageAccount
```

### RBAC

```powershell
New-AzRoleAssignment
Get-AzRoleAssignment
Remove-AzRoleAssignment
```

### Locks

```powershell
New-AzResourceLock
Get-AzResourceLock
Remove-AzResourceLock
```

---

## Exam Wording Patterns

| If the question says... | Think... |
|---|---|
| least privilege | Narrowest role and scope; avoid Owner |
| prevent deletion | CanNotDelete lock |
| prevent modification | ReadOnly lock |
| enforce allowed regions | Azure Policy |
| organize subscriptions at scale | Management groups |
| private access to Azure PaaS over private IP | Private Endpoint |
| traffic must not use public Internet for hybrid connectivity | ExpressRoute |
| secure RDP/SSH without public IP | Azure Bastion |
| HTTP path-based routing | Application Gateway |
| global DNS-based routing | Traffic Manager |
| global HTTP/HTTPS acceleration and WAF | Azure Front Door |
| backup and restore VM | Azure Backup / Recovery Services vault |
| fail over VM to another region | Azure Site Recovery |
| who deleted this resource? | Activity Log |
| query VM logs | Log Analytics with KQL |
| send alert email/webhook | Action Group |
| recommendations for cost/security/reliability | Azure Advisor |

---

## Mini KQL Reference

Basic structure:

```kusto
TableName
| where condition
| summarize aggregation by field
| order by field desc
```

Find recent Azure activity:

```kusto
AzureActivity
| sort by TimeGenerated desc
```

Find failed operations:

```kusto
AzureActivity
| where ActivityStatusValue == "Failure"
```

Count operations by resource group:

```kusto
AzureActivity
| summarize Count=count() by ResourceGroup
| order by Count desc
```

Find VM heartbeat:

```kusto
Heartbeat
| summarize LastSeen=max(TimeGenerated) by Computer
```

Find high CPU, example pattern:

```kusto
Perf
| where CounterName == "% Processor Time"
| summarize AvgCPU=avg(CounterValue) by Computer
```

---

## Common Wrong-Answer Traps

1. Choosing Owner when Contributor or a narrower role is enough.
2. Confusing Azure RBAC with Microsoft Entra admin roles.
3. Using Azure Policy to grant permissions.
4. Using RBAC to enforce allowed regions.
5. Forgetting that locks override normal allowed operations.
6. Choosing GRS when the requirement says zone failure.
7. Choosing ZRS when the requirement says regional disaster recovery.
8. Choosing Service Endpoint when the requirement says private IP.
9. Choosing Load Balancer for HTTP path-based routing.
10. Choosing Traffic Manager when traffic must be proxied.
11. Forgetting VNet peering is non-transitive.
12. Forgetting Bastion needs `AzureBastionSubnet`.
13. Forgetting VPN Gateway needs `GatewaySubnet`.
14. Confusing Azure Backup with Azure Site Recovery.
15. Looking in Log Analytics when the question asks who changed a resource; use Activity Log.
16. Forgetting diagnostic settings are needed to send many logs to Log Analytics.
17. Assuming tags are inherited automatically.
18. Assuming Contributor can assign roles.
19. Forgetting Archive blob tier requires rehydration.
20. Thinking a resource group’s region limits where its resources can be deployed.

---

## Last-Minute Student Checklist

### Identity and Governance

- Can I explain RBAC scope inheritance?
- Can I choose between Owner, Contributor, Reader, and User Access Administrator?
- Can I explain Azure Policy vs RBAC?
- Can I explain resource locks?
- Can I use management groups correctly?

### Storage

- Can I choose LRS, ZRS, GRS, RA-GRS, GZRS, or RA-GZRS?
- Can I explain hot, cool, cold, and archive tiers?
- Can I explain SAS and stored access policies?
- Can I secure a storage account with firewall/private endpoint?
- Can I choose between Azure Files, Blob, and File Sync?

### Compute

- Can I choose between availability sets, zones, and scale sets?
- Can I explain managed disks and snapshots?
- Can I use VM extensions and Run Command?
- Can I explain App Service scaling and deployment slots?
- Can I recognize when to use ACI vs AKS?

### Networking

- Can I design a simple VNet/subnet plan?
- Can I configure NSG rules and priorities?
- Can I explain ASG usage?
- Can I explain VNet peering and non-transitivity?
- Can I choose between VPN Gateway and ExpressRoute?
- Can I choose between Service Endpoint and Private Endpoint?
- Can I choose between Load Balancer, Application Gateway, Front Door, and Traffic Manager?

### Monitoring

- Can I explain metrics vs logs?
- Can I use diagnostic settings?
- Can I explain Activity Log vs resource logs?
- Can I explain Log Analytics and KQL?
- Can I configure alerts and action groups?
- Can I choose between Service Health and Resource Health?
- Can I explain Backup vs Site Recovery?

---

## Suggested Final Review Flow

### Day before exam

1. Review RBAC, Policy, locks, and management groups.
2. Review storage redundancy and access tiers.
3. Review Private Endpoint vs Service Endpoint.
4. Review Load Balancer vs Application Gateway vs Front Door vs Traffic Manager.
5. Review Azure Backup vs Azure Site Recovery.
6. Practice 20 to 30 scenario questions.
7. Spend at least 30 minutes reading wrong-answer explanations.

### Exam strategy

- Read the requirement first.
- Look for keywords:
  - least privilege
  - minimum cost
  - highest availability
  - private access
  - no public IP
  - regional failure
  - zone failure
  - administrative effort
- Eliminate answers that solve the wrong problem.
- Be careful with “first step,” “minimum required,” and “most appropriate.”
- Do not over-engineer unless the requirement demands it.

---

# AZ-104 Exam-Grade Questions


# 1

You have an Azure subscription that contains a storage account named `storage1`.

You need to provide an external vendor with temporary read access to blob data in `storage1`.

The solution must:

* minimize administrative effort
* avoid exposing account keys
* allow access to expire automatically

What should you use?

A. Azure RBAC Reader role  
B. Account SAS  
C. User delegation SAS  
D. Storage account access keys

---


# 2

You have:

* VNet1
* Subnet1
* VM1 in Subnet1

You deploy an NSG named NSG1 to Subnet1.

You create the following inbound rules:

| Priority | Port | Action |
| -------- | ---- | ------ |
| 100      | 3389 | Deny   |
| 200      | 3389 | Allow  |

Users report they cannot RDP to VM1.

What is the cause?

A. NSGs process higher numbers first  
B. Deny rules override allow rules regardless of priority  
C. Lower priority numbers are processed first  
D. The NSG must be applied to the NIC

---


# 3

You need to ensure that:

* Azure Storage traffic remains on the Microsoft backbone
* storage accounts are still accessed through public endpoints
* implementation complexity is minimized

What should you configure?

A. Private Endpoint  
B. ExpressRoute  
C. VPN Gateway  
D. Service Endpoint

---


# 4

You have two Azure virtual networks:

* VNetA
* VNetB

You configure peering between them.

You then create:

* VNetC peered with VNetB

Users in VNetA cannot access resources in VNetC.

What is the most likely reason?

A. NSGs block the traffic  
B. VPN Gateway is required  
C. VNet peering is non-transitive  
D. Azure Firewall is required

---


# 5

You need to deploy a solution that provides:

* HTTP path-based routing
* SSL termination
* Web Application Firewall
* regional load balancing

Which service should you deploy?

A. Azure Load Balancer  
B. Traffic Manager  
C. Azure Front Door  
D. Application Gateway

---


# 6

You need to ensure that administrators can:

* create and manage Azure resources
* NOT grant permissions to other users

Which role should you assign?

A. Owner  
B. Contributor  
C. User Access Administrator  
D. Security Administrator

---


# 7

You have a VM named VM1.

You need to troubleshoot a startup failure.

The solution must provide:

* screenshots
* serial console logs

What should you enable?

A. Azure Monitor  
B. Boot Diagnostics  
C. VM Insights  
D. Update Management

---


# 8

You need to ensure that a storage account remains available if an entire Azure region fails.

The solution must also provide read access to replicated data during the outage.

Which redundancy option should you choose?

A. ZRS  
B. GRS  
C. RA-GRS  
D. LRS

---


# 9

You need to prevent administrators from deleting a production resource group.

Administrators must still be able to modify resources inside the group.

What should you configure?

A. ReadOnly lock  
B. CanNotDelete lock  
C. Azure Policy  
D. Contributor role

---


# 10

You need to identify:

* who deleted a virtual machine
* when the deletion occurred

Which log should you query first?

A. NSG Flow Logs  
B. Resource Logs  
C. Activity Log  
D. Metrics

---


# 11

You have an Azure App Service web app.

You need to deploy a new version of the application with minimal downtime.

What should you use?

A. Availability Zones  
B. Deployment slots  
C. VM Scale Sets  
D. Azure Backup

---


# 12

You need to ensure that a subnet routes all Internet-bound traffic through a network virtual appliance.

What should you configure?

A. NSG  
B. ASG  
C. Route table with UDR  
D. Service Endpoint

---


# 13

You need to securely connect to Azure VMs using RDP and SSH.

The solution must:

* avoid public IP addresses on VMs
* minimize exposure to the Internet

Which service should you deploy?

A. NAT Gateway  
B. Azure Bastion  
C. VPN Gateway  
D. ExpressRoute

---


# 14

You need a globally distributed solution that:

* routes users to the closest endpoint
* operates at Layer 7
* supports WAF
* accelerates HTTP/HTTPS traffic

Which service should you use?

A. Traffic Manager  
B. Azure Load Balancer  
C. Azure Front Door  
D. Application Gateway

---


# 15

You need to collect performance counters and Windows Event Logs from Azure VMs.

Which two components should you configure?

A. Azure Monitor Agent  
B. Data Collection Rules  
C. Activity Log  
D. NSG Flow Logs

---


# 16

You need to ensure that users can only deploy resources in `westeurope`.

The solution must automatically block non-compliant deployments.

What should you use?

A. RBAC  
B. Management Groups  
C. Azure Policy  
D. Resource Locks

---


# 17

A company needs private connectivity between on-premises and Azure.

The solution must:

* avoid Internet traversal
* provide predictable latency

What should you recommend?

A. Point-to-site VPN  
B. Site-to-site VPN  
C. ExpressRoute  
D. Private Endpoint

---


# 18

You need to identify whether an NSG is blocking traffic to a VM.

Which Network Watcher feature should you use?

A. Packet Capture  
B. IP Flow Verify  
C. Topology  
D. Connection Monitor

---


# 19

You need to replicate Azure VMs to another region for disaster recovery.

Which service should you use?

A. Azure Backup  
B. Recovery Services Vault  
C. Azure Site Recovery  
D. Azure Monitor

---


# 20

You need to provide temporary script-based configuration after VM deployment.

Which feature should you use?

A. Run Command  
B. Boot Diagnostics  
C. Custom Script Extension  
D. Azure Policy

---


# Answers

## Question 1

C. User delegation SAS

## Why

* Uses Entra authentication
* Avoids storage keys
* Supports expiration
* Recommended for Blob access

## Question 2

C. Lower priority numbers are processed first

NSG rules are evaluated in order of priority number (lowest first). Priority 100 Deny is processed before priority 200 Allow, so RDP is denied.

## Question 3

D. Service Endpoint

## Trap

Private Endpoint would work, but violates:

* “minimize complexity”
* “still accessed through public endpoints”

## Question 4

C. VNet peering is non-transitive

Peering between A↔B and B↔C does not automatically allow A↔C traffic. You would need direct peering between A and C, or use a hub with gateway transit.

## Question 5

D. Application Gateway

## Trap

Front Door is global.
Question explicitly says regional.

## Question 6

B. Contributor

Contributor grants full resource management but cannot assign RBAC roles, meeting the "NOT grant permissions" requirement. Owner would allow role assignments.

## Question 7

B. Boot Diagnostics

Boot Diagnostics captures VM startup screenshots and serial console output, which are essential for diagnosing boot failures.

## Question 8

C. RA-GRS

RA-GRS replicates data to a secondary region (survives regional failure) and provides read access to the secondary during outage. GRS alone does not allow reading the secondary.

## Question 9

B. CanNotDelete lock

CanNotDelete allows read and modify operations but prevents deletion. ReadOnly would block modifications too.

## Question 10

C. Activity Log

Activity Log records subscription-level control-plane events including who performed operations (like deleting a VM) and when.

## Question 11

B. Deployment slots

Deployment slots let you deploy to a staging slot and swap to production with zero downtime, avoiding cold-start issues.

## Question 12

C. Route table with UDR

User-defined routes override Azure's default routing to force traffic through a virtual appliance (NVA) before reaching the Internet.

## Question 13

B. Azure Bastion

Bastion provides browser-based or native-client RDP/SSH directly through the Azure portal without needing a public IP on the VM.

## Question 14

C. Azure Front Door

Front Door is a global Layer 7 service with WAF, HTTP acceleration, and proximity-based routing. Traffic Manager is DNS-only (no WAF/acceleration); Application Gateway is regional.

## Question 15

A and B (Azure Monitor Agent + Data Collection Rules)

Azure Monitor Agent collects data from VMs, and Data Collection Rules define what to collect (perf counters, event logs) and where to send it.

## Question 16

C. Azure Policy

Azure Policy with a "Deny" effect on non-allowed locations automatically blocks deployments outside westeurope. RBAC controls who, not what.

## Question 17

C. ExpressRoute

ExpressRoute provides a private dedicated connection that bypasses the public Internet entirely, offering predictable latency and bandwidth.

## Question 18

B. IP Flow Verify

IP Flow Verify checks whether a packet is allowed or denied by NSG rules for a specific VM, port, and direction—ideal for diagnosing blocked traffic.

## Question 19

C. Azure Site Recovery

Site Recovery replicates VMs to a secondary region and orchestrates failover for disaster recovery. Azure Backup is for restore from backup, not live replication.

## Question 20

C. Custom Script Extension

Custom Script Extension runs scripts on a VM after deployment for automated configuration. Run Command is for ad-hoc execution, not deployment-time provisioning.

---

## Final Advice

If two answers both appear technically correct, focus on the requirement keywords and constraints.

AZ-104 questions are often testing the MOST appropriate administrative decision, not just a technically possible solution.

Always evaluate:
- least privilege
- operational effort
- availability requirements
- cost constraints
- private vs public connectivity
- regional vs zonal resiliency