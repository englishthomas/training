## eNodeB Configuration


### Prerequisites


Follow the instructions in [Deploying
Orchestrator](https://facebookincubator.github.io/magma/docs/orc8r/deploying) for a successful installation of Orchestrator and the instructions in [AGW
Configuration](https://facebookincubator.github.io/magma/docs/lte/config_agw) to provision and configure your Access Gateway (AGW\_HOST).<br><br/>

### Connect the S1 Interface

Connect your eNodeB to the ```eth1``` interface of Magma AGW. Magma uses ```eth1``` as the default ```S1``` interface. If you have more than one eNodeB, use an L2 switch to connect all S1 interfaces. For debugging purposes, you may find it useful to perform the following:

   1.  Configure a managed L2 switch (e.g. [NETGEAR](https://www.amazon.com/NETGEAR-GS108T-200NAS-GS108Tv2-Lifetime Protection/dp/B07PS6Z162/) to mirror port X and port Y to port Z.

   2.  Connect port X of that switch to the eth1 interface on your AGW.

   3.  Connect the WAN interface on your eNodeB to port Y on the switch.

   4.  Connect your host to port Z on the switch.

This will allow you to do live packet captures with [Wireshark](https://www.wireshark.org/) from your host to
debug the S1 interface between the eNodeB and the AGW (filter for SCTP).<br><br/>

### Automatic Configuration

To handle automatic configuration of eNodeB devices on your network, Magma uses the ```enodebd``` service. The ```enodebd``` service is responsible for handling the O&M interface between Magma and any connected eNodeB. The ```enodebd``` service can be disabled if you configure your eNodeB devices manually.

Magma officially supports auto-configuration of the following **devices**:

-   Baicells Nova-243 Outdoor FDD/TDD eNodeB

-   Firmware Version: BaiBS\_RTS\_3.1.6

-   Baicells mBS1100 LTE-TDD Base Station

-   Firmware Version: BaiStation\_V100R001C00B110SPC003

-   Baicells Neutrino-244 ID FDD/TDD enodeB<br><br/>


Magma supports the following **management protocols**:

-   TR-069 (CWMP)<br><br/>


Magma supports configuration of the following **data models***:*

-   TR-196 data model

-   TR-181 data model<br><br/>


The Magma team plans to add support for more devices and management protocols.

### Baicells -- Setting the Management Server URL

Use the eNodeB's management interface to set the management server URL to ```baiomc.cloudapp.net:48080```. Magma uses DNS hijacking to point the eNodeB to the configuration server being run by enodebd. ```baiomc.cloudapp.net:48080``` will point to ```192.88.99.142```, the IP address that the TR-069 ACS is being hosted on.<br><br/>


## Provisioning Your eNodeB on the NMS  To provision an eNodeB:

1.  Obtain the serial number of your eNodeB, you\'ll need it to registe the device.

2.  Login to the Magma Network Management System (NMS)

3.  From the left sidebar, select "eNodeB Devices"

4.  From the upper right side of the screen, click "Add EnodeB"

5.  Configure the RAN parameters as necessary.
    Note that fields left blank will be inherited from either the network or gateway LTE parameters:

6.  Return to the "Gateways" page and edit the LTE configuration of the AGW. Enter the serial number of the enodeB you just
    provisioned into the "Registered eNodeBs" field. When complete click Save.

![Screen Shot](media/image1.png){width="6.3550885826771655in"
height="3.5935673665791774in"}
*Configure eNodeB*

![Screen Shot](media/image4.png){width="6.677083333333333in"
height="4.169400699912511in"}
*Gateway, LTE Configuration of the AGW*<br><br/>


### Basic Troubleshooting

After connecting the eNodeB(s) to the Access Gateway through the ```eth1``` interface, you may want to check a few things if
auto-configuration is not working.

Magma will be running a DHCP server to assign an IP address to your connected eNodeB. Check if an IP address gets assigned to your eNodeB by either checking the eNodeB UI or monitoring the ```dnsd``` service.

```
journalctl -u magma@dnsd -f
# Check **for** a similar log
# DHCPDISCOVER(eth1) 48:bf:74:07:68:ee
# DHCPOFFER(eth1) 10.0.2.246 48:bf:74:07:68:ee
# DHCPREQUEST(eth1) 10.0.2.246 48:bf:74:07:68:ee
# DHCPACK(eth1) 10.0.2.246 48:bf:74:07:68:ee
```

### Status of eNodeB's

Use the ```enodebd_cli.py``` tool to check the basic status of eNodeB(s). It also allows for querying the value of parameters, setting them, and sending reboot requests to the eNodeB.

The following example returns the status of all connected eNodeBs.

```
enodebd_cli.py get_all_status
# --- eNodeB Serial: 120200002618AGP0001 ---
# IP Address\.........10.0.2.246
# eNodeB connected .............1
# eNodeB Configured ............1
# Opstate Enabled ..............1
# RF TX on .....................1
# RF TX desired ................1
# GPS Connected ................0
# PTP Connected ................0
# MME Connected ................1
# GPS Longitude ................113.902069
# GPS Latitude .................22.932018
# FSM State .....................Completed provisioning eNB. Awaiting new Inform.
```
<br/>

### Monitor the Status of eNodeB's

It may take time for the eNodeB to start transmitting because ```enodebd``` will reboot the eNodeB to apply new configurations.
Monitor the progress of ```enodebd``` using the following command:

```
**journalctl** -u magma@enodebd -f
# Check for a similar log
# INFO:root:Successfully configured CPE parameters!
```
<br/>

### Manual eNodeB Configuration

Manual configuration of connected eNodeB(s) is always possible. Magma was tested with multiple Airspan eNodeB models configured through
NetSpan management software. When manually configuring eNodeBs, make sure the eNodeB configuration matches that of the Magma cellular
configuration. Pay special attention to the configuration of ```PLMN```, ```EARFCN``` and ```TAC```.<br><br/>


### Basic Troubleshooting

When manually configuring your eNodeB, use the manufacturers tools or interfaces to monitor and troubleshoot the eNodeB configuration.

You can also listen to the  S1  interface traffic and validate a proper ```S1``` setup and handshake. Below are the ```SCTP``` packets exchanged between the eNodeB and MME.

```
Source Destination Protocol Length Info
10.0.2.246 10.0.2.1 SCTP 66 INIT
10.0.2.1 10.0.2.246 SCTP 298 INIT_ACK
10.0.2.246 10.0.2.1 SCTP 278 COOKIE_ECHO
10.0.2.1 10.0.2.246 SCTP 60 COOKIE_ACK
10.0.2.246 10.0.2.1 S1AP 106 S1SetupRequest
10.0.2.1 10.0.2.246 SCTP 62 SACK
10.0.2.1 10.0.2.246 S1AP 90 S1SetupResponse
10.0.2.246 10.0.2.1 SCTP 62 SACK
10.0.2.246 10.0.2.1 SCTP 66 HEARTBEAT
10.0.2.1 10.0.2.246 SCTP 66 HEARTBEAT\_ACK
```
<br/>

## Adding Subscribers

Once your eNodeB starts transmitting, UEs may attempt to attach to your network. The AGW will reject these attach requests due to authentication failure until the corresponding IMSI is added to the subscriber database.

1.  Login to the Network Management System (NMS)

2.  Select "Subscribers" from the left sidebar

3.  From the upper right side of the screen, click "Add Subscriber"

4.  Enter the SIM secrets either in hex or base64-encoded binary.

Subscriber information will eventually propagate to the AGW. Verify Subscribers using the CLI command ```subscriber_cli.py``` list.

![Screen Shot](media/image2.png){width="5.267442038495188in"
height="4.0715376202974625in"}
*Adding Subscribers*

<br/>

### Validating UE Connectivity

Validating UE connectivity can be performed from the UE side, MME side, or by listening to traffic on the ```S1``` interface. Below is a typical UE attach procedure as captured on the ```S1``` interface.

Verify Subscribers using the CLI command ```subscriber\_cli.py``` list

```
Source Destination Protocol Info
10.0.2.246 10.0.2.1 S1AP/NAS-EPS InitialUEMessage, Attach request, PDNconnectivity request
10.0.2.1 10.0.2.246 S1AP/NAS-EPS DownlinkNASTransport, Identity request
10.0.2.246 10.0.2.1 S1AP/NAS-EPS UplinkNASTransport, Identity response
10.0.2.1 10.0.2.246 S1AP/NAS-EPS DownlinkNASTransport, Authentication request
10.0.2.246 10.0.2.1 S1AP/NAS-EPS UplinkNASTransport, Authentication response
10.0.2.1 10.0.2.246 S1AP/NAS-EPS DownlinkNASTransport, Security mode command
10.0.2.246 10.0.2.1 S1AP/NAS-EPS UplinkNASTransport, Security mode complete
10.0.2.1 10.0.2.246 S1AP/NAS-EPS DownlinkNASTransport, ESM information request
10.0.2.246 10.0.2.1 S1AP/NAS-EPS UplinkNASTransport, ESM information response
10.0.2.1 10.0.2.246 S1AP/NAS-EPS InitialContextSetupRequest, Attach accept, Activate **default** EPS bearer context request
10.0.2.246 10.0.2.1 S1AP UECapabilityInfoIndication, UECapabilityInformation
10.0.2.246 10.0.2.1 S1AP InitialContextSetupResponse
10.0.2.246 10.0.2.1 S1AP/NAS-EPS UplinkNASTransport, Attach complete, Activate **default** EPS bearer context accept
10.0.2.1 10.0.2.246 S1AP/NAS-EPS DownlinkNASTransport, EMM information
```
