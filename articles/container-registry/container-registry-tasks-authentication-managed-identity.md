---
title: 使用受管理的身分識別與 Azure 容器登錄的工作
description: 提供適用於 Azure 資源，指派受管理的身分識別，包括其他私人容器登錄庫的 Azure 資源的 Azure Container Registry 工作存取。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: 5b60727472a06aaac8ccd3dce8609461e8972311
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148036"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>使用 Azure 受控 ACR 工作中的身分識別 

使用[受管理的 Azure 資源的識別](../active-directory/managed-identities-azure-resources/overview.md)來向 ACR 工作從 Azure container registry 或其他 Azure 資源，而不需要提供或管理程式碼中的認證。 比方說，使用受控身分識別來提取，或將容器映像推送至另一個登錄中，為工作中的步驟。

在本文中，深入了解受管理的身分識別，以及如何：

> [!div class="checklist"]
> * 啟用系統指派的身分識別或 ACR 工作上的使用者指派身分識別
> * 授與身分識別存取 Azure 資源，例如其他 Azure 容器登錄庫
> * 使用受管理的身分識別來從工作存取的資源 

若要建立的 Azure 資源，本文會要求您執行 Azure CLI 2.0.66 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli]。

## <a name="why-use-a-managed-identity"></a>為什麼要使用受控識別？

適用於 Azure 資源的受控識別會在 Azure Active Directory (Azure AD) 中為 Azure 服務提供自動的受控識別。 您可以設定[某些 Azure 資源](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)，ACR 工作，包括受管理的身分識別。 然後使用身分識別存取其他 Azure 資源，不需要使用程式碼或指令碼傳遞認證。

受控身分識別有兩種：

* *使用者指派的身分識別*，您可以指派給多個資源並視需要保存。 使用者指派的身分識別目前處於預覽狀態。

* A*系統管理身分識別*，這是特定的資源，例如 ACR 工作唯一，且該資源的存留期間持續。

設定 Azure 資源管理的身分識別之後，授與給另一個資源，就像任何安全性主體的身分識別存取。 比方說，將受控身分識別與提取、 推送和提取或其他權限的角色在 Azure 中私人容器登錄。 (如需登錄角色的完整清單，請參閱 [Azure Container Registry 角色和權限](container-registry-roles.md)。)您可以提供對一或多個資源的身分識別存取權。

## <a name="create-container-registries"></a>建立容器登錄庫

在本教學課程中，您需要三個容器登錄庫：

* 您可以使用第一次登錄來建立和執行 ACR 工作。 在本文中，名為此來源登錄*myregistry*。 
* 第二個和第三個登錄是第一次的範例工作，才能推送映像建置的目標登錄。 在本文中，名稱目標登錄*customregistry1*並*customregistry2*。

取代為您自己的登錄名稱，在稍後的步驟。

如果您還沒有所需的 Azure 容器登錄庫，請參閱[快速入門：使用 Azure CLI 建立私人容器登錄](container-registry-get-started-azure-cli.md)。 您不需要尚未將映像推送至登錄。

## <a name="example-task-with-a-system-assigned-identity"></a>範例：系統指派的身分識別的工作

此範例將示範如何建立[多步驟工作](container-registry-tasks-multi-step.md)系統指派的身分識別。 工作建置映像，，，然後使用向兩個目標登錄推送映像的 身分識別。

