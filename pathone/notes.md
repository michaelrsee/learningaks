--attach-acr is Grant the 'acrpull' role assignment to the ACR specified by name or resource ID.


# Push and Pull Docker image to an ACR  

Push and Pull a Docker Image to an ACR: https://learn.microsoft.com/en-us/azure/container-registry/container-registry-get-started-docker-cli?tabs=azure-cli  

Pre-req's:  
Azure CLI installed  
Docker CLI installed  

az login  
az acr login --name "myregistryexample" 
sudo az acr login --name acrpathone29614
az acr login --name acrpathone29614

or use docker login:  
docker login myregistry.azurecr.io  

Pull a public Nginx image to local:  
docker pull mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine  
or pull from the latest Nginx image from Docker Hub    
sudo docker pull nginx

Run the container locally:  
docker run -it --rm -p 8080:80 mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine   
docker run -it --rm -p 8080:80 nginx 

Browse to http://localhost:8080 to view the default web page served by Nginx in the running container.  

To stop and remove the container, press Control+C.  

Create an alias of the image:  
docker tag mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine myregistry.azurecr.io/samples/nginx    
sudo docker tag nginx acrpathone29614.azurecr.io/samples/nginx  
docker tag nginx acrpathone29614.azurecr.io/samples/nginx  

Push the image to your registry:  
docker push myregistry.azurecr.io/samples/nginx 
sudo docker push acrpathone29614.azurecr.io/samples/nginx
docker push acrpathone29614.azurecr.io/samples/nginx

Optional Testing Steps:  
Pull the image from your registry:    
docker pull myregistry.azurecr.io/samples/nginx  

Star the Nginx container:  
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx  

Browse to http://localhost:8080 to view the running container.
To stop and remove the container, press Control+C.  

Remove the local image (Optional):  
docker rmi myregistry.azurecr.io/samples/nginx

To remove the image from the Azure Container Registry:
az acr repository delete --name myregistry --image samples/nginx:latest

# Push an image to an AKS cluster 

https://learn.microsoft.com/en-us/azure/aks/hybrid/deploy-azure-container-registry

For an Ubuntu Linux image, install kubectl via:
sudo snap install kubectl --classic

Confirm that the image was successfully pushed to the ACR:
az acr repository list --name <REGISTRY_NAME>.azurecr.io --output table
az acr repository list --name acrpathone16649 --output table

To deploy your container image from the ACR to your Kubernetes cluster, you need to create Kubernetes Secrets to store your registry credentials. Kubernetes uses an image pull secret to store information needed to authenticate to your registry. To create the pull secret for an ACR, you provide the service principal ID, the password, and the registry URL.

kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<REGISTRY_NAME>.azurecr.io \
    --docker-username=<appId> \
    --docker-password=<password>

Get the credentials for the current cluster:
az aks get-credentials --resource-group <ResourceGroup> --name <AKSClusterName>
az aks get-credentials --resource-group rg-aks-pathone-32312 --name aks-pathone-23298  

Deploy the pod configuration to the Kubernetes cluster:
kubectl create -f pod-example.yaml

To confirm that the pod was successfully created using the container image from the ACR, run kubectl describe pod <POD_NAME>, which should show the container image used to create the pod.
kubectl describe pod pod-example

