---
id: version-1.0.1-quick_start
title: Basics Quick Start Guide
sidebar_label: Quick Start Guide
hide_title: true
original_id: quick_start_guide.md
---
## Quick Start Guide

Use this guide for developing on Magma or just trying it out. This quick
start guide is **not** for deploying Magma in a production environment.
Use the deployment guides under Orchestrator and Access Gateway if you
are installing Magma for a production deployment.

With the [prerequisites](https://facebookincubator.github.io/magma/docs/basics/prerequisites) installed
and completed, follow the below steps to setup a minimal end-to-end
system on your development environment.

Begin by running the LTE Access Gateway (AGW) and Orchestrator (orc8r)
cloud. Then register your local access gateway with your local cloud for
management.

We will be spinning up a virtual machine and docker containers for this
full setup, it is recommended to do this on a system with at least 8GB
of memory. Our development VM's are in the 192.168.60.0/24,
192.168.128.0/24 and 192.168.129.0/24 address spaces, so make sure that
you don't have anything running which hijacks those (e.g. VPN).

In the following steps, note the prefix in terminal commands. ```HOST``` means
to run the indicated command on your host machine, and ```MAGMA-VM``` on
the ```magma``` vagrant machine under ```lte/gateway```<br/><br/>

### Provisioning the Magma Environment

Open up a 2 fresh Terminal tabs.<br/>

#### Terminal Tab 1: Provision the AGW VM

The development environment virtualizes the Access Gateway, no
production hardware is required to test an end-to-end setup. We\'ll be
setting up the LTE AGW VM in this tab.

~~~
HOST [magma\]$ cd lte/gateway
HOST [magma/lte/gateway]\$ vagrant up magma
~~~

This will take a few minutes to spin up the VM. While that runs, switch
over to Terminal Tab 2.

**Note**: If you are looking to test/develop the LTE features of AGW,
without cloud based network management, you can skip the rest of this
guide and try the [S1AP integration
tests](https://facebookincubator.github.io/magma/docs/next/lte/s1ap_tests) now.<br/><br/>

#### Terminal Tab 2: Orchestrator Build

Here, we\'ll be building the Orchestrator docker containers.

```
HOST [magma]$ cd orc8r/cloud/docker
HOST [magma/orc8r/cloud/docker]\$ ./build.py -a
```

This will build all the docker images for Orchestrator.
The ```vagrant up``` from the first tab should finish before the Orchestrator
image build. Switch to Terminal Tab 1 and continue.<br/><br/>

### Initial Run

Once ```vagrant up``` in Terminal Tab 1 completes, continue with the AGW build.<br/><br/>


#### Terminal Tab 1: Build the AGW from Source

Begin the initial build of the AGW from source here.

```
HOST [magma/lte/gateway]$ vagrant ssh magma
MAGMA-VM [/home/vagrant]$ cd magma/lte/gateway
MAGMA-VM [/home/vagrant/magma/lte/gateway]$ make run
```

This will take a while, there are many CXX files to build. The first
build from source will take a while, afterwards, a persistent ccache and
Docker\'s native layer caching will speed up subsequent builds
significantly.

You can monitor what happens in the other tab.<br/><br/>

#### Terminal Tab 2: Start Orchestrator

Once the Orchestrator build completes, start the development
Orchestrator cloud for the first time. In addition, register the local
client certificate you\'ll need to access the local API gateway for your
development stack.

### To start Orchestrator (*without metrics*):

```
HOST \[magma/orc8r/cloud/docker\]\$ ./run.sh

Creating orc8r\_postgres\_1 \... **done**
Creating orc8r\_test\_1 \... **done**
Creating orc8r\_maria\_1 \... **done**
Creating elasticsearch \... **done**
Creating fluentd \... **done**
Creating orc8r\_kibana\_1 \... **done**
Creating orc8r\_proxy\_1 \... **done**
Creating orc8r\_controller\_1 \... **done**
```

### To start Orchestrator (*with metrics*):

```
HOST \[magma/orc8r/cloud/docker\]\$ ./run.sh -all

Creating orc8r\_alertmanager\_1 \... **done**
Creating orc8r\_maria\_1 \... **done**
Creating elasticsearch \... **done**
Creating orc8r\_postgres\_1 \... **done**
Creating orc8r\_grafana\_1 \... **done**
Creating orc8r\_config-manager\_1 \... **done**
Creating orc8r\_test\_1 \... **done**
Creating orc8r\_prometheus-cache\_1 \... **done**
Creating orc8r\_prometheus\_1 \... **done**
Creating orc8r\_kibana\_1 \... **done**
Creating fluentd \... **done**
Creating orc8r\_proxy\_1 \... **done**
Creating orc8r\_controller\_1 \... **done**
```

### Certificates

The Orchestrator application containers will bootstrap **certificates**
on startup which are cached for future runs. Watch the
directory ```magma/.cache/test_certs``` for a file
named ```admin_operator.pfx``` to appear, this may take a minute or 2, then
run:

```
HOST \[magma/orc8r/cloud/docker\]\$ ls ../../../.cache/test\_certs

admin\_operator.key.pem bootstrapper.key controller.crt rootCA.key
admin\_operator.pem certifier.key controller.csr rootCA.pem
admin\_operator.pfx certifier.pem controller.key rootCA.srl

HOST \[magma/orc8r/cloud/docker\]\$ open ../../../.cache/test\_certs
```

In the Finder window that pops up, double-click ```admin_operator.pfx``` to
add the local client cert to your keychain. The password for the cert is
*magma*. In some cases, you may have to open up the Keychain app in
MacOS and drag-drop the file into the login keychain if double-clicking
doesn\'t work.

If you use Firefox, you\'ll have to import this .pfx file into your
browser\'s installed client certificates.
See [here](https://support.globalsign.com/customer/en/portal/articles/1211486-install-client-digital-certificate---firefox-for-windows) for
instructions. If you use Chrome or Safari, you may have to restart the
browser before the certificate can be used.<br/><br/>

### Connecting Your Local LTE Gateway to Your Local Cloud

At this point, you will have built all the code in the LTE Access
Gateway and the Orchestrator cloud. All the services on the LTE access
gateway and orchestrator cloud are running, but your gateway VM isn\'t
yet set up to communicate with your local cloud.

We have a fabric command set up to have the gateway communicate with
your local cloud:

```
HOST \[magma\]\$ cd lte/gateway
HOST \[magma/lte/gateway\]\$ fab -f dev\_tools.py register\_vm
```

This command will seed your gateway and network on Orchestrator with
some default LTE configuration values and set your gateway VM up to talk
to your local Orchestrator cloud. Wait a minute or two for the changes
to propagate, then verify that things are working:

**Verify AGW Communication**

```
HOST \[magma/lte/gateway\]\$ vagrant ssh magma

MAGMA-VM\$ sudo service magma@\* stop
MAGMA-VM\$ sudo service magma\@magmad restart
MAGMA-VM\$ sudo tail -f /var/log/syslog

\# After a minute or 2 you should see these messages:
Sep 27 22:57:35 magma-dev magmad\[6226\]: \[2018-09-27 22:57:35,550 INFO root\] Checkin Successful!
Sep 27 22:57:55 magma-dev magmad\[6226\]: \[2018-09-27 22:57:55,684 INFO root\] Processing config update g1
Sep 27 22:57:55 magma-dev control\_proxy\[6418\]: 2018-09-27T22:57:55.683Z \[127.0.0.1 -\> streamer-controller.magma.test,8443\] \"POST /magma.Streamer/GetUpdates HTTP/2\" 200 7bytes 0.009s
```

### Setup the Network Management System (NMS)

Magma provides a UI for configuring and monitoring the networks.\
To set up the NMS to talk to your local Orchestrator:

```
HOST \[magma\]\$ cd symphony/app/fbcnms-projects/magmalte
HOST \[magma/symphony/app/fbcnms-projects/magmalte\] \$ docker-compose build magmalte
HOST \[magma/symphony/app/fbcnms-projects/magmalte\] \$ docker-compose up -d
HOST \[magma/symphony/app/fbcnms-projects/magmalte\] \$ ./scripts/dev\_setup.sh
```

When the NMS setup is complete, access the UI by
visiting [https://fb-test.localhost](https://fb-test.localhost/), and
using the email ```admin@magma.test``` and password ```password1234```. We
recommend Firefox or Chrome. If you see Gateway Error 502, don\'t worry,
the NMS can take up to 60 seconds to finish starting up.

**Note**: If you want to test the Access Gateway VM with a physical
eNodeB and UE, refer to the [Connecting a physical eNodeb and UE device
to gateway
VM](https://facebookincubator.github.io/magma/docs/next/lte/dev_notes#connecting-a-physical-enodeb-and-ue-to-gateway-vm) section.
