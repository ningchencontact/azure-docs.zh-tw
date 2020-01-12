---
title: 為您的 Azure Cosmos DB 帳戶設定客戶管理的金鑰
description: 瞭解如何為您的 Azure Cosmos DB 帳戶設定客戶管理的金鑰
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: thweiss
ms.openlocfilehash: 32266abd5bcf8d7e137095d130ee872cc07edaf0
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2020
ms.locfileid: "75904077"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-db-account"></a>為您的 Azure Cosmos DB 帳戶設定客戶管理的金鑰

> [!NOTE]
> 此時，您必須要求存取權才能使用這項功能。 若要這麼做，請洽詢[cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com)。

儲存在 Azure Cosmos DB 帳戶中的資料會自動且順暢地加密。 Azure Cosmos DB 提供兩個選項來管理用來加密待用資料的金鑰：
- **服務管理的金鑰**。 根據預設，Microsoft 會管理用來加密您的 Azure Cosmos DB 帳戶的金鑰。
- **客戶管理的金鑰（CMK）** 。 您可以選擇使用您所管理的金鑰來新增第二層的加密。

客戶管理的金鑰必須儲存在[Azure Key Vault](../key-vault/key-vault-overview.md)中。 必須為每個已啟用 CMK 的帳戶提供金鑰，並用來加密該帳戶中儲存的所有資料。

## <a name="setup"></a>安裝程式

目前，客戶管理的金鑰僅適用于新帳戶，而且必須在帳戶建立期間進行設定。

### <a name="1-make-sure-the-azure-cosmos-db-resource-provider-is-registered-for-your-azure-subscription"></a>1. 請確定已為您的 Azure 訂用帳戶註冊 Azure Cosmos DB 資源提供者

從 [Azure 入口網站] 移至您的 Azure 訂用帳戶，然後從左側功能表中選取 [資源提供者]：

![左側功能表中的 [資源提供者] 專案](./media/how-to-setup-cmk/portal-rp.png)

搜尋 "Microsoft DocumentDB" 資源提供者。
- 如果資源提供者已標示為已註冊，則不需要執行任何動作。
- 如果沒有，請選取它，然後按一下 [註冊]：

    ![正在註冊 Microsoft DocumentDB 資源提供者](./media/how-to-setup-cmk/portal-rp-register.png)

### <a name="2-configure-your-azure-key-vault-instance"></a>2. 設定您的 Azure Key Vault 實例

使用客戶管理的金鑰搭配 Azure Cosmos DB 需要在您打算用來裝載加密金鑰的 Azure Key Vault 實例上設定兩個屬性： [虛**刪除**] 和 [不要**清除**]。 這些屬性預設不會啟用，但可以使用 PowerShell 或 Azure CLI 來啟用。

若要瞭解如何在現有的 Azure Key Vault 實例上啟用這些屬性，請參閱下列其中一篇文章中的
- [如何使用 PowerShell 進行虛刪除](../key-vault/key-vault-soft-delete-powershell.md)
- [如何搭配 Azure CLI 使用虛刪除](../key-vault/key-vault-soft-delete-cli.md)

### <a name="3-add-an-access-policy-to-your-azure-key-vault-instance"></a>3. 將存取原則新增至您的 Azure Key Vault 實例

從 Azure 入口網站，移至您打算用來裝載加密金鑰的 Azure Key Vault 實例。 然後，從左側功能表中選取 [存取原則]：

![左側功能表中的 [存取原則]](./media/how-to-setup-cmk/portal-akv-ap.png)

