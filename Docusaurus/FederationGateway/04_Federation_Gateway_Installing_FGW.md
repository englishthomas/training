## Installing the Federation Gateway (FGW)

To install the Federation Gateway, there are 3 required files that are deployment-specific.

These are described below:

1.  ```rootCA.pem``` - This file should match the ```rootCA.pem``` of the Orchestrator that the FGW will connect to.

2.  ```control_proxy.yml``` - This file is used to configure the magmad and control_proxy services to point toward the
    appropriate Orchestrator.
    
    A sample configuration is provided below.
    
    The ```bootstrap_address```, ```bootstrap_port```, ```controller_address```, and ```controller_port``` are the parameters that will     likely need to be modified.

3.  ```.env``` - This file provides any deployment specific environment variables used in the docker-compose.yml of the FGW. A sample
    configuration is provided below:<br<<br/>

### Sample control.proxy.YML

```
#
# Copyright (c) 2016-present, Facebook, Inc.
# All rights reserved.
#
# This source code is licensed under the BSD-style license found in the
# LICENSE file in the root directory of this source tree. An additional grant
# of patent rights can be found in the PATENTS file in the same directory.

# nghttpx config will be generated here and used
nghttpx_config_location: /var/tmp/nghttpx.conf

# Location for certs
rootca_cert: /var/opt/magma/certs/rootCA.pem
gateway_cert: /var/opt/magma/certs/gateway.crt
gateway_key: /var/opt/magma/certs/gateway.key

# Listening port of the proxy for local services. The port would be losed
# for the rest of the world.
local_port: 8443

# Cloud address for reaching out to the cloud.
cloud_address: controller.magma.test
cloud_port: 443

bootstrap_address: bootstrapper-controller.magma.test
bootstrap_port: 443

# Option to use nghttpx for proxying. If disabled, the individual
# services would establish the TLS connections themselves.
proxy_cloud_connections: True

# Allows http\_proxy usage if the environment variable is present
allow_http_proxy: True<br><br/>

### Sample .env

\# Copyright (c) 2016-present, Facebook, Inc.

\# All rights reserved.

\#

\# This source code is licensed under the BSD-style license found in the

\# LICENSE file in the root directory of this source tree. An additional
grant

\# of patent rights can be found in the PATENTS file in the same
directory.

COMPOSE\_PROJECT\_NAME=feg

DOCKER\_REGISTRY=\<registry\>

DOCKER\_USERNAME=\<username\>

DOCKER\_PASSWORD=\<password\>

IMAGE\_VERSION=latest

ROOTCA\_PATH=/var/opt/magma/certs/rootCA.pem

CONTROL\_PROXY\_PATH=/etc/magma/control\_proxy.yml

SNOWFLAKE\_PATH=/etc/snowflake

CERTS\_VOLUME=/var/opt/magma/certs

CONFIGS\_VOLUME=/var/opt/magma/configs

\# This section is unnecessary if using host networking

S6A\_LOCAL\_PORT=3868

S6A\_HOST\_PORT=3868

S6A\_NETWORK=sctp

SWX\_LOCAL\_PORT=3869

SWX\_HOST\_PORT=3869

SWX\_NETWORK=sctp

GX\_LOCAL\_PORT=3870

GX\_HOST\_PORT=3870

GX\_NETWORK=tcp

GY\_LOCAL\_PORT=3871

GY\_HOST\_PORT=3871

GY\_NETWORK=tcp

Installation
------------

The installation is completed using the install\_gateway.sh script
located at magma/orc8r/tools/docker.\\

To Install the FGW:

1.  Copy install\_gateway.sh, and the three files described abov;
    rootCA.pem , control\_proxy.yml , .env  into a directory on the
    install host. Then run:

INSTALL\_HOST \[\~/\]\$ sudo ./install\_gateway.sh feg

Installed successfully!!

Registration
------------

To Register the Federation Gateway with the Orchestrator.

1.  To register the FGW run the following:

INSTALL\_HOST \[\~/\]\$ cd /var/opt/magma/docker

INSTALL\_HOST \[/var/opt/magma/docker\]\$ docker-compose exec magmad\
/usr/local/bin/show\_gateway\_info.py

2.  This will output a *hardware ID* and a *challenge key*. This
    information must be registered with the Orchestrator.

**Add\> How to register ID and Key w/ Ochestrator**

3.  To register the FGW go to the Orchestrator\'s APIdocs in your
    browser. **Note: It is highly encouraged to use V1 of the
    apidocs** (i.e. <https://controller.url.sample:9443/apidocs/v1/>).

4.  Create a Federation Network. This is found at /feg under
    the **Federation Networks** section.

5.  Register the gateway under the **Federation Gateway** section
    at /feg/{network\_id}/gateways using the *network ID* of the
    Federation Network and the *hardware ID* and *challenge key* from
    the previous step.

6.  Verify that the gateway was correctly registered, run:

> INSTALL\_HOST \[\~/\]\$ cd /var/opt/magma/docker
>
> INSTALL\_HOST \[/var/opt/magma/docker\]\$ docker-compose exec magmad
> /usr/local/bin/checkin\_cli.py

**Upgrades**

The Federation Gateway supports NMS initiated upgrades. These can be
triggered from the NMS under the Configure section by updating the
FGW\'s tier to the appropriate Software Version. After triggering the
upgrade from the NMS, magmad on the gateway will pull down the specified
docker images, update any static configuration, and update the
docker-compose file to the appropriate version.
