## <a name="create-a-service-principal"></a>建立服務主體

若要建立具容器登錄存取權的服務主體，可以使用以下指令碼。 使用您容器登錄的名稱更新 `ACR_NAME` 變數，並可選擇使用 [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] 命令中的 `--role` 值來授與不同權限。 您必須已安裝 [Azure CLI](/cli/azure/install-azure-cli)，才能使用這個指令碼。

執行指令碼之後，請記下的服務主體的**識別碼**和**密碼**。 一旦擁有其認證，便可以將您的應用程式和服務設定為以服務主體向您的容器登錄進行驗證。

```bash
#!/bin/bash

# Modify for your environment. The ACR_NAME is the name of your Azure Container
# Registry, and the SERVICE_PRINCIPAL_NAME can be any unique name within your
# subscription (you can use the default below).
ACR_NAME=myregistryname
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate some values required for subsequent command args
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create the service principal with rights scoped to the registry.
# Default permissions are for both docker push and pull access. Modify the
# '--role' argument value as desired:
# reader:      pull only
# contributor: push and pull
# owner:       push, pull, and assign roles
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --scopes $ACR_REGISTRY_ID --role reader --query password --output tsv)
SP_APP_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output the service principal's credentials; use these in your services and
# applications to authenticate to the container registry.
echo "Service principal ID: $SP_APP_ID"
echo "Service principal password: $SP_PASSWD"
```

## <a name="use-an-existing-service-principal"></a>使用現有的服務主體

若要授與現有服務主體登錄存取權，您必須為服務主體指派新的角色。 如同建立新的服務主體，您可以授與提取、推送和提取，以及擁有者存取權。

以下指令碼使用 [az role assignment create][az-role-assignment-create] 命令，以授與您在 `SERVICE_PRINCIPAL_ID` 變數中所指定的服務主體「提取」權限。 如果您要授與不同層級的存取權，請調整 `--role` 值。

```bash
#!/bin/bash

# Modify for your environment. The ACR_NAME is the name of your Azure Container
# Registry, and the SERVICE_PRINCIPAL_ID is the service principal's 'appId' or
# one of its 'servicePrincipalNames' values.
ACR_NAME=myregistryname
SERVICE_PRINCIPAL_ID=<service-principal-ID>

# Populate value required for subsequent command args
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Assign the desired role to the service principal. Modify the '--role' argument
# value as desired:
# reader:      pull only
# contributor: push and pull
# owner:       push, pull, and assign roles
az role assignment create --assignee $SERVICE_PRINCIPAL_ID --scope $ACR_REGISTRY_ID --role reader
```

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create