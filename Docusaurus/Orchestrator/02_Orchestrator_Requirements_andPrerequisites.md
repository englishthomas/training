# Orchestrator Requirements

The prerequisites and recommended hardware requirements to deploy
Orchestrator (ORC8r) are below. If you have deployed a Magma Access
Gateway (AGW) some of the prerequisites may have already been completed.

1.  Create an **AWS Account,** access key and a secret key.
    
    To create AWS User credentials with admin permissions:
    - Create Root account
    - Create an Admin User account
    - Obtain an \<access key ID\> for an administrator account
    - Obtain a \<secret key\> for an administrator account <br><br/>


2.  Have the following **Developer Tools** installed prior to proceeding:

    -   [[Homebrew]](https://brew.sh/) *only* for MacOS users, see the [[pyenv installation
        instructions]](https://github.com/pyenv/pyenv#installation)

    -   [[Docker]](https://www.docker.com/)  Image Depository to deploy Magma containers

    -   [[Helm]](https://helm.sh/) 2.0 manages Kubernetes applications; define, install, and upgrade

    -   [[Kubectl]](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
        kubernetes CLI tool allows you to run commands against Kubernetes clusters

    -   [[Terraform]{.underline}](https://www.terraform.io/downloads.html) manages the Orchestrator cloud deployment

    -   [[GitHub]](https://help.github.com/en/github/getting-started-with-github/set-up-git)
        Setup a GitHub Account and install Git to use command line

    -   [[AWS CLi]](https://aws.amazon.com/cli/) Amazon Web Services command line interface

Replace '''brew''' with your OS-appropriate package manager as necessary, or see the [pyenv installation
instructions](https://github.com/pyenv/pyenv#installation).

```
brew install pyenv\
# Replace .zshrc with your appropriate shell RC file\
# IMPORTANT: Use .bash\_profile, not .bashrc for bash\
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n eval "$(pyenv init -)"\nfi' >> ~/.zshrc
exec $SHELL
pyenv install 3.7.3
pyenv global 3.7.3
```
```
pip3 install ansible fabric3 jsonpickle requests PyYAML\
vagrant plugin install vagrant-vbguest
```<br><br/>

If you are on MacOS, you should start Docker for Mac and increase the memory allocation for the Docker engine to at least 4GB  in Preferences -> Advanced.

![](media/image3.png){width="4.619792213473316in"
> height="4.286139545056868in"}
<br><br/>

## Build/Deploy Tooling

We support building the AGW and Orchestrator on MacOS and Linux host
operating systems. Doing so on a Windows environment should be
possible but has not been tested. You may prefer to use a Linux
virtual machine if you are on a Windows host.

Follow the previous section on Developer Tools, then install
additional prerequisite tools (replace brew with your OS-appropriate
package manager as necessary):

```
brew install aws-iam-authenticator kubernetes-cli kubernetes-helm terraform
pip3 install awscli
aws configure
```

Provide the access key ID and secret key for an administrator user in
AWS (do NOT use the root user) when prompted by aws configure. Skip
this step if you will use something else for managing AWS credentials. <br><br/>

3.  Clone the Magma Dev package from GitHub

    -   **Magma Dev Package** - clone from the [[Facebook Github]](https://github.com/facebookincubator/magma/) repository
        &#x1F4D9; <br><br/>

4.  Obtain the following SSL Certificates, Secret Keys, Private Keys and Domain if you already have or will be using self-signed.
    ***The following are created during installation***.

    a.  ***SSL Certificates*** - the public SSL certificate for your Orchestrator domain, with CN=\*.yourdomain.com

    b.  ***Private Key*** - The private key which corresponds to the above SSL certificate

    c.  The **rootCA certificate** - which verifies your SSL certificate

    d.  ***Application Certificates*** and Keys -- these can be created during ORC8r installation

    e.  ***Environment Secrets*** - these can be created during ORC8r installation

    f.  **A Registered Domain** -- this is for the Orchestrator endpoints yourdomain.com, a '''domain/subdomain''' which you've
        chosen to host Orchestrator on.
