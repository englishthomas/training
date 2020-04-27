## Installing Orchestrator

### SSL Certificates Prerequisites

The following Certificates and Private Keys are required before proceeding:

- The **Public SSL certificate** for your Orchestrator domain, with CN=\*.yourdomain.com.\
  This can be an SSL certificate chain, but it must be in one file.
      
- The **Private Key** which corresponds to the above SSL certificate
      
- The **rootCA certificate** which verifies your SSL certificate. <br><br/>

Certificates will be uploaded to AWS Secretsmanager, they can be deleted locally afterwards.

1.  Create a local directory to hold the certificates that you will use
    for your Orchestrator deployment.

```
mkdir -p \~/secrets/certs
```

2.  Rename your *public SSL certificate* to ```controller.crt```

3.  Rename your SSL certificate\'s *private key* to ```controller.key```

4.  Rename your SSL certificate\'s *root CA certificate* to ```rootCA.pem```

5.  Place these 3 files under the directory you created above ``` \~/secrets/certs ```
<br/>

If you are not worried about a browser warning, you can also self-sign these certs:

```
cd \~/secrets/certs
MAGMA\_ROOT/orc8r/cloud/deploy/scripts/self\_sign\_certs.sh yourdomain.com
```

Regardless of if you have self-signed your certs or used existing ones,
run one more script here:

```
cd \~/secrets/certs
MAGMA\_ROOT/orc8r/cloud/deploy/scripts/create\_application\_certs.sh yourdomain.com
```

### Infrastructure and Application Installation

Create a new root Terraform module in a location of your choice by creating a new ```main.tf file```. Follow the example Terraform root module at ```orc8r/cloud/deploy/terraform/orc8r-helm-aws/examples/basic```  and **override the following parameters**:

-   ```deploy\_nms```: this should be false for now

-   ```nms\_db\_password```

-   ```orc8r\_db\_password```

-   ```orc8r\_domain\_name```

-   ```docker\_registry```

-   ```docker\_user```

-   ```docker\_pass```

-   ```helm\_repo```

-   ```helm\_user```

-   ```helm\_pass```

-   ```seed\_certs\_dir```: set this to ```"~/secrets/certs\"``` , or the directory you generated your certificates into

-   ```orc8r\_tag```: this should be set to the tag that you used when you pushed the containers you built earlier

Verify the ```source``` variables for the module definitions point to
```github.com/facebookincubator/magma//orc8r/cloud/deploy/terraform/\<module\>```. Adjust any other parameters as needed. Check the READMEs for the relevant Terraform modules to see additional variables that can be set.<br><br/>

### **Initial Infrastructure Terraform**

The 2 Terraform modules are organized so that ```orc8r-aws``` contains all the
resource definitions for the cloud infrastructure that you'll need to
run Orchestrator and ```orc8r-helm-aws``` contains all of the application
components behind Orchestrator. On the very first installation, you'll
have to ```terraform apply``` the infrastructure before the application. On
later changes to your Terraform root module, you can make all changes at
once with a single ```terraform apply```.

With your root module set up, simply run:

```
$ terraform apply -target=module.orc8r

# Note: actual resource count will depend on your root module variables
Apply complete! Resources: 70 added, 0 changed, 0 destroyed.
```

The ```terraform apply``` will create a Kubeconfig file in the same directory as your root Terraform module. To get access to the k8s cluster, either set your KUBECONFIG environment variable to point to this file or pull this file into your default kubeconfig file at ```~/.kube/config```.
See 
[[https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/]](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/) for more details.
<br><br/>

### Initial Application Terraform

Next step is the first application installation. From your same root Terraform module, run:

```
$ terraform apply -target=module.orc8r-app.null_resource.orc8r_seed_secrets

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

$ terraform apply
Apply complete! Resources: 16 added, 0 changed, 0 destroyed.
```


### Creating an NMS Admin User

Before the deployment of the NMS, we need to create an admin user for the NMS backend server inside Orchestrator.

