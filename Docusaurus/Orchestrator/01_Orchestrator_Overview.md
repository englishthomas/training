# Orchestrator Overview

Some Markdown text with <span style="color:blue">some *blue* text</span>.

The following pages will walk you through the full process of spinning
up a full Orchestrator deployment, from building the various containers
that you\'ll need, to deploying them onto Amazon Elastic Kubernetes
Service (EKS). This installation guide targets *production* environments.

Familiarity with the AWS console and the Kubernetes command line are
expected. The instructions in this section have been tested on MacOS and
Linux. If you are deploying from a Windows host, some shell commands may
require adjustments.

If you want to get a head start on the development setup, you can build
the Orchestrator containers following this guide and use docker-compose
at magma/orc8r/cloud/docker to spin up the local version of
Orchestrator.

**This installation guide targets *production* environments**<br/><br/>

## What is Orchestrator

The Magma **Orchestrator** (orc8r) is a cloud-hosted network management
plane that provides simple and consistent configuration management and
the ability to monitor the wireless network securely.

The Orchestrator has a user interface or dashboard called **NMS**
(Network Management System) used to configure devices & subscribers,
monitor the network, and KPIs.<br/><br/>

## What Does it Do

The Orchestrator communicates with a southbound API exposed to AGW\'s
and a northbound rest API to update the configuration and monitor
network status. The Orchestrator is logically centralized, and presents
an abstract logical view of the network. Because the controller is only
"logically centralized", the actual implementation of the control plane
need not be implemented in a centralized fashion; it could be built as a
purely distributed application as well.

The Access Gateways (AGW), which are commonly co-located with radio
equipment (ideally in the same physical enclosure); communicate with
Orchestrator to respond to configuration updates and report network
state.

The Orchestrator ensures security with the use of client side
certificates with SSL, a TPM key, SSH access, OpenVPN, and cloud
authentication. The orc8r maintains its own subscriber database but can
federate authentication to an existing HSS if one does not exist and can
push the data to each AGW on the network. Configuration changes are made
in orc8r and pushed down to all the AGWs.<br/><br/>

## Where does it Reside

The Orchestrator can be hosted on a public or private cloud. For
production deployments Magma works in an Amazon Elastic Kubernetes
Service (EKS) environment or on any compliant Kubernetes (k8s) cluster.
This installation guide targets *production* environments.

The following diagram shows Orchestrator network architecture:

![A screenshot of a cell phone Description automatically
generated](media/image2.png){width="4.96431539807524in"
height="3.4819160104986877in"}

*Orchestrator Network Architecture*
