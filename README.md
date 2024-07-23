# oke-bastion - OKE deployment using Github Actions and Bastion session

[![License: UPL](https://img.shields.io/badge/license-UPL-green)](https://img.shields.io/badge/license-UPL-green) [![Quality gate](https://sonarcloud.io/api/project_badges/quality_gate?project=oracle-devrel_oke-bastion)](https://sonarcloud.io/dashboard?id=oracle-devrel_oke-bastion)


## Introduction
Oracle Cloud Infrastructure Container Engine for Kubernetes is a fully-managed, scalable, and highly available service that you can use to deploy your containerized applications to the cloud. 

GitHub Actions workflows are automated processes defined in YAML format that are triggered in response to events in a GitHub repository. 

Workflows can be used to build, test, and deploy applications, automate tasks, and perform various other actions in a GitHub repository.

GitHub Actions workflows can be configured and managed within the repository's .github/workflows directory. Workflows can be triggered by events such as pushes to the repository, pull requests, scheduled intervals, or manual triggers.

## Getting Started
In this example, the deployment process to a private OKE cluster is facilitated by a bastion session. This involves a series of steps that utilize bastion session(SSH port forwarding) and `kubectl` commands to carry out the deployment to the OKE cluster.

1. Install OCI CLI tool
2. Setup SSH - creates the  public and private key used for ssh 
3. Create Bastion Session - creates bastion session using the Bastion service
4. Install kubectl
5. Start SSH Tunnel to OKE Cluster - start port forwarding session to k8s private endpoint
6. Configure OKE Cluster - setup kubeconfig for cluster access
7. Deploy to OKE - using kubectl commands
8. Delete basion session

### Prerequisites

1. Provision an OKE cluster with the Kubernetes API endpoint and worker nodes configured in a private subnet. Please note that the private Kubernetes API endpoint will be utilized for establishing the Bastion port forwarding session.

2. Bastion service created using  OKE VCN as target VCN and OKE node subnet as Target subnet. 

## Required IAM Service Policy
 Please refer to the following links for required IAM service policy to be setup for accesing OKE cluster.

[Policy Configuration for Cluster Creation and Deployment](https://docs.oracle.com/en-us/iaas/Content/ContEng/Concepts/contengpolicyconfig.htm#Policy_Configuration_for_Cluster_Creation_and_Deployment).

[Common Policies](https://docs.oracle.com/en-us/iaas/Content/Identity/Concepts/commonpolicies.htm#top).


## Configuration

1. __Github Repository Secrets & Variables__ -  Please ensure that the following secrets are properly configured with the relevant values according to your specific setup. You can set up secrets by navigating from your repository -> Settings --> Secrets and Variables --> Actions
    e.g
    ```
    BASTION_HOST - host.bastion.us-ashburn-1.oci.oraclecloud.com
    BASTION_OCID - ocid1.bastionsession.oc1.iad._______tg4xoxcetwov7p6nwsfhq5drxyqoize7x35hdboq
    OCI_AUTH_TOKEN - TLPfxey6q-L>wAxxxd3d
    OCI_CLI_FINGERPRINT - 41:4c:da:51:fe:26:7d:2a:e7:dd:83:ba:7d:49:8d:04
    OCI_CLI_KEY_CONTENT - Copy the private key file conetnt here
    OCI_CLI_REGION - us-ashburn-1
    OCI_CLI_TENANCY - ocid1.tenancy.oc1..aa____plkmid2untpzjcxrmbv4nowe74yb4lr6idtckwo4wyf7jh23be4q
    OCI_CLI_USER - ocid1.user.oc1..aaa____lh7itlkx5ersopnkybww3rt3gymelaxftmofky6it6mtjmcz6w4q
    ```
![Repo Secrets](./images/repo_secrets.png)
![Repo Variables](./images/repo_variables.png)

2. __OKE Cluster__
![Repo Secrets](./images/oke.png)

3. __Bastion__
![Repo Secrets](./images/bastion.png)

## Deploy and Run
To incorporate the workflow file (ci.yaml) into your repository and tailor it to your requirements, follow these steps:

1. Ensure the ci.yaml file is included in `.github/workflows` directory. 

2. Open the ci.yaml file and make the necessary customizations based on your specific needs. This may involve configuring build steps, defining tests, specifying deployment actions, or any other relevant adjustments.

3. Save the changes to the ci.yaml file.

4. Commit and push the modified ci.yaml file to your repository.

Once the changes are pushed, the workflow will be automatically triggered whenever code is pushed to the repository. This workflow acts as a framework for executing continuous integration tasks and can be further refined to suit your development processes and requirements.

## Test
You can follow the steps below to test the deployment from the workflow run. This involves accessing the OKE cluster locally through an SSH port-forwarding session and verifying the deployment using `kubectl` commands.

### Step 1: Configure OCI-CLI

On your local machine terminal, make sure oci-cli is installed using: 

`oci -v`

If not, follow the below link to install and setup OCI-CLI.

`https://docs.cloud.oracle.com/en-us/iaas/Content/API/SDKDocs/cliinstall.htm`

### Step 2: Create Bastion session

Create a port forwarding bastion session using the Bastion service created previously. Copy the SSH Command and start the SSH tunnel. Please keep this window open. 

__Example:__
`ssh -i <privateKey> -N -L <localPort>:10.0.0.13:6443 -p 22 ocid1.bastionsession.oc1.ca-toronto-1.________amaaaaaantxkdlyawqe2________vap5n3exve76cpt3slm3w7spvyegyuqv2aar2flq@host.bastion.ca-toronto-1.oci.oraclecloud.com`

### Step 3: Access Cluster & Verify deployment
Please use another terminal window and follwo the steps given below.

Please go to the cluster details page and click on "Access Cluster" button, and follwo the steps mentioned in the "Local Access" section:

This will setup the cluster access using kubeconfig file. While the SSH tunnel is to the private endpoint we can now test the deployment using the following commands.
````
kubectl get deployment -n nginx
kubectl get pods -n nginx
````

## Notes/Issues

* Ensure all the necessary IAM policies are in place to create and manage the OKE cluster successfully.
* Verify that the Bastion service is created and accurately updated with the appropriate CIDR block allowlist.

## URLs
* Nothing at this time

## Contributing
This project is open source.  Please submit your contributions by forking this repository and submitting a pull request!  Oracle appreciates any contributions that are made by the open source community.

## License
Copyright (c) 2024 Oracle and/or its affiliates.

Licensed under the Universal Permissive License (UPL), Version 1.0.

See [LICENSE](LICENSE) for more details.

ORACLE AND ITS AFFILIATES DO NOT PROVIDE ANY WARRANTY WHATSOEVER, EXPRESS OR IMPLIED, FOR ANY SOFTWARE, MATERIAL OR CONTENT OF ANY KIND CONTAINED OR PRODUCED WITHIN THIS REPOSITORY, AND IN PARTICULAR SPECIFICALLY DISCLAIM ANY AND ALL IMPLIED WARRANTIES OF TITLE, NON-INFRINGEMENT, MERCHANTABILITY, AND FITNESS FOR A PARTICULAR PURPOSE.  FURTHERMORE, ORACLE AND ITS AFFILIATES DO NOT REPRESENT THAT ANY CUSTOMARY SECURITY REVIEW HAS BEEN PERFORMED WITH RESPECT TO ANY SOFTWARE, MATERIAL OR CONTENT CONTAINED OR PRODUCED WITHIN THIS REPOSITORY. IN ADDITION, AND WITHOUT LIMITING THE FOREGOING, THIRD PARTIES MAY HAVE POSTED SOFTWARE, MATERIAL OR CONTENT TO THIS REPOSITORY WITHOUT ANY REVIEW. USE AT YOUR OWN RISK. 