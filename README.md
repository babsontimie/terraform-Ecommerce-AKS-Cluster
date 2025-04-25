# terraform-Ecommerce-AKS-Cluster
terraform-Ecommerce-AKS-Cluster
# Quickstart: Deploy an Azure Kubernetes Service (AKS) cluster using Terraform
# -----------------------------------------------------------------------------
#Install and configure Terraform.

#Download kubectl.

#Create a random value for the Azure resource group name using random_pet.

#Create an Azure resource group using azurerm_resource_group.

#Access the configuration of the AzureRM provider to get the Azure Object ID using azurerm_client_config.

#Create a Kubernetes cluster using azurerm_kubernetes_cluster.

#Create an AzAPI resource azapi_resource.


Initialize Terraform
=====================
 
terraform init -upgrade

terraform plan -out main.tfplan

terraform apply main.tfplan

#---------------------------------------------
#Verify the results
#---------------------
#Get the Azure resource group name using the following command.
#---------------------------------------------------------------


resource_group_name=$(terraform output -raw resource_group_name)


#Display the name of your new Kubernetes cluster using the az aks list command.
#------------------------------------------------------------------------------

az aks list \
  --resource-group $resource_group_name \
  --query "[].{\"K8s cluster name\":name}" \
  --output table
  

echo "$(terraform output kube_config)" > ./azurek8s

# Verify the previous command didn't add an ASCII EOT character using the following command.

cat ./azurek8s
 
export KUBECONFIG=./azurek8s


#Verify the health of the cluster using the kubectl get nodes command.

kubectl get nodes

--------------------------------------------------------
APPLY THE MANIFEST
------------------
kubectl apply -f aks-store-quickstart.yaml
------------------------------------------------
Test the application
======================
#When the application runs, a Kubernetes service exposes the application front end to the internet. This process can take a few minutes to complete.

kubectl get pods
kubectl get service store-front --watch
----------------------------------------------------------------------------
DESTROY THE CLUSTER
-------------------

terraform plan -destroy -out main.destroy.tfplan
terraform apply main.destroy.tfplan

-----------------------------------------------------
# Delete service principal
#-------------------------
sp=$(terraform output -raw sp)
az ad sp delete --id $sp

#-----------------------------------------------------------------------------------------------------
