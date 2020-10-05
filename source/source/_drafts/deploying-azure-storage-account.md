---
title: Deploying Azure storage account
tags:
  - azure
  - beginner
  - deployment
categories:
  - blog
disqusId: deploying-azure-storage-account
---


![Placeholder](https://via.placeholder.com/800x350/0000FF/808080/?text=StorageBanner)

With this post I am starting a new series that should help every person starting to work on Azure platform. I will go through deployment of most commonly used Azure services, while describing all different options that can be configured. Even more expirienced users can find useful information, as it might include options that they have not utilized before.

<!-- more -->

I will not go through common options: choosing subscription, region adding tags, review and create

## Basic

![Placeholder](https://via.placeholder.com/800x350/0000FF/808080/?text=Basic)

1. Storage account name - globally unique, since it can have public access
2. Location - region to store primary storage (see replication mode)
3. Performance - Standard (backed up by tape drives), Premium (backed up by premium SSD disk offering) - offers faster read and write times, good for frequently accessed storage
4. Account kind
5. Replication mode
| Replication mode 	| Data persistence           	|
|------------------	|----------------------------	|
| LRS              	| Locally redundant storage 	|
| RA-GRS           	| Read access global storage 	|
| ZRS              	| Zone redundant storage     	|
6. Blob access tier - default access tier to be used; can be overridden during blob creation and changed for already existing blobs; archive tier cannot be selected as default

## Networking

![Placeholder](https://via.placeholder.com/800x350/0000FF/808080/?text=Networking)

Connectivity:
1. Public endpoint (all networks)
2. Public endpoint (selected networks)
3. Private endpoint

Routing:
1. Microsoft network routing - traffic between other Azure resources and this storage will flow through internal Azure network
2. Internet routing - all traffic to this storage will use public network (Internet)

## Data Protection

![Placeholder](https://via.placeholder.com/800x350/0000FF/808080/?text=DataProtection)

Recovery:
1. Point-in-time restore
2. Blob soft delete
3. Container soft delete
4. File share soft delete

Tracking:
1. Versioning
2. Change feed

## Advanced

![Placeholder](https://via.placeholder.com/800x350/0000FF/808080/?text=Advanced)

Security
1. Require secure transfer
2. Allow blob public access
3. Minimum TLS version
4. Infrastructure encryption (preview)

Azure Files
1. Large file shares

Data Lake Storage Gen2
1. Hierarchical namespace
2. NFS v3 (preview)

## Conclusion

In this post I've shown you how to deploy a storage account, using Azure Portal. We went through all configuration options currently available. I have assessed available values and included my recommendations, based on individual needs. After mastering this you wont have any problem providing persistence for your applications.