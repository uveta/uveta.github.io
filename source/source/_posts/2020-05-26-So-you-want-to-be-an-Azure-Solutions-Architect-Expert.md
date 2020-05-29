---
title: So you want to be an Azure Solutions Architect Expert
tags:
  - azure
  - certification
date: 2020-05-26 17:48:14
---

![The culprit](https://miro.medium.com/max/3840/1*_HYOZExV1wV2f0OrfZ9YcA.png)

This article is intended for anyone actively trying to, or considering, obtaining Azure Solutions Architect Expert certification. As Azure currently offers well over 500 different services, even if you are not interested in certification, it can serve as a guideline to what Microsoft currently considers core Azure skills. 

_Disclaimer: as AZ-300 and AZ-301 are being replaced with AZ-303 and AZ-304 exams late June 2020, you might find some information provided in this article to be outdated. I will try to take into account any information available for future versions and not go into details about topics that are becoming obsolete._

## Prerequisites

In order to qualify for Azure Solutions Architect Expert certification (I will be referring to it as ASAE for the remainder of this text), you are required to pass two exams: AZ-300 and AZ-301. While AZ-300 deals mostly with skills related to deploying and configuring individual services to Azure, AZ-301 takes a deep dive into fulfilling business and system requirements such as availability, reliability, performance, as well as optimizing costs. As I have obtained ASAE near the end of 2019, I wanted to share relevant information gathered prior to taking exams, as well as personal experience, in order to help future applicants. I will focus mostly on technical aspects and go briefly through the types of questions you would encounter. Organizational information, such as exam length, scoring and how to apply, will not be a part of this article. Such instructions can be obtained from [Microsoft certification site](https://docs.microsoft.com/en-us/learn/certifications/azure-solutions-architect)

## Where to find study materials

![Your new best friend](https://i0.wp.com/win10.guru/wp-content/uploads/2019/03/MicrosoftDocs.jpg)

Answer to this question is very simple: [Microsoft Docs](https://docs.microsoft.com) has all the information you will ever need to prepare for this certification. The only problem is there a lot of it. Like, really a lot. Like, it would take you several lifetimes to only skim it through. Not to mention content is added to it on a daily basis. Still, official Microsoft documentation is the ultimate place where you will find answers to any question about Azure. It is also pretty easy to navigate and search for relevant information. If you have not worked with it already, take some time to familiarize yourself. Because you will be coming back to it regularly.

Nick Colyer's courses offered by [Skylines Academy](https://courses.skylinesacademy.com/p/az-300-301) helped me identify all relevant topics required for the exams. Both of them clock at about 20 hours in total, which could be covered in a single weekend, if you are persistent enough. The course immensely benefited me getting into the meat and bones of each exam and understanding what is required to pass. I cannot recommend it enough, as the course should serve you as a starting point into further studying. As a bonus piece of information, both courses are also available on [A Cloud Guru](https://acloud.guru/azure-cloud-training) which offers seven day free trial.

On the other hand, Pluralsight has great paths for both [AZ-300](https://www.pluralsight.com/paths/microsoft-azure-architect-technologies-az-300) and [AZ-301](https://www.pluralsight.com/paths/microsoft-azure-architect-design-az-301) exam. Each path consists of dozen courses going into great depth of each topic covered. What I consider its greatest obstacle is the sheer amount of video material, as both paths have, in total, well over 150 hours of content. I would recommend them only to applicants who have enough time to spare. If you are not one of them, try to go through the materials at increased speed (1.2x or 1.3x works fine) or at least use them to selectively strengthen knowledge in areas you find yourself lacking. Pluralsight comes with a ten day free trial, or, if you are lucky to have Visual Studio subscription, three to six months trial.

Lastly, try to go through as many practice tests as you can find. They will help you greatly, especially if you have never participated in this kind of exam. Some of them are offered on [ASAE certification page](https://docs.microsoft.com/en-us/learn/certifications/azure-solutions-architect). Also, both [AZ-300/AZ-303](https://www.udemy.com/course/azure-architect-technologies-practice-tests-az-300-az-303) and [AZ-301/AZ-304](https://www.udemy.com/course/az-301-azure-architect-design-practice-test) packs can be found at Udemy.

## So you want to Azure (AZ-300)

With basic information out of the way, it is time to plunge ourselves into technical skills required. Exam marked as AZ-300 will put your knowledge of Azure services, networking, deployment and configuration to test.

### What type of questions await

### Compute

### Storage

### Network and connectivity

This area is pretty straightforward, as it follows a set of explicit rules and there is not much space to get lost in thought. But do not even attempt to dive into it if you have knowledge gaps in topics such as IP addresses, CIDR notation, subnets, public and private ranges or what DNS is and how it works. All of these are just basis for whats to come, so make sure to handle them well first.

Exam specific topics include, but are not limited to:

* Virtual Networks (VNETs). Learn how to plan, deploy and configure them, how to use subnets effectively, what type of resources can you deploy to virtual network and what are the limitations.
* Connectivity. Find out what are the options when establishing connection between VNETs, on-premise locations and remote peers. Any combination is plausible, sometimes even all of them, and you need to know what kind of service is adequate for given scenario. Spend some time learning about VNET peering, ExpressRoute and VPN options.
* Routing and load balancing. Although not heavily emphasized in this exam, knowledge of different network appliances is still needed. Make sure you know what is the purpose of load balancer, Application Gateway or custom appliance deployed as virtual machine, and some basic deployment and configuration steps.
* Security. Another topic that can easily slip you by. You need to understand what are default security rules for resources deployed to VNETs and how can they be adjusted further. Do not miss reading on Network Security Group (NSG), how to define individual security rules and where can NSGs be applied.
* DNS. Understand various Azure DNS service offerings. Make sure you know the difference between CNAME and A record and how are they used to perform different tasks in Azure. Examples include overriding default assigned server names or exposing deployed Azure services under your specific domain.

### Monitoring

### Backup and recovery

### Miscellaneous

## So you want to Solution Architect (AZ-301)

### What type of questions await

## References