此範例中工作的步驟中所定義[YAML 檔案](container-registry-tasks-reference-yaml.md)名為`testtask.yaml`。 檔案位於 multipleRegistries 目錄[acr 工作](https://github.com/Azure-Samples/acr-tasks)範例存放庫。 檔案在此處重現：

```yml
version: v1.0.0
steps:
  - build: -t {{.Values.REGISTRY1}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY1}}/hello-world:{{.Run.ID}}"]
  - build: -t {{.Values.REGISTRY2}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY2}}/hello-world:{{.Run.ID}}"]
```

### <a name="create-task-with-system-assigned-identity"></a>系統指派的身分識別建立工作

建立工作*多個 reg*藉由執行下列[az acr 工作建立][ az-acr-task-create]命令。 工作內容是 multipleRegistries 資料夾的範例存放庫中，而命令參考檔案`testtask.yaml`存放庫中。 `--assign-identity`不帶其他值的參數建立工作的系統指派的身分識別。 這項工作是設定，因此您必須手動觸發程序，但您可以設定它以認可推送至存放庫，或提出提取要求時執行。 

```azurecli
az acr task create \
  --registry myregistry \
  --name multiple-reg \
  --context https://github.com/Azure-Samples/acr-tasks.git#:multipleRegistries \
  --file testtask.yaml \
  --commit-trigger-enabled false \
  --pull-request-trigger-enabled false \
  --assign-identity
```

在命令輸出中，`identity`區段會顯示類型的身分識別`SystemAssigned`工作中設定。 `principalId`是身分識別的服務主體識別碼：

```console
[...]
  "identity": {
    "principalId": "xxxxxxxx-2703-42f9-97d0-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  },
  "location": "eastus",
[...]
``` 

使用  [az acr 工作 show] [ az-acr-task-show]命令以儲存`principalId`在變數中，在後續命令中使用：

```azurecli
principalID=$(az acr task show --name multiple-reg --registry myregistry --query identity.principalId --output tsv)
```

### <a name="give-identity-push-permissions-to-two-target-container-registries"></a>授與身分識別到兩個目標容器登錄的推播權限

在本節中，系統指派的識別權限授與要推送到這兩個目標登錄，名為*customregistry1*並*customregistry2*。

第一次使用[az acr show] [ az-acr-show]命令來取得每個登錄的資源識別碼，並將識別碼儲存在變數：

```azurecli
reg1_id=$(az acr show --name customregistry1 --query id --output tsv)
reg2_id=$(az acr show --name customregistry2 --query id --output tsv)
```

使用[az 角色指派建立][ az-role-assignment-create]命令，以將身分識別指派`acrpush`每個登錄的角色。 此角色對於容器登錄庫提取和推送映像的權限。

```azurecli
az role assignment create --assignee $principalID --scope $reg1_id --role acrpush
az role assignment create --assignee $principalID --scope $reg2_id --role acrpush
```

### <a name="add-target-registry-credentials-to-task"></a>將目標登錄認證新增至工作

現在，使用[az acr 工作認證新增][ az-acr-task-credential-add]命令，以將身分識別的認證新增至工作，讓它可以使用這兩個目標登錄進行驗證。

```azurecli
az acr task credential add \
  --name multiple-reg \
  --registry myregistry \
  --login-server customregistry1.azurecr.io \
  --use-identity [system]

az acr task credential add \
  --name multiple-reg \
  --registry myregistry \
  --login-server customregistry2.azurecr.io \
  --use-identity [system]
```

### <a name="manually-run-the-task"></a>手動執行工作

使用[az acr 工作執行][ az-acr-task-run]命令來手動觸發的工作。 `--set`參數用來做為工作變數值傳入這兩個目標登錄的登入伺服器名稱`REGISTRY1`和`REGISTRY2`。

```azurecli
az acr task run \
  --name multiple-reg \
  --registry myregistry \
  --set REGISTRY1=customregistry1.azurecr.io \
  --set REGISTRY2=customregistry2.azurecr.io
```

輸出會類似：

```console
Sending build context to Docker daemon  4.096kB
Step 1/1 : FROM hello-world
 ---> fce289e99eb9
Successfully built fce289e99eb9
Successfully tagged customregistry2.azurecr.io/hello-world:cf31
2019/05/31 22:16:02 Successfully executed container: acb_step_0
2019/05/31 22:16:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:02 Pushing image: customregistry2.azurecr.io/hello-world:cf31, attempt 1
The push refers to repository [customregistry2.azurecr.io/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Pushed
cf31: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/05/31 22:16:08 Successfully pushed image: customregistry2.azurecr.io/hello-world:cf31
2019/05/31 22:16:08 Executing step ID: acb_step_2. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:08 Scanning for dependencies...
2019/05/31 22:16:08 Successfully scanned dependencies
2019/05/31 22:16:08 Launching container with name: acb_step_2
Sending build context to Docker daemon  4.096kB
Step 1/1 : FROM hello-world
 ---> fce289e99eb9
Successfully built fce289e99eb9
Successfully tagged customregistry1.azurecr.io/hello-world:cf31
2019/05/31 22:16:09 Successfully executed container: acb_step_2
2019/05/31 22:16:09 Executing step ID: acb_step_3. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:09 Pushing image: customregistry1.azurecr.io/hello-world:cf31, attempt 1
The push refers to repository [customregistry1.azurecr.io/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Pushed
cf31: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/05/31 22:16:21 Successfully pushed image: customregistry1.azurecr.io/hello-world:cf31
2019/05/31 22:16:21 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.985291)
2019/05/31 22:16:21 Populating digests for step ID: acb_step_0...
2019/05/31 22:16:22 Successfully populated digests for step ID: acb_step_0
2019/05/31 22:16:22 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 5.743225)
2019/05/31 22:16:22 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 1.925959)
2019/05/31 22:16:22 Populating digests for step ID: acb_step_2...
2019/05/31 22:16:24 Successfully populated digests for step ID: acb_step_2
2019/05/31 22:16:24 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.061057)
2019/05/31 22:16:24 The following dependencies were found:
2019/05/31 22:16:24
- image:
    registry: customregistry2.azurecr.io
    repository: hello-world
    tag: cf31
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:6f744a2005b12a704d2608d8070a494ad1145636eeb74a570c56b94d94ccdbfc
  git:
    git-head-revision: 05275dca2bc61f584085ca913c39d509236f576b
- image:
    registry: customregistry1.azurecr.io
    repository: hello-world
    tag: cf31
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:6f744a2005b12a704d2608d8070a494ad1145636eeb74a570c56b94d94ccdbfc
  git:
    git-head-revision: 05275dca2bc61f584085ca913c39d509236f576b

Run ID: cf31 was successful after 35s
```

## <a name="example-task-with-a-user-assigned-identity"></a>範例：使用使用者指派的身分識別的工作

在此範例中，您可以建立使用者指派的身分識別具有從 Azure key vault 讀取祕密的權限。 您可以指派此身分識別讀取祕密、 建置映像，且登入至 Azure CLI 來讀取影像標記的多步驟工作。

### <a name="create-a-key-vault-and-store-a-secret"></a>建立金鑰保存庫，並儲存祕密

首先，如果您需要建立名為的資源群組*myResourceGroup*中*eastus*取代為下列位置[az 群組建立][ az-group-create]命令：

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用[az keyvault 建立][ az-keyvault-create]命令來建立金鑰保存庫。 請務必指定唯一的金鑰保存庫名稱。 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

儲存在金鑰保存庫中使用的範例祕密[az keyvault 祕密組][ az-keyvault-secret-set]命令：

```azurecli
az keyvault secret set \
  --name SampleSecret \
  --value "Hello ACR Tasks!" \
  --description ACRTasksecret  \
  --vault-name mykeyvault
```

例如，您可能要儲存認證，以向私人 Docker 登錄，因此您可以提取私人映像。

### <a name="create-an-identity"></a>建立身分識別

建立名為身分識別*myACRTasksId*在您的訂用帳戶使用[az 身分識別建立][ az-identity-create]命令。 您可以使用您先前用來建立容器登錄庫或金鑰保存庫或另一個相同的資源群組。

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

若要在接下來的步驟中設定身分識別，請使用 [az identity show][az-identity-show] 命令，以將身分識別的資源識別碼與服務主體識別碼儲存在變數中。

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get service principal ID of the user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)
```

### <a name="grant-identity-access-to-keyvault-to-read-secret"></a>身分識別存取權授與金鑰保存庫讀取祕密

執行下列[az keyvault 設定原則][ az-keyvault-set-policy]命令來設定金鑰保存庫的存取原則。 下列範例可讓您的使用者指派的身分識別從 key vault 取得密碼。 稍後需要此存取權，才能順利執行的多步驟工作。

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $principalID --secret-permissions get
```