NOTE: In all the below ```kubectl``` commands, use the ```-n``` flag or ```kubens``` to select the appropriate k8s namespace (by default this is ```orc8r```).

```
export CNTLR_POD=$(kubectl get pod -l app.kubernetes.io/component=controller -o
jsonpath='{.items[0].metadata.name}')

kubectl exec -it \${CNTLR\_POD} bash

# The following commands are to be run inside the pod
(pod)$ cd /var/opt/magma/bin
(pod)$ envdir /var/opt/magma/envdir ./accessc add-admin -cert admin_operator admin_operator
(pod)$ openssl pkcs12 -export -out admin_operator.pfx -inkey admin_operator.key.pem -in
admin_operator.pem

Enter Export Password:
Verifying - Enter Export Password:

(pod)$ exit
```

Now on your host, copy the client certificates for the admin operator we just created into the secrets directory:

```
cd ~/secrets/certs
for certfile in admin_operator.pem admin_operator.key.pem admin_operator.pfx
do
kubectl cp ${CNTLR\_POD}:/var/opt/magma/bin/${certfile} ./${certfile}
done
```

```admin_operator.pem``` and ```admin_operator.key.pem``` are the files that NMS will use to authenticate itself with the Orchestrator
API. ```admin_operator.pfx``` is for you to add to your keychain if you'd like to use the Orchestrator REST API directly (on MacOS, double-click this file and add it to your keychain).<br><br/>

### Upload Certs to AWS

We can now upload these new certificates to AWS Secretsmanager:

```
$ terraform taint module.orc8r-app.null_resource.orc8r_seed\_secrets
$ terraform apply
Apply complete! Resources: 1 added, 0 changed, 1 destroyed.
```

At this point, you can ```rm -rf ~/secrets``` to remove the certificates from your local disk (we recommend this for security). If you ever need to update your certificates, you can create this local directory again and ```terraform taint``` the ```null_resource``` to re-upload local certificates to Secretsmanager.<br><br/>

### **Final Application Terraform**

### Deploy the NMS

The final application, Terraform for the last time, is to deploy the Network Management System (NMS).

  1.  In your root Terraform module, set the ```deploy_nms``` variable to true now
  2.  Run the Terraform apply command

```
$ terraform apply

Apply complete! Resources: 1 added, 1 changed, 0 destroyed.
```

  3.  Lastly, create an admin user on the NMS:

```
kubectl exec -it \
$(kubectl get pod -l app.kubernetes.io/component=magmalte -o
jsonpath='{.items[0].metadata.name}') --- \ yarn setAdminPassword <admin user email> \<admin user password>
```


### DNS Resolution

EKS has been set up with ```external-dns``` so AWS Route53 will already have the appropriate CNAME records for the relevant subdomains of Orchestrator at this point.

  1.  Configure your DNS records on your managed domain name to use the Route53 nameservers in order to resolve these subdomains.

The example terraform root module has an output ```nameservers``` which will list the Route53 nameservers for the hosted zone for Orchestrator. You have probably already noticed some output with every ```terraform apply``` that looks like:

```diff
-**Outputs:**```
```
nameservers = [
"ns-xxxx.awsdns-yy.org",
"ns-xxxx.awsdns-yy.co.uk",
"ns-xxxx.awsdns-yy.com",
"ns-xxxx.awsdns-yy.net",
]
```

  2.  For each of the following subdomains, add an NS Record to the above nameservers on your domain registrar:
    
    - nms
    - controller
    - bootstrapper-controller
    - api

If you chose a subdomain prefix for your Orchestrator domain name in your root Terraform module, you'll have to append that subdomain prefix to your NS Record names. For example, if you chose ```orc8r.yourdomain.com``` for your Route53 zone, you'll have to add NS
Records for ```nms.orc8r```, ```api.orc8r```, and so on.<br><br/>

### Upgrading the Deployment

You can upgrade the deployment simply by changing the ```orc8r_tag``` variable in your root Terraform module to the new
software version that you want to run and running ```terraform apply```. Changes to the Terraform modules between releases may require some updates to your root Terraform module - these will be communicated in release notes.
