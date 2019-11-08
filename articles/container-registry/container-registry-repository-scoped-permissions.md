---
title: Azure Container Registry 中存放庫的許可權
description: 建立權杖，其許可權範圍限於登錄中的特定存放庫，以提取或推送映射
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 10/31/2019
ms.author: danlep
ms.openlocfilehash: 7b9d220ac7e507513458eab6b55276b3aa434739
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2019
ms.locfileid: "73742758"
---
# <a name="repository-scoped-permissions-in-azure-container-registry"></a>Azure Container Registry 中的存放庫範圍許可權 

Azure Container Registry 使用對整個登錄具有[角色型存取權](container-registry-roles.md)的身分識別，支援數個[驗證選項](container-registry-authentication.md)。 不過，在某些情況下，您可能只需要提供登錄中特定*存放庫*的存取權。 

本文說明如何建立及使用存取權杖，其僅有許可權可在登錄中的特定存放庫上執行動作。 有了存取權杖，您就可以為使用者或服務提供有範圍的限時存取存放庫，以提取或推送映射，或執行其他動作。 

如需有關權杖概念和案例的背景資訊，請參閱本文稍後的[關於儲存機制範圍的許可權](#about-repository-scoped-permissions)。

> [!IMPORTANT]
> 此功能目前在預覽階段，但[有某些限制](#preview-limitations)。 若您同意[補充的使用規定][terms-of-use]即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="preview-limitations"></a>預覽限制

* 這項功能僅適用于**Premium**容器登錄。 如需登錄服務層和限制的相關資訊，請參閱[Azure Container Registry sku](container-registry-skus.md)。
* 您目前無法將存放庫範圍的許可權指派給 Azure Active Directory 物件，例如服務主體或受控識別。

## <a name="prerequisites"></a>必要條件

* **Azure CLI** -這篇文章需要本機安裝 Azure CLI （2.0.76 或更新版本）。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。
* **Docker** -若要使用登錄進行驗證，您也需要本機 Docker 安裝。 Docker 提供 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 系統的安裝指示。
* **具有存放庫的 container registry** -如果您沒有，請在您的 Azure 訂用帳戶中建立容器登錄。 例如，使用 [Azure 入口網站](container-registry-get-started-portal.md)或 [Azure CLI](container-registry-get-started-azure-cli.md)。 

  基於測試目的，請將一或多個範例影像[推送](container-registry-get-started-docker-cli.md)或匯[入](container-registry-import-images.md)至登錄。 本文中的範例會參考兩個存放庫中的下列影像： `samples/hello-world:v1` 和 `samples/nginx:v1`。 

## <a name="create-an-access-token"></a>建立存取權杖

使用[az acr token create][az-acr-token-create]命令來建立權杖。 建立權杖時，請在每個儲存機制上指定一或多個存放庫和相關聯的動作，或使用這些設定來指定現有的範圍對應。

### <a name="create-access-token-and-specify-repositories"></a>建立存取權杖並指定存放庫

下列範例會建立具有許可權的存取權杖，以執行 `samples/hello-world` 存放庫上的 `content/write` 和 `content/read` 動作，以及 `samples/nginx` 儲存機制上的 `content/read` 動作。 根據預設，此命令會產生兩個密碼。 

這個範例會將權杖狀態設定為 `enabled` （預設設定），但是您可以隨時更新權杖，並將狀態設定為 `disabled`。

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world content/write content/read \
  --repository samples/nginx content/read --status enabled
```

輸出會顯示權杖的詳細資料，包括產生的密碼和範圍對應。 建議您將密碼儲存在安全的位置，以供稍後使用 `docker login`。 無法再次抓取密碼，但可以產生新的密碼。

輸出也會顯示已自動建立範圍對應，名為 `MyToken-scope-map`。 您可以使用範圍對應，將相同的儲存機制動作套用至其他權杖。 或者，稍後更新範圍對應以變更權杖許可權。

```console
{
  "creationDate": "2019-10-22T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2019-10-22T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2019-10-22T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password2",
        "value": "kPX6Or/xxxxLXpqowxxxxkA0idwLtmxxxx"
      }
    ],
    "username": "MyToken"
  },
  "id": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/tokens/MyToken",
  "name": "MyToken",
  "objectId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapId": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyToken-scope-map",
  "status": "enabled",
  "type": "Microsoft.ContainerRegistry/registries/tokens"
