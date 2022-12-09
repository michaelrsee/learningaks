# learningaks

# Intent
This repo is to show basic learning path architectures for working with Azure Kubernetes Service.  

# Outline
Different levels of deployment, going from simple to more complex.  

1. Vanilla marketplace deployment scenario  
    a. Deployment also creates and connects an Azure Container Registry  
    b. Security concerns: these are publicly accessible resources  
    c. Deploying containers to the cluster from local  
    d. Connecting Azure DevOps and deploying containers, via helm
        
2. Deploying an AKS configuration to an existing virtual network infrastructure 
    a. Example bicep deployment of virtual networking infrastructure  
    b. Security concerns: what is the external facing endpoint?  
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Application gateway or Azure Firewall?  
    c. Connecting Azure DevOps and deploying containers  

3. AKS-Baseline deployment  

4. Custom deployment  

5. Notes:
https://azure.github.io/azure-workload-identity/docs/installation/managed-clusters.html#steps-to-get-the-oidc-issuer-url-from-a-generic-managed-cluster
