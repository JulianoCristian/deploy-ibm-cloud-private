Summary
=======

This Terraform sample will perform a simple IBM Cloud Private (ICP) deployment.
By default, it will install the Community Edition, but you can also configure
it to install the Enterprise Edition as well. It is currently setup to deploy
an ICP master node (also serves as the boot, proxy, and management node) and a
user-configurable number of ICP worker nodes. This sample does not supersede
the official ICP deployment instructions (as of version 2.1.0), it merely serves
as a simple way to provision an ICP cluster within your infrastructure.

Assumptions
-----------
* It is assumed that the reader is already familiar with IBM Cloud Private;
  if you are not, please refer to the `ICP Community
  <https://www.ibm.com/developerworks/community/wikis/home?lang=en#!/wiki/W1559b1be149d_43b0_881e_9783f38faaff>`_
* It is assumed that the reader is already familiar with Terraform; if you
  are not, there are a lot of great articles available on the web

Prerequisites
-------------
* You have `downloaded Terraform
  <https://www.terraform.io/downloads.html>`_ and installed it on your workstation
* You have an instance of OpenStack (or PowerVC) running; the OpenStack version
  should be Ocata or newer
* You have a supported OS image loaded within OpenStack with minimally a
  **100 GB root disk**; this will be used as the baseline image for all of the
  ICP nodes

Supported OS versions
---------------------
This Terraform deployment supports the following OS images:
* Ubuntu 16.04
* Red Hat Enterprise Linux 7.3, 7.4, 7.5
  (Ensure that your chosen version of ICP also supports your selected OS)

Additional Redhat image considerations
------------------------------------

For a Red Hat installation, you must ensure that the base image has 
the necessary repositories for the docker prerequisites, or has those 
packages installed. Those packages are:

* docker-ce
  ICP Enterprise Edition users should use the ICP-provided docker tar ball and
  the docker_download_location variable in variables.tf to specify the download url.

  Other ppc64le users should use the following Unicamp repository:
  http://ftp.unicamp.br/pub/ppc64el/rhel/7/docker-ppc64el/

  Other architectures can be obtained from the docker download site: 
  https://www.docker.com/get-docker

* pigz
  Available from the RedHat EPEL repository. See:
  http://fedoraproject.org/wiki/EPEL

* container-selinux and socat.
  Available from the Redhat Extras repository.

Instructions
------------
* Clone (**git clone git@github.com:IBM/deploy-ibm-cloud-private.git**)
  this repository so you have these files on your Terraform workstation
* On your Terraform workstation, generate an SSH key pair to be pushed (via
  Terraform) to all of the nodes; e.g., you can [**ssh-keygen -t rsa**] to
  create this from a Linux or macOS workstation (this will be referenced in
  the subsequent step when you're updating **variables.tf**).
* The default user for your image (configured via variables.tf) should have 
  sudo (root) access in order to perform the ICP installation.
* Edit the contents of **variables.tf** to align with your OpenStack
  (or PowerVC) deployment (make sure you're using an OpenStack flavor with
  sufficient resources; a minimum of 4 vcpus and 16 GB of memory is recommended)
* If you want to install ICP Enterprise Edition, you need to:

  * Place the ICP tar ball in an HTTP(S) accessible location (i.e., so that
    wget can be used to download the file)
  * Update the *icp_architecture*, *icp_edition*, and *icp_download_location*
    variables within the **variables.tf** file to point to appropriate ICP
    architecture, edition, and tar ball.
  * Update the *docker_download_location* variable within the **variables.tf** 
    file to point to appropriate ICP-provided Docker tar ball.
* Run [**terraform apply**] to start the ICP deployment
* Sit back and relax... within about 30-40 minutes, you should be able to
  access your ICP cluster at https://<ICP_MASTER_IP_ADDRESS>:8443
* If you're using this for anything beyond a proof-of-concept, please also take
  the added step of setting the **insecure=false** variable in the **main.tf**
  OpenStack provider, and the OS_CACERT environment variable.
  (https://www.terraform.io/docs/providers/openstack/#ca_certfile)

See [Accessing IBM Cloud Private](/README.md#accessing-ibm-cloud-private) for next steps.