```

### <a name="create-a-scope-map-and-associated-token"></a>建立範圍對應和相關聯的權杖

或者，在建立權杖時，使用存放庫和相關聯的動作來指定範圍對應。 若要建立範圍對應，請使用[az acr scope-map create][az-acr-scope-map-create]命令。

下列範例命令會使用上述範例中所使用的相同許可權來建立範圍對應。 它允許 `samples/hello-world` 存放庫上的 `content/write` 和 `content/read` 動作，以及 `samples/nginx` 存放庫上的 `content/read` 動作：

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world content/write content/read \
  --repository samples/nginx content/read \
  --description "Sample scope map"
```

輸出大致如下：

```console
{
  "actions": [
    "repositories/samples/hello-world/content/write",
    "repositories/samples/nginx/content/read"
  ],
  "creationDate": "2019-10-22T05:07:35.194413+00:00",
  "description": "Sample scope map.",
  "id": "/subscriptions/fxxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyScopeMap",
  "name": "MyScopeMap",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapType": "UserDefined",
  "type": "Microsoft.ContainerRegistry/registries/scopeMaps"
```

執行[az acr token create][az-acr-token-create]來建立與*MyScopeMap*範圍對應相關聯的權杖。 根據預設，此命令會產生兩個密碼。 這個範例會將權杖狀態設定為 `enabled` （預設設定），但是您可以隨時更新權杖，並將狀態設定為 `disabled`。

```azurecli
az acr token create --name MyToken --registry myregistry --scope-map MyScopeMap --status enabled
```

輸出會顯示權杖的詳細資料，包括產生的密碼和您套用的範圍對應。 建議您將密碼儲存在安全的位置，以供稍後使用 `docker login`。 無法再次抓取密碼，但可以產生新的密碼。

## <a name="generate-passwords-for-token"></a>產生權杖的密碼

