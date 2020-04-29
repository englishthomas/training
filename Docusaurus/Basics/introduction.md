---
id: version-1.0.1-introduction
title: Basics Introduction
sidebar_label: Introduction
hide_title: true
original_id: introduction
---
## What is Magma

Magma is an open source, software platform that gives network operators
a manageable, flexible and extendable mobile core network solution. It
is a distributed core network architecture for building and supporting
different types of access networks (4G LTE, Wi-Fi, CBRS, etc.) and
provides network services as pluggable modules for building such
networks. Network services in this context can be authentication,
metering, subscriber management, IP allocation, mobile edge computing
services, etc.

At a high level, Magma consists of 4 components.

1.  **Access Gateways (AGW)** LTE base stations

2.  **Orchestrator (orc8r)**  A centralized controller 

3.  **Network Management System (NMS)**  A console dashboard

4.  **The Federation Gateway (FGW)** a centralized control plane
    interface towards HSS, PCRF , OCS and MSC.<br><br/>

Magma provides a flexible building block for an IP access network by
decoupling the data plane, and network control plane or management
plane. In Magma, all implementation of network policy is against a
logical model of the network with functionality implemented in software,
as opposed to being implemented in H/W appliances. By decoupling policy
definition (i.e., operator intent) from policy implementation (i.e.,
packet processing), we achieve greater deployment flexibility than
today\'s networks and provide a common basis upon which any IP access
network can be built.<br><br/>

#### Magma enables better connectivity by:

-   Offering an Open Source packet core network, enabling a faster
    connections, with no need to connect to a centralized data center

-   Allowing for access network and device agnostic, cellular or WiFi

-   Utilizing a distributed EPC with a small footprint

-   Enabling operators to manage their networks more efficiently with
    more automation, less downtime, better predictability, and more
    agility to add new services and applications

-   Allowing for integration with existing MNO (Mobile Network
    Operators) core

Magma helps with global internet connectivity challenges of
accessibility, affordability, and relevance, by allowing for fast, less
expensive to deploy, easier to manage, wireless networks.

It is an Open Source solution and scales horizontally as a distributed
cellular core network.<br><br/>

### Deployment Environment Tolerance
Our typical deployments involve running devices in remote areas, on
backhaul networks with arbitrary topologies, behind network address
translations (NATS), and where intermittent or unreliable connectivity
is a common case.<br><br/>

### Common Acronyms

See the Magma Acronyms & Jargon list
