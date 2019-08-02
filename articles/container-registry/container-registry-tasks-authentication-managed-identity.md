---
title: 使用受控識別搭配 Azure Container Registry 工作
description: 在 Azure Container Registry 工作中啟用適用于 Azure 資源的受控識別, 以允許工作存取其他 Azure 資源, 包括其他私人容器登錄。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/11/2019
ms.author: danlep
ms.openlocfilehash: 9f7c083a079e42172a9e2865f90293fa4d6813d8
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640422"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>在 ACR 工作中使用受 Azure 管理的身分識別 

在[ACR](container-registry-tasks-overview.md)工作中啟用[適用于 Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/overview.md), 讓工作可以存取其他 Azure 資源, 而不需要提供或管理認證。 例如, 您可以使用受控識別來啟用工作步驟, 以將容器映射提取或推送至另一個登錄。

在本文中, 您將瞭解如何使用 Azure CLI, 在 ACR 工作上啟用使用者指派或系統指派的受控識別。 您可以使用 Azure Cloud Shell 或 Azure CLI 的本機安裝。 如果您想要在本機使用, 則需要2.0.68 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

如需使用受控識別從 ACR 工作存取受保護資源的案例, 請參閱:

* [跨登錄驗證](container-registry-tasks-cross-registry-authentication.md)
* [使用儲存在 Azure Key Vault 中的秘密存取外部資源](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>為什麼要使用受控識別？

適用于 Azure 資源的受控識別會在 Azure Active Directory (Azure AD) 中, 為選取的 Azure 服務提供自動的受控識別。 您可以使用受控識別來設定 ACR 工作, 讓工作可以存取其他受保護的 Azure 資源, 而不需要在工作步驟中傳遞認證。

受控身分識別有兩種：

* *使用者指派*的身分識別, 您可以指派給多個資源, 並視需要保存。 使用者指派的身分識別目前處於預覽狀態。

* *系統指派*的身分識別, 這對於特定資源 (例如 ACR 工作) 而言是唯一的, 而且會在該資源的存留期內持續。

您可以在 ACR 工作中啟用其中一種或兩種類型的身分識別。 將身分識別存取權授與另一個資源, 就像任何安全性主體一樣。 當工作執行時, 它會使用身分識別來存取任何需要存取的工作步驟中的資源。

## <a name="steps-to-use-a-managed-identity"></a>使用受控識別的步驟

遵循這些高階步驟, 以使用受控識別搭配 ACR 工作。

### <a name="1-optional-create-a-user-assigned-identity"></a>1.選擇性建立使用者指派的身分識別

如果您打算使用使用者指派的身分識別, 您可以使用現有的身分識別。 或者, 使用 Azure CLI 或其他 Azure 工具來建立身分識別。 例如, 使用[az identity create][az-identity-create]命令。 

如果您打算僅使用系統指派的身分識別, 請略過此步驟。 當您建立 ACR 工作時, 可以建立系統指派的身分識別。

### <a name="2-enable-identity-on-an-acr-task"></a>2.啟用 ACR 工作的身分識別

當您建立 ACR 工作時, 可以選擇性地啟用使用者指派的身分識別、系統指派的身分識別, 或兩者。 例如, 當您在`--assign-identity` Azure CLI 中執行[az acr task create][az-acr-task-create]命令時, 請傳遞參數。

若要啟用系統指派的身分識別, `--assign-identity`請傳遞沒有值`assign-identity [system]`或的。 下列命令會從公用 GitHub 存放庫建立 Linux 工作, 以使用 Git `hello-world`認可觸發程式和系統指派的受控識別來建立映射:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --assign-identity
```

若要啟用使用者指派的身分識別, `--assign-identity`請使用身分識別的*資源識別碼*值來傳遞。 下列命令會從公用 GitHub 存放庫建立 Linux 工作, 以使用 Git `hello-world`認可觸發程式和使用者指派的受控識別來建立映射:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --assign-identity <resourceID>
```

您可以藉由執行[az identity show][az-identity-show]命令, 取得身分識別的資源識別碼。 資源群組*myResourceGroup*中識別碼*MYUSERASSIGNEDIDENTITY*的資源識別碼的格式為。 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3.授與身分識別許可權以存取其他 Azure 資源

視您工作的需求而定, 授與身分識別許可權以存取其他 Azure 資源。 範例包括：

* 使用提取、推送和提取, 或 Azure 中目標容器登錄的其他許可權, 將角色指派給受控識別。 如需登錄角色的完整清單, 請參閱[Azure Container Registry 角色和許可權](container-registry-roles.md)。 
* 將角色指派給受控識別, 以讀取 Azure 金鑰保存庫中的秘密。

使用[Azure CLI](../role-based-access-control/role-assignments-cli.md)或其他 Azure 工具來管理資源的角色型存取。 例如, 執行[az role 指派 create][az-role-assignment-create]命令, 將身分識別的角色指派給身分識別。 

下列範例會將許可權指派給受控識別, 以從容器登錄中提取。 命令會指定身分識別的*服務主體識別碼*和目標登錄的*資源識別碼*。


```azurecli
az role assignment create --assignee <servicePrincipalID> --scope <registryID> --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4.選擇性將認證新增至工作

如果您的工作將映射提取或推送至另一個 Azure 容器登錄, 請將認證新增至要驗證的身分識別工作。 執行[az acr task credential add][az-acr-task-credential-add]命令並傳遞`--use-identity`參數, 以將身分識別的認證新增至工作。 

例如, 若要為系統指派的身分識別新增認證以向登錄*targetregistry*進行驗證, 請`use-identity [system]`傳遞:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

若要為使用者指派的身分識別新增認證, 以向登錄*targetregistry*進行驗證`use-identity` , 請使用識別的*用戶端識別碼*值來傳遞。 例如:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

您可以藉由執行[az identity show][az-identity-show]命令, 取得身分識別的用戶端識別碼。 [用戶端識別碼] 是表單`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`的 GUID。

## <a name="next-steps"></a>後續步驟

在本文中, 您已瞭解如何在 ACR 工作上啟用和使用使用者指派或系統指派的受控識別。 如需使用受控識別從 ACR 工作存取受保護資源的案例, 請參閱:

* [跨登錄驗證](container-registry-tasks-cross-registry-authentication.md)
* [使用儲存在 Azure Key Vault 中的秘密存取外部資源](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[azure-cli-install]: /cli/azure/install-azure-cli
