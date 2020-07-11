---
title: So you want to be an Azure Solutions Architect Expert - Part 2
tags:
  - azure
  - certification
  - advanced
date: 2020-07-01 17:48:14
categories:
  - blog
disqusId: azure-solution-architect-expert-part-2
---

![Goal!!!](https://www.thomasmaurer.ch/wp-content/uploads/2019/01/Azure-Solutions-Architect-Expert.jpg)

Microsoft Azure Architect Design exam is what truly separates Azure architects from operators. Backed up by deployment and configuration skills, obtained from preparation for technologies exam, we will go into building more complex and robust systems in this part.

<!-- more -->

This post is part of my Azure Architect certification guide:

* [So you want to be an Azure Solutions Architect Expert - Part 1](/categories/blog/azure-solutions-architect-expert-part-1)
* [So you want to be an Azure Solutions Architect Expert - Part 2](/categories/blog/azure-solutions-architect-expert-part-2)

_Disclaimer: on June 29, AZ-303 and AZ-304 were released, but only as beta for the time being. Exams being replaced, i.e. AZ-300 and AZ-301, will retire on September 30. I will take into account AZ-304 curriculum, and not go into AZ-301 topics that are becoming obsolete._

## So you want to Solution Architect (AZ-301: Microsoft Azure Architect Design)

![AZ-301: Microsoft Azure Architect Design](https://img-a.udemycdn.com/course/750x422/2375594_e17b.jpg)

Armed with knowledge of individual Azure services, design exam puts it all together under one roof. As a solution architect it will be your responsibility to create systems fulfilling both functional and non-functional requirements, while taking care not to overstep established restrictions. Desired reliability, availability, scalability and costs heavily influence your architecture and components used, hence a much deeper understanding of Azure services is required.

### What type of design questions await

Question formats do not diverge much from the ones mentioned in [previous post](/categories/blog/azure-solutions-architect-expert-part-1/#What-type-of-technology-questions-await). You should however expect many more case-studies, which best suit to illustrate obstacles confronted by a system designer.

### Where to find study materials

The same can be said for study materials. Microsoft documentation and video courses I previously suggested offer more than enough information needed to pass design exam as well. Special significance should be paid to [Azure architecture section](https://docs.microsoft.com/en-us/azure/architecture/), containing reference architectures used for different types of deployments. I suggest studying most commonly used ones and how their individual pieces fit together to achieve desired system requirements.

### Availability
Understanding how Azure datacenters are organized on a global scale is imperative when planning for availability of your deployment. Although Microsoft offers generous SLA for all of its services, outages happen, and careful planning can help you mitigate such interruptions. Most important topics that are covered by design exam include:

* Datacenter. Basic building block of Azure, consisting of physical devices, storage, power and network connections.
* Availability zone. Collection of one or more datacenters with independent power and network connections. Most Azure resources can be distributed between different availability zones, helping against individual datacenter outages.
* Region. Specific location where Azure resources are deployed, guaranteeing compliance and data residency for specific state or country where datacenters are located. With regard to availability zones, each region contains at least 3 of them.
* Regional pair. Each Azure region is paired with another one, with prescribed minimum distance between them. On one hand, it helps in case of regional outages, due to natural disasters or any similar impediments. Any service update is rolled to one region per pair at a time. Also, in case of global Azure outage, Microsoft will prioritize restoring only one region per pair.
* Geography. Regions belonging to the same country are organized into geographies. Any regional pair is usually a part of the same geography, ensuring compliance with local laws. Exceptions do exists, especially for new geographies where usually only one region is deployed.

Besides knowing how outage in any part of Azure infrastructure could affect you, it is vital to understand how specific resources are deployed. While most of them are being deployed to specific regions, some of them, e.g. Traffic Manager, are non-regional and are deployed globally. Also, get to know which services require distributing them into availability zones, in order to protect them from datacenter outages.
	
### Reliability
backup and recovery
	Azure Site Recovery
	Azure Backup
	archive
RTO, RLO, RPO

### Scalability
choosing appropriate service tear
	database
	compute
auto-scaling

### Security
user management and authentication
	access, SSO, MFA
	hybrid identity (Azure AD Connect and Azure AD Connect Health)
	self-service
	B2B
	PIM, RBAC, Identity Protection, JIT
application security and authentication
	Key Vault, Managed Identity
data protection
	encryption at rest, in transit, in use

### Governance, auditing, compliance
tagging
Azure Policy
Azure Blueprint

### Misc
Networking
	addressing strategy
	security
	hybrid networks
	load balancing and routing
Compute
	determine appropriate service (VM, App Service, Service Fabric, Azure Functions, containers)
	AKS vs ACI
Integration
	external
	public API - gateway architecture, management, keys

### Cost optimization
RTO RLO
