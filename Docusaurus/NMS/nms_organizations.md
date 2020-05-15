---
id: nms_organizations
title: NMS Organizations
hide_title: true
---

NMS Organizations
In version 1.1 of Magma, the NMS includes the concept of “Organizations”, or segmentations of networks which controls what networks an organization can access. Organizations are controlled via the master organization, which is a special org accessible from master.<nms-hostname> once you have created a user for that org (see: first-time setup)

First-time Setup
When you deploy the nms for the first time, you’ll need to create a user that has access to the master organization, run the command

Docker:
docker-compose exec magmalte yarn setAdminPassword master <email> <password>
Kubernetes:
kubectl exec <magmalte-container> -- yarn setAdminPassword master <email> <password>
Examples
Single-tenant: Create one organization and give it access to all networks
