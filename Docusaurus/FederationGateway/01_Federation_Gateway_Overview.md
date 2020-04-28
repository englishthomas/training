## What is the Federation Gateway (FGW)

The Federation Gateway or FGW integrates the Magma network with the Operators core network using standard 3GPP interfaces to existing MNO components.

### What Does it Do

The Federation Gateway or FGW iacts as a proxy between the Magma Access
Gateways (AGW) and the operator's network. It facilitates core
functions, such as authentication, data plans, policy enforcement, and
charging to stay uniform between an existing MNO network and the
expanded network with Magma.

The Federation Gateway (FGW) supports the following features and
functionalities:

1.  Federation Gateway hosts centralized control plane interface towards
    HSS, PCRF , OCS and MSC/ VLR on behalf of distributed AGW/EPCs.

2.  Establish diameter connection with HSS, PCRF and OCS directly as 1:1
    or via DRA.

3.  Establish SCTP/IP connection with MSC/VLR.

4.  The FGW interfaces with the AGW over GPRC interface by responding to
    and receiving remote calls from the EPC (MME and Sessiond/PCEF)
    components. It converts these to 3GPP compliant messages and sends
    these messages to the appropriate core network components such as
    HSS, PCRF, OCS and MSC.<br><br/>
    
The Federation Gateway includes the following Core Services:

-   ***S6a Proxy*** - provides GRPC based interface to HSS S6a
-   ***SWx Proxy*** - provides GRPC based interface to HSS SWx
-   ***Session Proxy*** - controls the session of each subscriber and provides interface to PCRF & OCS (Gx, Gy)
-   ***CSFB*** - the Circuit Switch Fall Back supports<br><br/>

### Additional FGW Features


*Resiliency*

Most of the resiliency features of the FGW are common amongst any gateway, such as system 
service restarts and service restart on mconfig update.

*Redundancy*

The FGW achieves redundancy through an active/standby configuration. Each gateway reports its health status to Orchestrator comprised of real-time system and service metrics, such as service availability, request failure percentage, and CPU utilization.

*Backup*

A Federation Gateway does not store persistent state, and a standby gateway can quickly be promoted to active during failover events. To aid in quick failover, standby gateway services run in idle mode and Diameter (authentication, authorization, and accounting
protocol) connections are ready to be created upon receipt of requests.<br><br/>

### Where does it Reside

The FGW resides in a Public or Private hosted cloud environment, either
on either a Bare-Metal or Virtual Machine. It sits between the Magma
Orchestrator and the MNO's core network.

![FGW Gateway](https://github.com/facebookincubator/magma/blob/master/docs/readmes/assets/federated_gateway_diagram.png)
<br><br/>

### Deploying Federation Gateway

The following pages will walk you through the full process of deploying a Federation Gateway, from building the various containers that you will need, to installing them on either a Bare-Metal or VM host. The installation guide targets *production* environments.

To get a head start on the development setup, you can build the FGW containers following this guide.

1.  Use docker-compose at ```magma/feg/gateway/docker``` to spin up the local version of the FGW.

<br><br/>

