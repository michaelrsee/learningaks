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

# Create and Ingress Controller in your AKS cluster

https://learn.microsoft.com/en-us/azure/aks/ingress-basic?tabs=azure-cli   

https://youtu.be/nuNfCRQ2Yac

Install the latest version of Helm: https://helm.sh/   

Create a new ingress controller namespace:  
kubectl create namespace ingress-basic  

Add the community based nginx ingress controller to your local helm repo:  
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx  

Run the Ingress create command:
helm install ingress-nginx ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.externalTrafficPolicy=Local

helm install ingress-nginx ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."kubernetes\.io/os"=linux \
    --set controller.service.externalTrafficPolicy=Local

It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace ingress-basic get services -o wide -w ingress-nginx-controller'  

Validate all the resources that were deployed to the ingress-basic namespace:  
kubectl get all -n ingress-basic

Now create the path based routing rules using the ingress service:
Create the aks-hellowworld-one.yaml file and the aks-helloworld-two.yaml files and paste in info from learning path.

Create the hello-world-ingress.yaml file and paste in info from learning patgh.

Run the two demo applications into the new namespace:  
kubectl apply -f aks-helloworld-one.yaml --namespace ingress-basic  
kubectl apply -f aks-helloworld-two.yaml --namespace ingress-basic  

Make sure you are in the correct namespace context:
kubectl config set-context --current --namespace=ingress-basic

Create the ingress service:
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic

Verify the connectivity by getting the extrnal ip of the new ingress controller:
kubectl get svc -n ingress-basic

Open browser and check:
http://20.42.32.194
http://20.42.32.194/hello-world-one
http://20.42.32.194/hello-world-two