### <a name="grant-identity-reader-access-to-the-resource-group-for-registry"></a>識別讀取者存取權授與登錄的資源群組

執行下列[az 角色指派建立][ az-role-assignment-create]命令將指派的身分識別讀取器角色，在此情況下包含來源登錄的資源群組。 稍後需要此角色，才能順利執行的多步驟工作。

```azurecli
az role assignment create --role reader --resource-group myResourceGroup --assignee $principalID
```

### <a name="create-task-with-user-assigned-identity"></a>建立工作以指派給使用者的身分識別

現在，建立[多步驟工作](container-registry-tasks-multi-step.md)並將其指派的使用者指派身分識別。 針對此範例工作中，建立[YAML 檔案](container-registry-tasks-reference-yaml.md)名為`managed-identities.yaml`在本機工作目錄並貼上下列內容。 請務必使用金鑰保存庫的名稱取代檔案中的金鑰保存庫名稱

```yml
version: v1.0.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: name
    keyvault: https://mykeyvault.vault.azure.net/secrets/SampleSecret
steps:
  # Verify that the task can access the secret in the key vault
  - cmd: bash -c 'if [ -z "$MY_SECRET" ]; then echo "Secret not resolved"; else echo "Secret resolved"; fi'
    env: 
      - MY_SECRET='{{.Secrets.name}}' 

  # Build/push the website image to source registry, using dockerfile in the Azure-Samples repo
  - cmd: docker build -t {{.Run.Registry}}/my-website:{{.Run.ID}} https://github.com/Azure-Samples/aci-helloworld.git
  - push: 
    - "{{.Run.Registry}}/my-website:{{.Run.ID}}"
  
  # Login to Azure CLI with identity and list the tags to verify that the image is in the registry
  - cmd: microsoft/azure-cli az login --identity
  - cmd: microsoft/azure-cli az acr repository show-tags -n {{.Values.registryName}} --repository my-website
```

這項工作會執行下列動作：

