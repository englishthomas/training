## Access Gateway Configuration

### Prerequisites
Before beginning to configure your Magma Access Gateway, confirm you have:

1.  Completed all the steps in [Access Gateway Setup (On Bare
    Metal](https://facebookincubator.github.io/magma/docs/lte/setup_deb).
    For this part, we strongly recommend that you SSH into the AGW box from a host machine instead of using the AGW directly.

2.  Verify that all services are running on the AGW host without crashing by running ```service magma@* status```

3.  A working Orchestrator setup. Please follow the instructions in [Deploying
    Orchestrator](https://facebookincubator.github.io/magma/docs/orc8r/deploying) for a successful Orchestrator installation.<br><br/>


### AGW Configuration 
----------------------------

1.  Copy the root CA for your Orchestrator deployment into your AGW:

```
HOST$ scp rootCA.pem magma@10.0.2.1:~
HOST$ ssh [[magma\@10.0.2.1]{.underline}](about:blank)

AGW\$ sudo mkdir -p /var/opt/magma/tmp/certs/
AGW\$ sudo mv rootCA.pem /var/opt/magma/tmp/certs/rootCA.pem
```
<br/>

2.  Point your AGW to your Orchestrator:

```
AGW$ sudo mkdir -p /var/opt/magma/configs
AGW$ cd /var/opt/magma/configs
AGW$ sudo vi control_proxy.yml
```
<br/>

3.  Put the following contents into the file:

```
**cloud_address**: controller.yourdomain.com
**cloud_port**: 443
**bootstrap_address**: bootstrapper-controller.yourdomain.com
**bootstrap_port**: 443

rootca_cert: **/var/opt/magma/tmp/certs/rootCA.pem
```
<br/>

4.  Restart your services to pick up the config changes:

```
AGW$ sudo service magma@* stop
AGW$ sudo service magma@magmad restart
```
<br/>

### Creating and Configuring Your Network

Navigate to your Network Management System (NMS) instance,
[https://nms.yourdomain.com](https://nms.yourdomain.com), log in with the administrator credentials you provisioned when installing Orchestrator.<br><br/>

### Create a Network

If this is a fresh Orchestrator install, you will be prompted to create your first network, otherwise create a Network:

1.  Select **Add Network** from the network selection icon at the top right of the screen

2.  Select *lte* as the network type and complete the other fields as you see fit

![A screenshot of a cell phone Description automatically
generated](media/image1.png){width="4.7273687664042in"
height="3.546537620297463in"}
<br/>

### Configure the RAN/Network Configuration

To get to Network Management use the app selector in the bottom left of the dashboard screen.

1.  Select **Configure** from the left sidebar

2.  Select "Network Configuration" from the tab at the top of the screen

3.  Configure your RAN and EPC parameters which are appropriate for your hardware setup

![](media/image2.png){width="4.586971784776903in"
height="3.441502624671916in"}
<br/>

### Registering and Configuring Your Access Gateway

1.  **Challenge Key**
    Copy the Challenge Key or hardware secrets from your AGW host:

```
    AGW\$ show\_gateway\_info.py
    Hardware ID:
    -----------
    1576b8e7-91a0-4e8d-b19f-d06421ad72b4
    
    Challenge Key:
    -------------
    MHYwEAYHKoZIzj0CAQYFK4EEACIDYgAECMB9zEbAlLDQLq1K8tgCLO8Kie5IloU4QuAXEjtR19jt0KTkRzTYcBK1XwA+C6                              
    ALVKFWtlxQfrPpwOwLE7GFkZv1i7Lzc6dpqLnufSlvE/Xlq4n5K877tIuNac3U/8un
```

2.  **Add Gateway**

    a.  Navigate to the "Gateways" icon from the left sidebar

    b.  Select "Add Gateway"

    c.  Complete the Add Gateway form using the Challenge Key from above

![](media/image3.png){width="4.602597331583552in"
height="3.379073709536308in"}
<br/>

### Configure the RAN
After you create your Access Gateway, configure your EPC/RAN.

1.  Click the Edit icon in its table row

2.  Select the "LTE" tab, and enter the EPC/RAN parameters

3.  Enable eENODEB transmit

![](media/image4.png){width="5.565138888888889in"
height="4.085742563429571in"}
<br/>

### Validate the AGW
Validate the connection between your AGW and Orchestrator:

```
AGW$ journalctl -u magma@magmad -f
    # Look for the following logs
    # INFO:root:Checkin Successful!
    # INFO:root:\[SyncRPC\] Got heartBeat from cloud
    # INFO:root:Processing config update gateway\_id
```
<br/>

Next steps, configuring an eNodeB.
