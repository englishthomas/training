## Building Federation Gateway Components


### When is an FGW Required?

Deploy a Federation Gateway when the Mobile Operators Core Network needs to use a Carriers core service components;

   - **MSC** Mobile Switching Center

   - **HSS** Home Subscriber Service; PDN, APN, QoS Profile, Access Restrictions

   - **PCRF** Policy Control and Charging Rules Function; Policy control decision-making

   - **OCS** Online Charging System <br><br/>
    

### Building the Components

1.  Start up your Docker daemon, cd to where you\'ve cloned Magma, and     run:

```
cd magma/fgw/gateway/docker
docker-compose build --parallel
```

If this is your first time building the FGW, this may take a while.


2.  When the docker-compose completes, upload the following images to your image registry:

```
../../../orc8r/tools/docker/publish.sh -r <REGISTRY> -i gateway_python
../../../orc8r/tools/docker/publish.sh -r <REGISTRY> -i gateway_go
../../../orc8r/tools/docker/publish.sh -r <REGISTRY> -i gateway_radius
```
<br><br/>
