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

Microsoft Azure Architect Design exam is what truly separates Azure architects from operators. Backed up by deployment and configuration skills, obtained from the AZ-300 exam, we will go into building more complex and robust systems in this part.

<!-- more -->

This post is part of my Azure Architect certification guide:

* [So you want to be an Azure Solutions Architect Expert - Part 1](/categories/blog/azure-solutions-architect-expert-part-1)
* [So you want to be an Azure Solutions Architect Expert - Part 2](/categories/blog/azure-solutions-architect-expert-part-2)

_Disclaimer: on June 29, AZ-303 and AZ-304 were released, but only in beta for the time being. Exams being replaced, i.e. AZ-300 and AZ-301, will retire on September 30. I will take into account AZ-304 curriculum, and not go into topics from AZ-301 that are becoming obsolete._

## So you want to Solution Architect (AZ-301: Microsoft Azure Architect Design)

![AZ-301: Microsoft Azure Architect Design](https://img-a.udemycdn.com/course/750x422/2375594_e17b.jpg)

Armed with knowledge of individual Azure services, design exam puts it all together under one roof. As a solution architect it will be your responsibility to create systems fulfilling both functional and non-functional requirements, while taking care not to overstep established restrictions. Desired reliability, availability, scalability and costs heavily influence your architecture and components used, hence a much deeper understanding of Azure services will be required.

### What type of design questions await

Question formats do not diverge much from the ones mentioned in [previous post](/categories/blog/azure-solutions-architect-expert-part-1/#What-type-of-technology-questions-await). You should however expect many more case-studies, which best suit to illustrate obstacles confronted by system designer.

### Where to find study materials

The same can be said for study materials. Microsoft documentation and video courses I previously suggested offer more than enough information needed to pass AZ-301 exam as well. Special significance should be paid to [Azure architecture section](https://docs.microsoft.com/en-us/azure/architecture/), containing reference architectures used for different types of deployments. I suggest studying most commonly used ones and how their individual pieces fit together to achieve desired system requirements.

### Availability
redundancy
	GEO, local, zone
	Azure service redundancy

### Scalability
choosing appropriate service tear
	database
	compute
auto-scaling

### Reliability
backup and recovery
	Azure Site Recovery
	Azure Backup
	archive
RTO, RLO, RPO

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
