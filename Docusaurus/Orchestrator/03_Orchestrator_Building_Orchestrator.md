# Building Orchestrator Components

1.  Start up your Docker daemon and run the following:

    Example:
```
    cd magma/orc8r/cloud/docker
    ./build.py -a
```

If this is your first-time building Orchestrator, this may take a while.
We recommend continuing onto the next step (Terraforming cloud
resources) in a different terminal instance or tab and coming back to
this section when the builds are completed.<br><br/>

## Upload Images

2.  When the ```./build.py -a``` job completes, upload the following images to your image registry:

```
export MAGMA\_TAG=v1.1.0-master
for image in proxy controller prometheus-cache alertmanager-configurer prometheus-configurer
grafana
do
../../../orc8r/tools/docker/publish.sh -r REGISTRY -i ${image} -v ${MAGMA_TAG}
done
```
<br/>
## Build the NMS

3.  After the uploading of the Magma images to your registry, build and publish the Network Management System (NMS) containers:

```
cd magma/symphony/app/fbcnms-projects/magmalte
docker-compose build magmalte
COMPOSE_PROJECT_NAME=magmalte ../../../../orc8r/tools/docker/publish.sh -r REGISTRY -i
magmalte -v ${MAGMA_TAG}
```
