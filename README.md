# Terraform Automation on Oracle Cloud : Autoscale x Container Instances (private network) without service interruption (due to the use of a Loab Balancer (public network) to access them) but at start you set the maximum number of CI so you can create them in an INACTIVE state and activate them when needed (except one that is always running).

This project has been designed to run with Oracle OCI Stacks Resource Manager. Nevertheless you can use it on another place but you have to uncomment some security variables needed outside of OCI Stacks Resource Manager. Look at these 3 files (variables.tf, provider.tf and main.tf).

## Prerequisites

Before Starting you need to create a Secret in Oracle OCI Vault for being able to connect to the OCI Registry where your docker images for Container Instances are stored.

1) Create the secret. It is a JSON String like below : 

{
"username": "charles-foster-kane", --> Your Cloud User
"password": "rosebud" --> Your Auth Token
} 

2) Create a Dynamic Group for Container Instance in your compratment
   
"Any {resource.type = 'computecontainerinstance', resource.compartment.id = 'ocid1.compartment.oc1..aaaaaaaax6vcsmyeticnpfvvixqk5lyqgihqbjvhcxfakuruoyv4dr4utq7q'}"

3) Create a Policy to allow Container Instance read Secret

allow dynamic-group <dynamic-group-name> to read secret-bundles in tenancy

## Create the Stack

This project consider that your network configuration is done. It means : 
- VCN is created with a private subnet and a public subnet
- The security list of the public subnet has an ingress rule for the load balancer port
- The security list of the private subnet has an ingress rule for the container instances port

You can look at the variables and see : 
- Some of the variables have default values than can be updated by yourself or not (using the env-var-template.ps1 or with Stack Ressource Manager Variables)
- Some other variables have no default value and are mandatory so you must know them.
  - compartment_ocid
  - region
  - private_subnet_ocid
  - public_subnet_ocid
  - ci_image_url (Image of CI always ACTIVE)
  - ci_image_url_bis (Image of CIs that could be ACTIVE or INACTIVE)
  - ci_registry_secret (ocid)

## Activate all CI

Very simple just follow do that :  Update ci_state_bis from INACTIVE to ACTIVE . Do a terraform apply and check that all CIs are started now. Use the Load Balancer Ip address to check that all is ok.

## Desactivate all CI except One

Very simple just follow do that :  Update ci_state_bis from ACTIVE to INACTIVE . Do a terraform apply and check that only one CI is started now. Use the Load Balancer Ip address to check that all is ok.
