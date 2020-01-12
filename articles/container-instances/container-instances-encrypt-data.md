---
title: 加密部署資料
description: 瞭解為您的容器實例資源保存的資料加密，以及如何使用客戶管理的金鑰來加密資料
ms.topic: article
ms.date: 01/10/2020
ms.author: danlep
ms.openlocfilehash: 146effd7f1a7ad1ddd94886d1a79e2914bd1c94b
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2020
ms.locfileid: "75904207"
---
# <a name="encrypt-deployment-data"></a>加密部署資料

在雲端中執行 Azure 容器實例（ACI）資源時，ACI 服務會收集並保存與您的容器相關的資料。 ACI 會在此資料保存在雲端時，自動將其加密。 此加密可保護您的資料，以協助您符合組織的安全性和合規性承諾。 ACI 也可以讓您選擇使用自己的金鑰來加密此資料，讓您能夠更充分掌控與 ACI 部署相關的資料。

## <a name="about-aci-data-encryption"></a>關於 ACI 資料加密 

ACI 中的資料會使用256位 AES 加密來加密和解密。 它會針對所有 ACI 部署啟用，而且您不需要修改您的部署或容器，即可利用此加密。 這包括有關部署的中繼資料、環境變數、將金鑰傳遞至您的容器，以及在您的容器停止後保存的記錄，讓您仍然可以看到它們。 加密並不會影響您的容器群組效能，而且不會有額外的加密成本。

## <a name="encryption-key-management"></a>加密金鑰管理

您可以依賴 Microsoft 管理的金鑰來加密容器資料，也可以使用您自己的金鑰來管理加密。 下表會比較這些選項： 

|    |    Microsoft 管理的金鑰     |     客戶管理的金鑰     |
|----|----|----|
|    加密/解密作業    |    Azure    |    Azure    |
|    金鑰儲存    |    Microsoft 金鑰存放區    |    Azure Key Vault    |
|    金鑰輪替責任    |    Microsoft    |    客戶    |
|    金鑰存取    |    僅限 Microsoft    |    Microsoft、客戶    |

本檔的其餘部分涵蓋以金鑰（客戶管理的金鑰）加密 ACI 部署資料所需的步驟。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>使用客戶管理的金鑰來加密資料

### <a name="create-service-principal-for-aci"></a>建立 ACI 的服務主體

第一個步驟是確保您的[azure 租](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)使用者具有指派給 Azure 容器實例服務許可權的服務主體。 

下列 CLI 命令會在您的 Azure 環境中設定 ACI SP：

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

執行此命令的輸出應該會顯示已使用 "displayName"： "Azure 容器實例服務" 設定的服務主體。

### <a name="create-a-key-vault-resource"></a>建立金鑰保存庫資源

使用[Azure 入口網站](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault)、 [CLI](https://docs.microsoft.com/azure/key-vault/quick-create-cli)或[PowerShell](https://docs.microsoft.com/azure/key-vault/quick-create-powershell)建立 Azure Key Vault。 

如需金鑰保存庫的屬性，請使用下列指導方針： 
* 名稱：需要唯一的名稱。 
* 訂用帳戶：選擇訂用帳戶。
* 在 [資源群組] 下，選擇現有的資源群組，或 [建立新的]，然後輸入資源組名。
* 在 [位置] 下拉式功能表中選擇位置。
* 您可以將其他選項保留為預設值，或根據其他需求進行挑選。

> [!IMPORTANT]
> 使用客戶管理的金鑰來加密 ACI 部署範本時，建議您在金鑰保存庫上設定下列兩個屬性： [虛刪除] 和 [不要清除]。 這些屬性預設不會啟用，但可以使用 PowerShell 或 Azure CLI 在新的或現有的金鑰保存庫上啟用。

### <a name="generate-a-new-key"></a>產生新的金鑰 

建立金鑰保存庫之後，流覽至 Azure 入口網站中的資源。 在資源分頁的左側導覽功能表上，按一下 [設定] 底下的 [**金鑰**]。 在 [金鑰] 的 [顯示] 上，按一下 [產生/匯入] 以產生新的金鑰。 針對此金鑰使用任何唯一的名稱，以及根據您的需求的任何其他喜好設定。 

![產生新的金鑰](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>設定存取原則

建立新的存取原則，以允許 ACI 服務存取您的金鑰。

* 一旦產生金鑰，請回到您的金鑰保存庫資源分頁，在 [設定] 底下，按一下 [**存取原則**]。
* 在金鑰保存庫的 [存取原則] 頁面上，按一下 [**新增存取原則**]。
* 設定*金鑰許可權*以包含**取得**和解除包裝**金鑰**![設定金鑰許可權](./media/container-instances-encrypt-data/set-key-permissions.png)
* 針對 [*選取主體*]，選取 [ **Azure 容器實例服務**]
* 按一下底部的 [**新增**] 

存取原則現在應該會顯示在您的金鑰保存庫的存取原則中。

![新增存取原則](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>修改您的 JSON 部署範本

> [!IMPORTANT]
> 使用客戶管理的金鑰來加密部署資料，可在目前推出的最新 API 版本（2019-12-01）中取得。在您的部署範本中指定此 API 版本。 如果您有任何問題，請與 Azure 支援服務聯繫。

設定金鑰保存庫金鑰和存取原則之後，請將下列屬性新增至您的 ACI 部署範本。 您可以在[教學課程：使用 Resource Manager 範本部署多容器群組](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group)中，深入瞭解如何使用範本部署 ACI 資源。 

具體而言，在部署範本的容器群組屬性區段底下，新增 "encryptionProperties"，其中包含下列值：
* vaultBaseUrl：金鑰保存庫的 DNS 名稱，可以在入口網站中金鑰保存庫資源的 [總覽] 分頁中找到。
* keyName：稍早產生的金鑰名稱
* keyVersion：金鑰的目前版本。 您可以按一下金鑰本身（在金鑰保存庫資源的 [設定] 區段中的 [金鑰] 底下）來找到此值


```json
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "encryptionProperties": {
                "vaultBaseUrl": "https://example.vault.azure.net",
                "keyName": "acikey",
                "keyVersion": "xxxxxxxxxxxxxxxx"
            },
            "containers": {
                [...]
            }
        }
    }
]
```

### <a name="deploy-your-resources"></a>部署您的資源

如果您已在桌面上建立並編輯範本檔案，您可以將檔案拖曳到您的 Cloud Shell 目錄中，將其上傳至該檔案。 

使用 [az group create][az-group-create] 命令來建立資源群組。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用 [az group deployment create][az-group-deployment-create] 命令部署範本。

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

在幾秒內，您應該會從 Azure 收到首次回應。 部署完成之後，ACI 服務所保存的所有相關資料都會以您提供的金鑰進行加密。

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create