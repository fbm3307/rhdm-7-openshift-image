# Red Hat Decision Manager 7 OpenShift images

This repository contains all the image descriptors and files necessary to build the RHPAM images.
It also includes the application templates, however they are deprecated in 7.12. Using the Red Hat Business Automation Operator is recommended.


### Repo structure:

- **rhdm-7-openshift-image**
  - [controller](controller): RHDM Controller Container Image descriptor files.
  - [decisioncentral](decisioncentral): Decision Central Container Image descriptor files.
  - [kieserver](kieserver): Execution Server Container Image descriptor files.
  - [quickstarts](quickstarts): Quickstarts used as example on RHDM images.
  - [scripts](scripts): Holds scripts to help this repository maintenance.
  - [templates](templates): Application templates, used to instantiate a new RHDM environment on OpenShift. (Deprecated)  \
    - [docs](templates/docs):  Application templates docs generated by the [gen_template_docs.py](https://github.com/jboss-container-images/jboss-kie-modules/blob/main/tools/gen-template-doc/gen_template_docs.py) script.

Inside each image directory you will find the following files:

 - **container.yaml**: used by OSBS builds.
 - **content_sets.yaml**: define the yum repositories needed to install dependencies for the image.
 - **branch-overrides.yaml**: overrides file which uses the latest stable version for external dependencies.
 - **tag-overrides.yaml**: Used to override the branchs to use the final tags to rebuild released images, for CVE respins.
 - **image.yaml**: the main image descriptor file, here are all the pieces and configuration needed to build an image.


On the root directory you can also find the two files:

 - [example-app-secret-template.yaml](example-app-secret-template.yaml): Contains a https certificate to be used as example where https is required.
 - [rhdm-image-streams.yaml](rhdm712-image-streams.yaml): imagestreams definitions, file used to install the product image streams on OpenShift, this files contains the image stream name and the registry the image will be pulled of.


This repo depends directly on 6 repositories, which are:

 - [cct_module](https://github.com/jboss-openshift/cct_module.git): contains some core modules, like s2i and maven.
 - [jboss-eap-modules](https://github.com/jboss-container-images/jboss-eap-modules.git): contains modules responsible to configure JBoss EAP, like datasources and the base configuration files.
 - [jboss-eap-image](https://github.com/jboss-container-images/jboss-eap-7-image.git): provides the EAP modules, which is used to install the EAP on the target image.
 - [wildfly-cekit-modules](https://github.com/wildfly/wildfly-cekit-modules.git): provides the EAP configuration modules, used to configure the EAP during startup.
 - [rhdm-7-image](https://github.com/jboss-container-images/rhdm-7-image.git): provides the RHDM binaries modules.
 - [jboss-kie-modules](https://github.com/jboss-container-images/jboss-kie-modules): contains all the resources used to configure the RHDM images.


#### Building Images

Notice that, [CEKit](https://cekit.io/) 3.8 or higher is required to build RHDM images.

To build the images for local testing there is a [Makefile](./Makefile) which will do all the hard work for you.
With this Makefile you can:

- Build and test all images with only one command:

     ```bash
     make
     ```

     If there's no need to run the tests just set the *ignore_test* env to true, e.g.:

     ```bash
     make ignore_test=true
     ```

- Test all images with only one command, no build triggered, set the *ignore_build* env to true, e.g.:

     ```bash
     make ignore_build=true
     ```

- Build images individually, by default it will build and test each image

     ```bash
     make image image_name=decisioncentral
     make image image_name=controller
     make image image_name=kieserver
     ```
  
     We can ignore the build or the tests while interacting with a specific image as well, to build only:

     ```bash
     make ignore_test=true image_name={image_name}
     ```

     Or to test only:

     ```bash
     make ignore_build=true image_name={image_name}
     ```

- List all available Images:
    To list all available images in the repository do:

    ```bash
    make list-images
    ```

- Building the images with a different overrides file:
    This can be done by simpling provide the overrides file name that should reside on the same directory than
    the target image.
    ```bash
    make OVERRIDES=my-overrides-file-name.yaml
    ```

#### Generate Application Templates docs

The scripts needed to generate the adocs can be found [here](https://github.com/jboss-container-images/jboss-kie-modules/tree/main/tools/gen-template-doc).
To generate the adocs using the current branch use:

```bash
make generate_adocs
```

It will use the `main` branch from *jboss-kie-modules* using the current branch from RHDM git repository.

To generate the adocs for, example, 7.12.x branch, first, switch the git branch from RHDM repository then execute the
following command:

```bash
make generate_adocs branch=7.12.x
```

It will use the `7.12.x` branch from *jboss-kie-modules* using the checked out branch on RHDM repository based from 7.12.x.


#### Update versions

Before each release, there is a need to update the product version on each repository that composes the Container
Images.
In this repo you will find the **scripts** directory which containers the `update-version.py` script which helps to
update the version to the next release interation smoothly.

This script requires python 3.


See its usage:
```bash
$ python scripts/update-version.py --help
usage: update-version.py [-h] [-v T_VERSION] [--confirm]
RHDM Version Manager
optional arguments:
  -h, --help    show this help message and exit
  -v T_VERSION  update everything to the next version
  --confirm     if not set, script will not update the rhdm modules. (Dry run)
```
There is two options to run it, a dry-run option which will only print for you the changes, useful if you want just to see
how the changes will looks like after the script is executed, and if the chages are correct, the `--confirm` flag
should be used.


##### Found an issue?

Please submit an issue [here](https://issues.jboss.org/projects/RHDM) with the **Cloud** tag or 
send us one email: bsig-cloud@redhat.com.
