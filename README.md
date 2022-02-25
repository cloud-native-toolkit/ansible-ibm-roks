# Ansible Playbook For IBM ROKS Cluster on IBM VPC Gen2 Infrastructure

This repository contains ansible playbook which helps to perform the following activities
    - Provision IBM ROKS cluster on VPC Gen2 
    - Get cluster info
    - Destroy the provisioned resources using above playbook 


# Provision IBM ROKS cluster on VPC Gen2 

The playbook create.yml provisions IBM ROKS cluster on VPC Gen2 along with the required resources.
Here are the resources which are provisioned through the playbook
    - Virtual Private Cloud (VPC) Gen2
    - Subnets on the given zone to associate with VPC
    - IBM VPC Public Gateway
    - Object Stroage instance which is required for the IBM ROKS internal image registry
    - IBM ROKS cluster with the IBM Cloud pak entitlement 
    - Retrieve the Cluster information

The cluster provisioning may take longer time (around 90 mins or more, depends on network, etc). The create playbook gracefully exits once one of the Workder node is in Ready state, and displays the message on the terminal as "Ingress took longer than expected, but the cluster is Ready:  {{ cluster_name}}"

User can run the cluster_info playbook, to retrieve the cluster details later point of time.

## Configuration Parameters

The following parameters can be set by the user in vars.yml

* `region:`: Region where the resource being provisioned
* `zone`: Zone where the subnet is being provisioned
* `rg_name`: Name of the Resource Group in which the resources to be provisioned
* `name_prefix:`:  used as prefix for the resource names while provisioning
* `flavor`: machine type.
* `worker_count`: number of worker nodes. Minimum 2 is required
* `entitlement`: Entitlement info for openshift cluster
* `kube_version`: Please refer the  to list the kubeversions supported by ibmcloud - `ibmcloud ks versions`
* `total_ipv4_address_count`: 256
* `objstorage_service`:  For IBM Object storage serivce name: cloud-object-storage
* `objstorage_plan`: Specify the Object Storage service plan lite, standard

* `cluster_name`: Provide an empty string '' to generate the default cluster_name by script which looks like {{name_prefix }}-cluster
* `objstorage_name`: Provide an empty string '' to generate the default object storage name by script which looks like {{name_prefix }}-cos and provisions the same. To use an existing instance name,  provide the name.



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

# Known issues
Destory playbook may come out while performing vpc deletion task which is last one with an error : "The VPC is in use and cannot be deleted".  This is due to the Public Gateway is not getting detached from VPC through the script.  Will be fixing in later version, till then please detach and delete manually. 