- 按一下 [+ 新增存取原則]
- 在 [金鑰許可權] 下拉式功能表中，選取 [取得]、[解除包裝金鑰] 和 [將金鑰換行]：

    ![選取正確的許可權](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

- 在 [選取主體] 底下，按一下 [未選取任何]，搜尋並選取 [Azure Cosmos DB] 主體，然後按一下底部的 [選取] （如果找不到 "Azure Cosmos DB" 主體，您可能需要重新註冊 "Microsoft DocumentDB" 資源步驟2中的提供者）：

    ![選取 Azure Cosmos DB 主體](./media/how-to-setup-cmk/portal-akv-add-ap.png)

- 按一下 [新增] 以新增新的存取原則

### <a name="4-generate-a-key-in-azure-key-vault"></a>4. 在 Azure Key Vault 中產生金鑰

從 Azure 入口網站，移至您打算用來裝載加密金鑰的 Azure Key Vault 實例。 然後，從左側功能表中選取 [金鑰]：

![左側功能表中的 [金鑰] 專案](./media/how-to-setup-cmk/portal-akv-keys.png)

- 按一下 [產生/匯入]
- 提供新金鑰的名稱，選取 RSA 金鑰大小（建議最少3072，以獲得最佳安全性），然後按一下 [建立]：

    ![建立新的金鑰](./media/how-to-setup-cmk/portal-akv-gen.png)

- 建立金鑰之後，依序按一下新建立的金鑰和其目前版本
- 複製索引鍵的「金鑰識別碼」，但最後一個正斜線後面的部分除外：

    ![複製金鑰的金鑰識別碼](./media/how-to-setup-cmk/portal-akv-keyid.png)

### <a name="5-create-a-new-azure-cosmos-db-account"></a>5. 建立新的 Azure Cosmos DB 帳戶

#### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

從 Azure 入口網站建立新的 Azure Cosmos DB 帳戶時，請選擇「加密」步驟中的 [客戶管理的金鑰]。 在 [金鑰 URI] 欄位中，傳遞從步驟4複製的 Azure Key Vault 金鑰的 URI：

![在 Azure 入口網站中設定 CMK 參數](./media/how-to-setup-cmk/portal-cosmos-enc.png)

#### <a name="using-powershell"></a>使用 PowerShell

使用 PowerShell 建立新的 Azure Cosmos DB 帳戶時，
- 在 "PropertyObject" 的 "keyVaultKeyUri" 屬性下，傳遞從步驟4複製的 Azure Key Vault 金鑰的 URI。
- 請務必使用 "2019-12-12" 作為 API 版本。

```powershell
$resourceGroupName = "myResourceGroup"
$accountLocation = "West US 2"
$accountName = "mycosmosaccount"

$failoverLocations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 }
)

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$failoverLocations;
    "keyVaultKeyUri" = "https://<my-vault>.vault.azure.net/keys/<my-key>";
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2019-12-12" -ResourceGroupName $resourceGroupName `
    -Location $accountLocation -Name $accountName -PropertyObject $CosmosDBProperties
```

#### <a name="using-azure-resource-manager-templates"></a>使用 Azure 資源管理員範本

透過 Azure Resource Manager 範本建立新的 Azure Cosmos DB 帳戶時：
- 傳遞在 "properties" 物件的 "keyVaultKeyUri" 屬性下，從步驟4複製的 Azure Key Vault 金鑰的 URI
- 請務必使用 "2019-12-12" 作為 API 版本

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "keyVaultKeyUri": {
            "type": "string"
        }
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[parameters('accountName')]",
            "apiVersion": "2019-12-12",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "locations": [ 
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    }
                ],
                "databaseAccountOfferType": "Standard",
                "keyVaultKeyUri": "[parameters('keyVaultKeyUri')]"
            }
        }
    ]
}

```

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="is-there-any-additional-charge-when-using-customer-managed-keys"></a>使用客戶管理的金鑰時，是否會有任何額外的費用？

可以。 若要考慮使用客戶管理的金鑰來管理資料加密和解密所需的額外計算負載，針對 Azure Cosmos DB 帳戶執行的所有作業都會在取用的[要求單位](./request-units.md)中增加25%。

### <a name="what-data-gets-encrypted-with-the-cmk"></a>哪些資料會使用 CMK 加密？

儲存在 Azure Cosmos DB 帳戶中的所有資料都會使用 CMK 進行加密，但下列中繼資料除外：
- 您的 Azure Cosmos DB[帳戶、資料庫和容器](./account-overview.md#elements-in-an-azure-cosmos-account)的名稱、
- [預存程式](./stored-procedures-triggers-udfs.md)的名稱，
- 在[索引編制原則](./index-policy.md)中宣告的屬性路徑，
- 容器的分割區索引[鍵](./partitioning-overview.md)的值。

### <a name="will-customer-managed-keys-be-supported-for-existing-accounts"></a>現有帳戶是否支援客戶管理的金鑰？

這項功能目前僅適用于新帳戶。

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>是否有計劃支援比帳戶層級金鑰更精細的資料細微性？

但目前不會考慮容器層級的索引鍵。

### <a name="how-does-customer-managed-keys-affect-backups"></a>客戶管理的金鑰如何影響備份？

Azure Cosmos DB 會針對儲存在您帳戶中的資料[進行定期和自動備份](./online-backup-and-restore.md)。 這種作業會備份加密的資料。 若要讓還原的備份可供使用，備份時所使用的加密金鑰仍然必須可供使用。 這表示不應該進行撤銷，而且備份時所使用的金鑰版本仍應啟用。

### <a name="how-do-i-revoke-an-encryption-key"></a>如何? 撤銷加密金鑰？

金鑰撤銷是藉由停用最新版本的金鑰來完成：

![停用金鑰的版本](./media/how-to-setup-cmk/portal-akv-rev2.png)

或者，若要從 Azure Key Vault 實例中撤銷所有金鑰，您可以刪除授與 Azure Cosmos DB 主體的存取原則：

![正在刪除 Azure Cosmos DB 主體的存取原則](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>撤銷客戶管理的金鑰後，可以使用哪些作業？

撤銷加密金鑰時唯一可能的作業是刪除帳戶。

## <a name="next-steps"></a>後續步驟

- 深入瞭解[Azure Cosmos DB 中的資料加密](./database-encryption-at-rest.md)
- 深入瞭解[Cosmos DB 中資料的安全存取](secure-access-to-data.md)