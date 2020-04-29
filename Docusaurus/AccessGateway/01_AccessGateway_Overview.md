---
id: version-1.0.1-accessgateway_overview
title: Access Gateway Overview
sidebar_label: AGW Overview
hide_title: true
original_id: 01_AccessGateway_Overview.md
---
## Magma Access Gateway (AGW) Overview


### What is the Access Gateway (AGW)

The Access Gateway provides the mobile packet core (EPC) functionality,
the network services and policy enforcement. The AGW is what makes Magma
a distributed architecture designed for horizontal scaling with the
radio access network.<br><br/>

### What Does it Do

The AGW works with existing, unmodified commercial radio hardware,
eNodeB's. In an LTE network, the AGW implements an evolved packet core
(EPC), and a combination of:

-   **MME** Mobility Management Entity, processes signaling

-   **PGW/PDN** Packet Network Data Gateway (SGi) provides PDN access, IP address allocation, policy control and charging.

-   **SGW** Service Gateway, providing packet transfer, mobility anchor

The AGW uses the **TR-069** Interface for both provisioning the eNodeB
and collecting the performance metrics. The eNodeB can also be
configured manually or through 3rd party management systems.<br><br/>

### Where Does it Reside

Conceptually, you can think of an AGW as a lightweight *software-defined network element* that is co-located with radio "near
the edge", for example, at a cell tower. The AGW can also be deployed in a public or private hosted cloud environment. Regardless of where it is deployed, the AGW is a remotely managed device through the Magma Orchestrator.


![Magma Access Gateway Architecture](https://github.com/englishthomas/training/blob/master/Docusaurus/assets/agw_architecture_v1.png){width="4.8in"
height="3.39in"}
<br><br/>
