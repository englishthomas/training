---
id: version-1.0.1-architecture
title: Basics Architecture
sidebar_label: Architecture
hide_title: true
original_id: architecture
---
# Magma Architecture

The figure below shows a high-level Magma Network Architecture. Magma is
designed to be 3GPP generation and access network agnostic (cellular or
WiFi) where all devices can connect. It can flexibly support a radio
access network with minimal development and deployment effort.

Magma has three major components:

-   **Access Gateway (AGW):**  The Access Gateway provides the mobile
    packet core (EPC) functionality, the network services and policy
    enforcement. The AGW is what makes Magma a distributed
    architecture designed for horizontal scaling with the radio access
    network. Conceptually, you can think of an AGW as a lightweight
    *software* element that is co-located "near the edge", for
    example, at a cell tower, as software connected to an eNodeB. The
    AGW can also be deployed in a public or private cloud hosted
    environment.

	The AGW uses the **TR069** (S1) Interface for both provisioning the
	eNodeB and collecting the performance metrics. It defines the
	application layer protocol for remote management.

The AGW works with existing, unmodified commercial radio hardware. In an
LTE network, the AGW implements an evolved packet core (EPC), and a
combination of:

<ul>
	<ul>
        <li> MME Mobility Management Entity</li>
        <li> PGW (SGi) Packet Gateway </li>
        <li> SGW Service Gateway </li>
</ul>


-   **Orchestrator (ORC8r):**  The Orchestrator is a cloud service that
    provides a simple and consistent way to configure, manage and
    monitor the wireless network securely. The Orchestrator can be
    hosted on a public or private cloud environment. Access gateways
    are configured and monitored through the cloud-based Orchestrator
    allowing for a consistent way of managing all access gateways.
    
    The Orchestrator has 3 main functions:

    -   **Network Management System (NMS):** The NMS provides bootstrap
        configuration and monitoring of the system. The metrics
        acquired through the platform allow for visibility into the
        analytics and traffic flows of the wireless users through the
        Magma web UI. The Network Monitoring System also raises alerts
        and alarms, logging messages, and reporting events through
        RESTful API's. It displays radio locations on a map and
        includes performance management.
        
        New hardware is added and configured to the system from the
        NMS. Subscriber management is performed in the NMS. Configure
        eNodeB's, Access Gateways, and Data Plans.

    -   **Key Performance Indicators (KPI)**: The network elements
        (Radios, Gateways etc.) generate different kinds of KPIs that
        can be calculated from the data flow across the wireless
        network. The Orchestrator creates, stores and exports these
        KPIs for visualization of usage across the network, for
        example, connection speeds, rates, uptime, etc.

    -   **Relay**: The Orchestrator acts as a relay between gateway
        nodes allowing for the brokering of trust between nodes
        running in different domains.

-   **Federation Gateway (FGW):**  The Federation Gateway integrates the
    Mobile Network Operators (MNO) core network with Magma by using
    standard 3GPP interfaces to existing MNO components. It acts as a
    proxy between the Magma AGW and the operator\'s network. It
    *facilitates core functions*, such as authentication, data plans,
    policy enforcement, and charging to stay uniform between an
    existing MNO network and the expanded network with Magma.
    
    Components the Federation Gateway may interface with include:

    -   MSC (SGs) Mobile Switching Center or VLR (Visiting Location Requestor)

    -   HSS (S6a) Home Subscriber Service; PDN, APN, QoS Profile, Access Restrictions

    -   PCRF (Gx) Policy Control and Charging Rules Function

    -   OCS (Gy) Online Charging
	
	![Magma architecture diagram](assets/magma_overview.png?raw=true "Magma Architecture")
	
        > System![](media/image1.png){width="6.479166666666667in"
        > height="3.1666666666666665in"}

**Common Acronyms**

See the Magma Acronymns and Jargon list.
