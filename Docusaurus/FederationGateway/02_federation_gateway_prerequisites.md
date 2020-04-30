---
id: version-1.0.1-federation-gateway_prerequisites
title: Federation Gateway Prerequisites
sidebar_label: Prerequisites
hide_title: true
original_id: 02_federation_gateway_prerequisites.md
---

## Federation Gateway Prerequisites and Requiremets


### When is an FGW Required?

A Federation Gateway is required when a Mobile Operators Core Network needs to use a Carriers core service components;

- **MSC** Mobile Switching Center

- **HSS** Home Subscriber Service; PDN, APN, QoS Profile, Access Restrictions

- **PCRF** Policy Control and Charging Rules Function; Policy control decision-making

- **OCS** Online Charging System<br><br/>

### Federation Gateway Requirements

The prerequisites and recommended hardware requirements to deploy a
Federation Gateway (FGW)) are below. If you have deployed Orchestrator
(ORC8r) or an Access Gateway (AGW) some of the prerequisites may have
already been completed.

Obtain, Install and create the following:

1.  A working and running Magma Orchestrator

2.  [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) kubernetes CLI tool

3.  [Docker](https://www.docker.com/) to deploy Magma containers

4.  [Magma](https://github.com/facebookincubator/magma/) Dev Package from GitHub

5.  A Bare Metal (single tenant physical server) or a Virtual Machine.

6.  An AWS Account and familiarity with the AWS console.

7.  ```rootCA.pem``` - This file should match the ```rootCA.pem``` of the Orchestrator that the FGW will connect to

8.  ```control_proxy.yml``` - This file is used to configure the ```magmad``` and ```control_proxy``` services to point toward the
    appropriate Orchestrator. Follow the steps in "Installing the Federation Gateway" to create.

9.  ```.env``` - provides deployment specific environment variables used in the docker-compose.yml of the FGW.<br><br/>

Follow the steps in "Installing the Federation Gateway" to create.

To get a head start on the development setup, you can build the FGW
containers following the steps in this guide and use docker-compose
at ```magma/feg/gateway/docker``` to spin up the local version of the FGW.<br><br/>
