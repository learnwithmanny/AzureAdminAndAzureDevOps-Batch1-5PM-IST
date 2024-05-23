# Step1
    Create Azure Key-Vault and Secret ( we can use Azure Cli, Azure Powershell or Azure Portal for this)
# Step2
    Assign AKS worker node<VMSS>(get and list) permission at Key Vault either using system managed identity or user assigned identity
# Step3    
    To Verify the Secrets Store CSI Driver is installed or not, Run the below command:-
    
    > kubectl get pods -n kube-system -l ‘app in (secrets-store-csi-driver, secrets-store-provider-azure)’
# Step4
    If the Secrets Store CSI Driver is not installed , use the below command to install:-
    
    > az aks enable-addons --addons azure-keyvault-secrets-provider --name VisualPathlearningAKS --resource-group visualpathlab

    #Verify again by running the command in #Step 2
# Step5
    After Completting Step 1 to Step 4, we need to create "secretproviderclass" by using yaml mainfest file name "secretproviderclass.yml"
            Points to be noted:-
                1. In Secret Provider Class Yaml file, replae below values
                    keyvaultName: aks-kv123 # Enter the name of your Azure Key Vault
                2. objects: |
                        array:
                          - |
                            objectName: mysql-db-password   # name of your secret which you created in Azure Key Vault
                            objectType: secret              # object types: secret, key, or cert

                3. secretObjects:
                     - secretName: aks-secret
                      # mention any name to create secret, the same name going to be used in Deployment Yaml file to call the secret stored in Azure Key Vault
   # Step6
   Mount the secret class provider as Volume in Deployment YAML File

    volumeMounts:
                - name: kv-secret-store
                  mountPath: /mnt/secrets-store
    volumes:
      - name: kv-secret-store
        csi:
          driver: secrets-store.csi.k8s.io
          readOnly: true
          volumeAttributes:
              secretProviderClass: "azurekeyvault-secproclass"