--attach-acr is Grant the 'acrpull' role assignment to the ACR specified by name or resource ID.


# Push and Pull Docker image to an ACR  

Push and Pull a Docker Image to an ACR: https://learn.microsoft.com/en-us/azure/container-registry/container-registry-get-started-docker-cli?tabs=azure-cli  

Pre-req's:  
Azure CLI installed  
Docker CLI installed  

az login  
az acr login --name <myregistry>  

or use docker login:  
docker login myregistry.azurecr.io  

Pull a public Nginx image to local:  
docker pull mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine  
or  
docker pull nginx

Run the container locally:  
docker run -it --rm -p 8080:80 mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine 
docker run -it --rm -p 8080:80 nginx 

Browse to http://localhost:8080 to view the default web page served by Nginx in the running container.  

To stop and remove the container, press Control+C.  

Create an alias of the image:  
docker tag mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine myregistry.azurecr.io/samples/nginx  
docker tag nginx acrpathone29895.azurecr.io/samples/nginx

Push the image to your registry:  
docker push myregistry.azurecr.io/samples/nginx 
docker push acrpathone29895.azurecr.io/samples/nginx

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