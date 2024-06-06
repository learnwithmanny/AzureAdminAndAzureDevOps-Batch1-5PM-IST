# Step-01: Create Static Public IP

 Create Public IP with static allocation either on Azure Portal or with below command.

 > az network public-ip create --resource-group "ResourceGrpName" --name "NameofPublicIP" --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv

    Make a note of Public IP created for Ingress. E.g. 152.23.11.90

## Step-02: Install Ingress Controller

    #1. Create a namespace for your ingress resources
    > kubectl create namespace ingress-collection

    #2. Install Helm(if not installed)
    > winget install Helm.Helm   #for helm installation details https://helm.sh/docs/intro/install/ 
    #3  Add the official stable repository
        > helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
        > helm repo update
    
    #4  Customizing the Chart Before Installing.    
        > helm show values ingress-nginx/ingress-nginx

    #5 Use Helm to deploy an NGINX ingress controller

        > helm install ingress-nginx ingress-nginx/ingress-nginx \
            --namespace ingress-basic \
            --set controller.replicaCount=2 \
            --set controller.nodeSelector."kubernetes\.io/os"=linux \
            --set defaultBackend.nodeSelector."kubernetes\.io/os"=linux \
            --set controller.service.externalTrafficPolicy=Local \
            --set controller.service.loadBalancerIP="REPLACE_STATIC_IP" 

    #6 List Services with labels
            kubectl get service -l app.kubernetes.io/name=ingress-nginx --namespace ingress-basic

    #7 List Pods
            kubectl get pods -n ingress-basic
            kubectl get all -n ingress-basic

    #8 Verify Load Balancer on Azure Mgmt Console
            Primarily refer Settings -> Frontend IP Configuration

## Step-03: Apply Yaml Manisfest files
- app1deployment.yml
- app1ClusterIPSvc.yml
- app2deployment.yml
- app2ClusterIPSvc.yml
- ingressService.yml

> kubectl apply -f kube-manifests/

## Step-04: Validate Application and Deployment

    - List Pods
        > kubectl get pods

    - List Services
        > kubectl get svc

    - List Ingress
        > kubectl get ingress

    - Access Application
        http://<Public-IP-created-for-Ingress>

    - Verify Ingress Controller Logs
        > kubectl get pods -n ingress-basic
        > kubectl logs -f <pod-name> -n ingress-basic