如果您在建立權杖時建立了密碼，請繼續[使用登錄進行驗證](#authenticate-using-token)。

如果您沒有權杖密碼，或您想要產生新的密碼，請執行[az acr token credential 產生][az-acr-token-credential-generate]命令。

下列範例會針對您所建立的權杖產生新密碼，並在30天內到期。 它會將密碼儲存在環境變數中 TOKEN_PWD。 這個範例會針對 bash shell 進行格式化。

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

## <a name="authenticate-using-token"></a>使用權杖進行驗證

執行 `docker login`，使用權杖認證來向登錄進行驗證。 輸入權杖名稱做為使用者名稱，並提供其中一個密碼。 下列範例會針對 bash shell 進行格式化，並使用環境變數來提供值。

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

輸出應該會顯示成功的驗證：

```console
Login Succeeded
```

## <a name="verify-scoped-access"></a>驗證限定範圍的存取

您可以驗證權杖是否提供登錄中存放庫的範圍許可權。 在此範例中，下列 `docker pull` 命令會順利完成，以提取 `samples/hello-world` 和 `samples/nginx` 存放庫中可用的映射：

```console
docker pull myregistry.azurecr.io/samples/hello-world:v1
docker pull myregistry.azurecr.io/samples/nginx:v1
```

因為範例權杖只允許 `samples/hello-world` 存放庫上的 `content/write` 動作，所以 `docker push` 成功到該存放庫，但 `samples/nginx`會失敗：

```console
# docker push succeeds
docker pull myregistry.azurecr.io/samples/hello-world:v1

# docker push fails
docker pull myregistry.azurecr.io/samples/nginx:v1
```

## <a name="update-scope-map-and-token"></a>更新範圍對應和權杖

若要更新權杖許可權，請使用[az acr scope map update][az-acr-scope-map-update]來更新相關聯範圍對應中的許可權。 例如，若要更新*MyScopeMap*以移除 `samples/hello-world` 儲存機制上的 `content/write` 動作：

```azurecli
az acr scope-map update --name MyScopeMap --registry myregistry \
  --remove samples/hello-world content/write
```

如果範圍對應與一個以上的權杖相關聯，此命令會更新所有相關聯權杖的許可權。

如果您想要使用不同的範圍對應來更新權杖，請執行[az acr token update][az-acr-token-update]。 例如：

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

更新權杖或與權杖相關聯的範圍對應之後，許可權變更會在下一個 `docker login` 或使用權杖的其他驗證生效。

更新權杖之後，您可能會想要產生新的密碼來存取登錄。 執行[az acr token credential [產生][az-acr-token-credential-generate]]。 例如：

```azurecli
az acr token credential generate \
  --name MyToken --registry myregistry --days 30
```

## <a name="about-repository-scoped-permissions"></a>關於存放庫範圍的許可權

### <a name="concepts"></a>概念

若要設定存放庫範圍的許可權，您可以使用 Azure CLI 中的命令來建立*存取權杖*和相關聯的*範圍對應*。

* **存取權杖**是一種認證，搭配用來向登錄進行驗證的密碼。 與每個權杖相關聯的允許*動作*範圍設定為一個或多個存放庫。 您可以設定每個權杖的到期時間。 

* 每個指定存放庫上的**動作**都包含下列一或多個。

  |動作  |說明  |
  |---------|---------|
  |`content/read`     |  讀取存放庫中的資料。 例如，提取成品。  |
  |`metadata/read`    | 讀取存放庫中的中繼資料。 例如，列出標記或顯示資訊清單中繼資料。   |
  |`content/write`     |  將資料寫入存放庫。 使用與 `content/read` 來推送成品。    |
  |`metadata/write`     |  將中繼資料寫入至存放庫。 例如，更新資訊清單屬性。  |
  |`content/delete`    | 移除存放庫中的資料。 例如，刪除存放庫或資訊清單。 |

* **範圍對應**是一個登錄物件，可將您套用至權杖的存放庫許可權分組，或重新套用至其他權杖。 如果您在建立權杖時未套用範圍對應，系統會自動為您建立範圍對應，以儲存許可權設定。 

  範圍對應可協助您設定多個使用者對一組存放庫具有相同的存取權。 Azure Container Registry 也會提供系統定義的範圍對應，您可以在建立存取權杖時加以套用。

下圖摘要說明權杖與範圍對應之間的關聯性。 

![登錄範圍對應和權杖](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

### <a name="scenarios"></a>案例

使用存取權杖的案例包括：

* 提供具有個別權杖的 IoT 裝置，以從存放庫提取映射
* 提供具有特定存放庫許可權的外部組織 
* 限制存放庫存取您組織中的特定使用者群組。 例如，針對建立以特定存放庫為目標之影像的開發人員，以及從這些存放庫部署之小組的讀取存取權，提供寫入和讀取權限。

### <a name="authentication-using-token"></a>使用權杖進行驗證

使用權杖名稱作為使用者名稱，以及其中一個相關聯的密碼來向目標登錄進行驗證。 驗證方法取決於設定的動作。

### <a name="contentread-or-contentwrite"></a>內容/讀取或內容/寫入

如果權杖只允許 `content/read` 或 `content/write` 動作，請在下列其中一個驗證流程中提供權杖認證：

* 使用 `docker login` 向 Docker 進行驗證
* 使用 Azure CLI 中的[az acr login][az-acr-login]命令，向登錄進行驗證

在驗證之後，權杖允許已設定範圍的儲存機制或儲存機制上的動作。 例如，如果權杖允許存放庫上的 `content/read` 動作，則會允許在該存放庫中的映射上執行 `docker pull` 作業。

#### <a name="metadataread-metadatawrite-or-contentdelete"></a>中繼資料/讀取、中繼資料/寫入，或內容/刪除

如果權杖允許在存放庫上 `metadata/read`、`metadata/write`或 `content/delete` 動作，則必須使用 Azure CLI 中的相關[az acr repository][az-acr-repository]命令，將權杖認證當做參數提供。

例如，如果在存放庫上允許 `metadata/read` 動作，請在執行[az acr repository show-tags][az-acr-repository-show-tags]命令以列出標記時，傳遞權杖認證。

## <a name="next-steps"></a>後續步驟

* 若要管理範圍對應和存取權杖，請使用[az acr 範圍對應][az-acr-scope-map]和[az acr token][az-acr-token]命令群組中的其他命令。
* 如需使用系統管理員帳戶或 Azure Active Directory 身分識別向 Azure container registry 進行驗證的案例，請參閱[驗證概述](container-registry-authentication.md)。


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate
