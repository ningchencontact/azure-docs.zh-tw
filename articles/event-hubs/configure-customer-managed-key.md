---
title: 設定您自己的金鑰來加密待用 Azure 事件中樞資料
description: 本文提供有關如何設定您自己的金鑰來加密 Azure 事件中樞資料其餘部分的資訊。
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: spelluru
ms.openlocfilehash: 311f69ffa436eebb261fb8aa5ee72886ad9fe9d0
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035900"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>使用 Azure 入口網站, 設定客戶管理的金鑰來加密待用 Azure 事件中樞資料
Azure 事件中樞使用 Azure 儲存體服務加密 (Azure SSE) 提供待用資料的加密。 事件中樞依賴 Azure 儲存體來儲存資料, 而且根據預設, 與 Azure 儲存體一起儲存的所有資料都會使用 Microsoft 管理的金鑰進行加密。 

## <a name="overview"></a>總覽
Azure 事件中樞現在支援使用 Microsoft 管理的金鑰或客戶管理的金鑰 (攜帶您自己的金鑰– BYOK) 來加密待用資料的選項。 這項功能可讓您建立、輪替、停用及撤銷用來加密待用資料 Azure 事件中樞的客戶管理金鑰的存取權。

啟用 BYOK 功能是在命名空間上進行的一次性設定程式。

> [!NOTE]
> [事件中樞專用的單一租](event-hubs-dedicated-overview.md)使用者叢集支援 BYOK 功能。 無法針對標準事件中樞命名空間啟用此功能。

您可以使用 Azure Key Vault 來管理金鑰, 並審核金鑰使用方式。 您可以建立自己的金鑰, 並將其儲存在金鑰保存庫中, 或者您可以使用 Azure Key Vault Api 來產生金鑰。 如需 Azure 金鑰保存庫的詳細資訊，請參閱 [什麼是 Azure 金鑰保存庫？](../key-vault/key-vault-overview.md)

本文說明如何使用 Azure 入口網站, 以客戶管理的金鑰來設定金鑰保存庫。 若要瞭解如何使用 Azure 入口網站建立金鑰保存庫, 請參閱 [] 快速入門:使用 Azure 入口網站] (.., 從 Azure Key Vault 設定和取出秘密。/key-vault/quick-create-portal.md).

> [!IMPORTANT]
> 將客戶管理的金鑰與 Azure 事件中樞搭配使用時, 金鑰保存庫必須設定兩個必要的屬性。 其中包括：虛**刪除**和**不要清除**。 當您在 Azure 入口網站中建立新的金鑰保存庫時, 預設會啟用這些屬性。 不過, 如果您需要在現有的金鑰保存庫上啟用這些屬性, 則必須使用 PowerShell 或 Azure CLI。

## <a name="enable-customer-managed-keys"></a>啟用客戶管理的金鑰
若要在 Azure 入口網站中啟用客戶管理的金鑰, 請遵循下列步驟:

1. 流覽至您的事件中樞專用叢集。
1. 選取您要啟用 BYOK 的命名空間。
1. 在事件中樞命名空間的 [**設定**] 頁面上, 選取 [**加密 (預覽)** ]。 
1. 選取 [待用**客戶管理的金鑰加密**], 如下圖所示。 

    ![啟用客戶管理的金鑰](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>使用金鑰設定金鑰保存庫
啟用客戶管理的金鑰之後, 您必須將客戶管理的金鑰與您的 Azure 事件中樞命名空間產生關聯。 事件中樞只支援 Azure Key Vault。 如果您在上一節中啟用 [**使用客戶管理的金鑰進行加密**] 選項, 則必須將金鑰匯入 Azure Key Vault。 此外, 索引鍵必須具有虛**刪除**, 而且**不會**針對金鑰進行清除設定。 這些設定可以使用[PowerShell](../key-vault/key-vault-soft-delete-powershell.md)或[CLI](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection)來設定。

1. 若要建立新的金鑰保存庫, 請遵循 Azure Key Vault[快速入門](../key-vault/key-vault-overview.md)。 如需匯入現有金鑰的詳細資訊, 請參閱[關於金鑰、秘密和憑證](../key-vault/about-keys-secrets-and-certificates.md)。
1. 若要在建立保存庫時開啟虛刪除和清除保護, 請使用[az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)命令。

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. 若要將清除保護新增至現有的保存庫 (已啟用虛刪除), 請使用[az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)命令。

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. 依照下列步驟建立金鑰:
    1. 若要建立新的金鑰，從 [設定] 下方的 [金鑰] 功能表中選取 [產生/匯入]。
        
        ![選取 [產生/匯入] 按鈕](./media/configure-customer-managed-key/select-generate-import.png)
    1. 將 [選項] 設定為 [產生]，並為金鑰提供名稱。

        ![建立金鑰](./media/configure-customer-managed-key/create-key.png) 
    1. 您現在可以從下拉式清單中選取此金鑰, 使其與要加密的事件中樞命名空間產生關聯。 

        ![從 key vault 選取金鑰](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. 填入金鑰的詳細資料, 然後按一下 [**選取**]。 這可讓您使用客戶管理的金鑰, 對命名空間中的待用資料進行加密。 

        > [!NOTE]
        > 針對預覽, 您只能選取單一金鑰。 

## <a name="rotate-your-encryption-keys"></a>輪替加密金鑰
您可以使用 Azure 金鑰保存庫迴圈機制來輪替金鑰保存庫中的金鑰。 如需詳細資訊, 請參閱[設定金鑰輪替和審核](../key-vault/key-vault-key-rotation-log-monitoring.md)。 啟用和到期日期也可以設定為自動執行金鑰輪替。 事件中樞服務會偵測新的金鑰版本, 並開始自動使用它們。

## <a name="revoke-access-to-keys"></a>撤銷金鑰的存取權
撤銷加密金鑰的存取權並不會從事件中樞清除資料。 不過, 資料無法從事件中樞命名空間存取。 您可以透過存取原則或藉由刪除金鑰來撤銷加密金鑰。 深入瞭解存取原則和保護您的金鑰保存庫,[以安全地存取金鑰保存庫](../key-vault/key-vault-secure-your-key-vault.md)。

撤銷加密金鑰之後, 加密命名空間上的事件中樞服務就會變成無法運作。 如果已啟用金鑰的存取權, 或還原刪除金鑰, 事件中樞服務會挑選金鑰, 讓您可以從加密的事件中樞命名空間存取資料。

> [!NOTE]
> 如果您從金鑰保存庫中刪除現有的加密金鑰, 並將它取代為事件中樞命名空間上的新金鑰, 由於刪除金鑰仍然有效 (快取) 長達一小時, 因此仍然可以存取舊資料 (以舊金鑰加密) 使用新的資料, 現在只能使用新的金鑰來存取。 此行為是在功能的預覽版本中設計的。 

## <a name="set-up-diagnostic-logs"></a>設定診斷記錄 
設定已啟用 BYOK 之命名空間的診斷記錄, 可在使用客戶管理的金鑰加密命名空間時, 提供有關作業的必要資訊。 這些記錄可以啟用並于稍後串流至事件中樞, 或透過 log analytics 分析, 或串流至儲存體來執行自訂分析。 若要深入瞭解診斷記錄, 請參閱[Azure 診斷記錄的總覽](../azure-monitor/platform/diagnostic-logs-overview.md)。

## <a name="enable-user-logs"></a>啟用使用者記錄
請遵循下列步驟來啟用客戶管理金鑰的記錄。

1. 在 Azure 入口網站中, 流覽至已啟用 BYOK 的命名空間。
1. 選取 [**監視**] 下的 [**診斷設定**]。

    ![選取 [診斷設定]](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. 選取 [ **+ 新增診斷設定**]。 

    ![選取 [新增診斷設定]](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. 提供**名稱**, 然後選取您要將記錄串流至其中的位置。
1. 選取 [ **CustomerManagedKeyUserLogs** ], 然後按一下 [**儲存**]。 此動作會在命名空間上啟用 BYOK 的記錄。

    ![選取 [客戶管理的金鑰使用者記錄檔] 選項](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>記錄檔結構描述 
所有記錄都會以「JavaScript 物件標記法」(JSON) 格式儲存。 每個專案都有使用下表所述格式的字串欄位。 

| Name | 描述 |
| ---- | ----------- | 
| TaskName | 失敗工作的描述。 |
| ActivityId | 用於追蹤的內部識別碼。 |
| category | 定義工作的分類。 例如, 如果您的金鑰保存庫中的金鑰已停用, 則它會是資訊類別目錄, 或者, 如果無法解除包裝金鑰, 可能會發生錯誤。 |
| resourceId | Azure Resource Manager 資源識別碼 |
| keyVault | 金鑰保存庫的完整名稱。 |
| key | 用來加密事件中樞命名空間的索引鍵名稱。 |
| 版本 | 所使用的金鑰版本。 |
| operation | 金鑰保存庫中金鑰上執行的作業。 例如, 停用/啟用金鑰、換行或解除包裝 |
| code | 與作業相關聯的程式碼。 範例：錯誤碼, 404 表示找不到索引鍵。 |
| message | 與作業相關聯的任何錯誤訊息 |

以下是客戶管理的金鑰的記錄範例:

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="troubleshoot"></a>疑難排解
最佳做法是一律啟用記錄, 如前一節所示。 當啟用 BYOK 加密時, 它有助於追蹤活動。 它也有助於界定問題的範圍。

以下是啟用 BYOK 加密時, 要尋找的常見錯誤代碼。

| Action | 錯誤碼 | 產生的資料狀態 |
| ------ | ---------- | ----------------------- | 
| 從金鑰保存庫移除 wrap/解除包裝許可權 | 403 |    無法存取 |
| 從授與 wrap/解除包裝許可權的 AAD 主體移除 AAD 角色成員資格 | 403 |  無法存取 |
| 從金鑰保存庫中刪除加密金鑰 | 404 | 無法存取 |
| 刪除金鑰保存庫 | 404 | 無法存取 (假設已啟用虛刪除, 這是必要的設定)。 |
| 變更加密金鑰的到期時間, 使其已過期 | 403 |   無法存取  |
| 變更 NBF (而不是之前), 讓金鑰加密金鑰不在使用中 | 403 | 無法存取  |
| 針對金鑰保存庫防火牆選取 [**允許 MSFT 服務**] 選項, 或封鎖具有加密金鑰的金鑰保存庫的網路存取 | 403 | 無法存取 |
| 將金鑰保存庫移至不同的租使用者 | 404 | 無法存取 |  
| 間歇性的網路問題或 DNS/AAD/MSI 中斷 |  | 可使用快取資料加密金鑰來存取 |

> [!IMPORTANT]
> 若要在使用 BYOK 加密的命名空間上啟用異地 DR, 配對的次要命名空間必須在專用叢集中, 而且必須在其上啟用系統指派的受控識別。 若要深入瞭解, 請參閱[適用于 Azure 資源的受控](../active-directory/managed-identities-azure-resources/overview.md)識別。

> [!NOTE]
> 如果您事件中樞命名空間的 Azure Key Vault 上設定虛擬網路 (VNet) 服務端點, 將不會支援 BYOK。 


## <a name="next-steps"></a>後續步驟
請參閱下列文章：
- [事件中心概觀](event-hubs-about.md)
- [Key Vault 總覽](../key-vault/key-vault-overview.md)