* 確認它可以存取您金鑰保存庫中的密碼。 這個步驟是供示範之用。 在真實世界案例中，您可能需要的工作步驟，以取得認證來存取私人的 Docker Hub 存放庫。
* 建置並將推送`mywebsite`來源登錄的映像。
* 登入 Azure CLI 來清單`my-website`影像來源登錄中的標記。

建立工作，稱為*msitask*並將它傳遞您先前建立的使用者指派身分識別的資源識別碼。 此範例建立工作時從`managed-identities.yaml`您在本機工作目錄，所以您不必手動觸發程序儲存的檔案。

```azurecli
az acr task create \
  --name msitask \
  --registry myregistry \
  --context /dev/null \
  --file managed-identities.yaml \
  --pull-request-trigger-enabled false \
  --commit-trigger-enabled false \
  --assign-identity $resourceID
```

在命令輸出中，`identity`區段會顯示類型的身分識別`UserAssigned`工作中設定。 `principalId`是身分識別的服務主體識別碼：

```console
[...]
"identity": {
    "principalId": null,
    "tenantId": null,
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-d12e-4760-9ab6-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRTasksId": {
        "clientId": "xxxxxxxx-f17e-4768-bb4e-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-1335-433d-bb6c-xxxxxxxxxxxx"
      }
[...]
```

### <a name="manually-run-the-task"></a>手動執行工作

使用[az acr 工作執行][ az-acr-task-run]命令來手動觸發的工作。 `--set`參數用來在來源登錄名稱傳遞至工作：

```azurecli
az acr task run --name msitask --registry myregistry --set registryName=myregistry  
```

輸出會顯示祕密已解決，並順利建置並推送，映像工作登入身分識別與 Azure CLI，從來源登錄讀取影像標籤：

```console
Queued a run with ID: cf32
Waiting for an agent...
2019/06/10 23:25:37 Downloading source code...
2019/06/10 23:25:38 Finished downloading source code
2019/06/10 23:25:39 Using acb_vol_4e4a0a7c-b6ef-4ec5-b40f-3436fc5eb0f5 as the home volume
2019/06/10 23:25:41 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/10 23:25:41 Successfully set up Docker network: acb_default_network
2019/06/10 23:25:41 Setting up Docker configuration...
2019/06/10 23:25:42 Successfully set up Docker configuration
2019/06/10 23:25:42 Logging in to registry: myregistry.azurecr.io
2019/06/10 23:25:43 Successfully logged into myregistry.azurecr.io
2019/06/10 23:25:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:25:43 Launching container with name: acb_step_0
Secret resolved
2019/06/10 23:25:44 Successfully executed container: acb_step_0
2019/06/10 23:25:44 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:25:44 Launching container with name: acb_step_1
Sending build context to Docker daemon  74.75kB

[...]

cf32: digest: sha256:cbb4aa83b33f6959d83e84bfd43ca901084966a9f91c42f111766473dc977f36 size: 1577
2019/06/10 23:26:05 Successfully pushed image: myregistry.azurecr.io/my-website:cf32
2019/06/10 23:26:05 Executing step ID: acb_step_3. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:26:05 Launching container with name: acb_step_3
[
  {
    "environmentName": "AzureCloud",
    "id": "xxxxxxxx-d12e-4760-9ab6-xxxxxxxxxxxx",
    "isDefault": true,
    "name": "DanLep Internal Consumption",
    "state": "Enabled",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
      "user": {
      "assignedIdentityInfo": "MSI",
      "name": "systemAssignedIdentity",
      "type": "servicePrincipal"
    }
  }
]
2019/06/10 23:26:09 Successfully executed container: acb_step_3
2019/06/10 23:26:09 Executing step ID: acb_step_4. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:26:09 Launching container with name: acb_step_4
[
  "cf32"
]
2019/06/10 23:26:14 Successfully executed container: acb_step_4
2019/06/10 23:26:14 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.025312)
2019/06/10 23:26:14 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 13.703823)
2019/06/10 23:26:14 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 6.791506)
2019/06/10 23:26:14 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 3.852972)
2019/06/10 23:26:14 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 5.079079)
```


## <a name="next-steps"></a>後續步驟

在本文中，您已了解有關搭配使用受控身分識別與 Azure 容器登錄的工作，以及如何：

> [!div class="checklist"]
> * 啟用系統指派的身分識別或 ACR 工作上使用者指派
> * 授與身分識別存取 Azure 資源，例如其他 Azure 容器登錄庫
> * 使用受管理的身分識別來從工作存取的資源  

* 深入了解 [Azure 資源的受控識別](/azure/active-directory/managed-identities-azure-resources/)。

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
[az-keyvault-create]: /cli/azure/keyvault?#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
