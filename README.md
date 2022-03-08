# Ansible Playbook For IBM ROKS Cluster on IBM VPC Gen2 Infrastructure

This repository contains ansible playbook which helps to perform the following activities
 * Provision IBM ROKS cluster on VPC Gen2 
 * Get cluster info
 * Destroy the provisioned resources using above playbook 

# Provision IBM ROKS cluster on VPC Gen2 

The playbook create.yml provisions IBM ROKS cluster on VPC Gen2 along with the required resources.
Here are the resources which are provisioned through the playbook
* Virtual Private Cloud (VPC) Gen2
* Subnets on the given zone to associate with VPC
* IBM VPC Public Gateway
* Object Stroage instance (for the IBM ROKS internal image registry)
* IBM ROKS cluster  
* Retrieve the Cluster information

The cluster provisioning may take longer time (around 90 mins or more, depends on network, etc). The create playbook gracefully exits when one of the Worker node is in Ready state and displays the message on the terminal "Ingress took longer than expected, but the cluster is Ready:  {{ cluster_name}}" since Ingress readiness might be delayed.

User can run the cluster_info playbook, to retrieve the cluster details at any point of time.

## Configuration Parameters

The following parameters can be set by the user in vars.yml

* `region:`: Region where the resource being provisioned
* `zone`: Zone where the subnet is being provisioned
* `rg_name`: Name of the Resource Group in which the resources to be provisioned
* `name_prefix:`:  Prefix for the resource names while provisioning
* `flavor`: Worker node configuration
* `worker_count`: Number of worker nodes. Minimum 2 is required
* `entitlement`: Entitlement info for openshift cluster, specify "cloud_pak" if using cloud pak entitlement for ROKS
* `kube_version`: Please refer the  to list the kubeversions supported by ibmcloud - `ibmcloud ks versions`
* `total_ipv4_address_count`: 256
* `objstorage_service`:  IBM Object storage serivce name: cloud-object-storage
* `objstorage_plan`: Specify the Object Storage service plan e.g. lite, standard

* `cluster_name`: Provide an empty string '' to generate the default cluster_name by script which looks like {{name_prefix }}-cluster
* `objstorage_name`: Provide an empty string '' to generate the default object storage name by script which looks like {{name_prefix }}-cos and provisions the same. To use an existing instance name,  provide the name.


## Prerequisites

1. Install [Python3]

2. [RedHat Ansible] 2.9+

    ```
    pip install "ansible>=2.9.2"
    ```
    
## Install

1. Download and Install collection

    ```
    ansible-galaxy collection install ibm.cloudcollection
    ```
## Running

### Set API Key and Region

1. [Obtain an IBM Cloud API key].

2. Export your API key to the `IC_API_KEY` environment variable:

    ```
    export IC_API_KEY=<YOUR_API_KEY_HERE>
    ```

    Note: Modules also support the 'ibmcloud_api_key' parameter, but it is
    recommended to only use this when encrypting your API key value.

### Create

1. To create IBM ROKS  cluster, run the
   'create' playbook:

    ```
    ansible-playbook create.yml
    ```
### Get Cluster info

1. To create IBM ROKS  cluster, run the
   'cluster_info' playbook:

    ```
    ansible-playbook cluster_info.yml
    ```    

### Destroy

1. To destroy cluster run the 'destroy' playbook:

    ```
    ansible-playbook destroy.yml
    ```
[Obtain an IBM Cloud API key]:https://cloud.ibm.com/docs/iam?topic=iam-userapikey
[Python3]: https://www.python.org/downloads/

# Known issues
Destory playbook may come out while performing vpc deletion task which is last one with an error : "The VPC is in use and cannot be deleted".  This is due to the Public Gateway is not getting detached from VPC through the script.  Will be fixing in later version, till then please detach and delete manually. 
