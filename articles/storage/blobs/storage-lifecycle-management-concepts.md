---
title: 管理 Azure 儲存體生命週期
description: 了解如何建立生命週期原則規則，以將過時資料從「經常性」層轉換到「非經常性」層和「封存」層。
services: storage
author: yzheng-msft
ms.service: storage
ms.topic: article
ms.date: 11/04/2018
ms.author: yzheng
ms.subservice: common
ms.openlocfilehash: 1428c2925ab57642899732bd4504b2d5b38781a8
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2019
ms.locfileid: "58315142"
---
# <a name="managing-the-azure-blob-storage-lifecycle-preview"></a>管理 Azure Blob 儲存體生命週期 (預覽)

資料集具有唯一的生命週期。 在早期的生命週期中，使用者經常會存取某些資料。 但資料的存取需求會隨著資料存在的時間越來越久而大幅降低。 有些資料在雲端維持閒置狀態，而且在儲存後就很少存取。 有些資料集會在建立後幾天或幾個月過期，而其他資料集在其生命週期內會積極地讀取及修改。 Azure Blob 儲存體生命週期管理 (預覽) 對於 GPv2 與 Blob 儲存體帳戶提供了以規則為基礎的豐富原則。 使用原則可將資料轉換到適當的存取層，或在資料的生命週期結束時過期。

生命週期管理原則可達成以下事項：

- 將 Blob 轉換到較少存取的儲存層 (經常性到非經常性、經常性到封存或非經常性到封存)，以最佳化效能和成本
- 在其生命週期結束時刪除 Blob
- 定義每天要在儲存體帳戶層級執行一次的規則
- 將規則套用至容器或 Blob 子集 (使用前置詞作為篩選)

假設有個資料集在生命週期的早期階段存取頻率很高，但兩週後偶爾才會存取。 第一個月過後，就已經很少會存取該資料集。 在這種情況下，經常性儲存層最適合早期階段。 非經常性儲存體最適合偶爾存取，而封存儲存體則是資料老化超過一個月之後的最佳階層選項。 藉由根據資料存在時間來調整儲存層，您就可以按照自己的需求設計最便宜的儲存體選項。 若要達成這項轉換，可使用生命週期管理原則規則將過時資料移至較少存取的階層。

## <a name="storage-account-support"></a>儲存體帳戶支援

生命週期管理原則適用於一般用途 v2 (GPv2) 帳戶和 Blob 儲存體帳戶。 在 Azure 入口網站中，您可以透過簡單的單鍵程序，將現有的一般用途 (GPv1) 帳戶升級為 GPv2 帳戶。 如需有關儲存體帳戶的詳細資訊，請參閱 [Azure 儲存體帳戶概觀](../common/storage-account-overview.md)。  

## <a name="pricing"></a>價格 

生命週期管理功能提供免費預覽。 客戶需針對 [List Blobs ](https://docs.microsoft.com/rest/api/storageservices/list-blobs) (列出 Blob) 和[Set Blob Tier](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) (設定 Blob 層) API 呼叫的一般作業成本支付費用。 如需定價的詳細資訊，請參閱[區塊 Blob 價格](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="register-for-preview"></a>註冊預覽版 
若要註冊公開預覽版，您必須提交要求向您的訂用帳戶註冊這項功能。 要求通常會在 72 小時內核准。 一經核准，在以下區域內的所有現有和新的 GPv2 或 Blob 儲存體帳戶均會加進該功能：美國西部 2、美國中西部、美國東部 2 和西歐。 預覽僅支援區塊 Blob。 如同大部分預覽，請不要在這項功能正式運作之前，將之用於生產的工作負載上。

若要提交要求，請執行下列 PowerShell 或 CLI 命令。

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

若要提交要求：

```powershell
Register-AzProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage 
```
您可以使用下列命令來檢查註冊核准狀態：
```powershell
Get-AzProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage
```
經過核准和正確註冊後，您提交前一個要求時，就會收到*已註冊*狀態。

### <a name="azure-cli"></a>Azure CLI

若要提交要求： 
```cli
az feature register --namespace Microsoft.Storage --name DLM
```
您可以使用下列命令來檢查註冊核准狀態：
```cli
az feature show --namespace Microsoft.Storage --name DLM
```
經過核准和正確註冊後，您提交前一個要求時，就會收到*已註冊*狀態。


## <a name="add-or-remove-a-policy"></a>新增或移除原則 

您可以使用 Azure 入口網站、[PowerShell](https://www.powershellgallery.com/packages/Az.Storage)、[Azure CLI](https://docs.microsoft.com/cli/azure/ext/storage-preview/storage/account/management-policy?view=azure-cli-latest#ext-storage-preview-az-storage-account-management-policy-create)、[REST API](https://docs.microsoft.com/rest/api/storagerp/managementpolicies/createorupdate) 或下列語言的用戶端工具，來新增、編輯或移除原則：[.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/8.0.0-preview)、[Python](https://pypi.org/project/azure-mgmt-storage/2.0.0rc3/)、[Node.js]( https://www.npmjs.com/package/azure-arm-storage/v/5.0.0)、[Ruby](https://rubygems.org/gems/azure_mgmt_storage/versions/0.16.2)。 

### <a name="azure-portal"></a>Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 選取 [所有資源]，然後選取您的儲存體帳戶。

3. 選取分到 [Blob 服務] 底下的 [生命週期管理 (預覽)]，即可檢視或變更原則。

### <a name="powershell"></a>PowerShell

```powershell
$rules = '{ ... }'

Set-AzStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName] -Policy $rules 

Get-AzStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName]
```

### <a name="azure-cli"></a>Azure CLI

```
az account set --subscription "[subscriptionName]”
az extension add --name storage-preview
az storage account management-policy show --resource-group [resourceGroupName] --account-name [accountName]
```

> [!NOTE]
> 如果您啟用儲存體帳戶的防火牆規則，可能會封鎖生命週期管理要求。 您可以提供例外狀況來解除封鎖這些要求。 如需詳細資訊，請參閱[設定防火牆和虛擬網路](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)中的＜例外狀況＞一節。

## <a name="policy"></a>原則

生命週期管理原則是 JSON 文件中的規則集合：

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "rule1",
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```


原則需要兩個參數：

| 參數名稱 | 參數類型 | 注意 |
|----------------|----------------|-------|
| version        | 以 `x.x` 表示的字串 | 預覽版本號碼為 0.5。 |
| 規則          | 規則物件的陣列 | 每個原則至少需要一項規則。 在預覽期間，每個原則最多可以指定 4 項規則。 |

在原則內的每個規則均需要三個參數：

| 參數名稱 | 參數類型 | 注意 |
|----------------|----------------|-------|
| 名稱           | 字串 | 規則名稱可包含英數字元的任意組合。 規則名稱會區分大小寫。 它在原則內必須是唯一的。 |
| type           | 列舉值 | 預覽的有效值為 `Lifecycle`。 |
| 定義     | 定義生命週期規則的物件 | 每個定義是由篩選集和動作集組成。 |

## <a name="rules"></a>規則

每個規則定義包含篩選集和動作集。 [篩選集](#rule-filters)會將規則動作限制在容器或物件名稱內的一組特定物件。 [動作集](#rule-actions)會將階層或刪除動作套用至已篩選的一組物件。

### <a name="sample-rule"></a>範例規則
以下範例規則會篩選帳戶，以只對 `container1/foo` 執行動作。 針對存在於 `container1` 內**且**開頭為 `foo` 的所有物件，會執行下列動作： 

- 在上次修改 30 天後將 Blob 分層到「非經常性」層
- 在上次修改 90 天後將 Blob 分層到「封存」層
- 上次修改 2,555 天 (七年) 後刪除 Blob
- 快照集建立 90 天後刪除 Blob 快照集

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "ruleFoo",
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
            "delete": { "daysAfterModificationGreaterThan": 2555 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}

```

### <a name="rule-filters"></a>規則篩選

篩選會將規則動作限制為儲存體帳戶內的 Blob 子集。 如果定義一個以上的篩選條件，則邏輯 `AND` 會在所有篩選器上執行。

在預覽期間，有效的篩選包括：

| 篩選名稱 | 篩選類型 | 注意 | 必要 |
|-------------|-------------|-------|-------------|
| blobTypes   | 預先定義的列舉值陣列。 | 預覽版本僅支援 `blockBlob`。 | 是 |
| prefixMatch | 要比對前置詞的字串陣列。 前置詞字串必須以容器名稱開頭。 例如，如果您想要比對下的所有 blob"https:\//myaccount.blob.core.windows.net/container1/foo/...」對於規則而言是 prefixMatch `container1/foo`。 | 如果未定義 prefixMatch，規則就會套用至帳戶內的所有 Blob。 | 否 |

### <a name="rule-actions"></a>規則動作

符合執行條件時，將動作套用至篩選的 Blob。

在預覽中，生命週期管理支援分層及刪除 Blob，也支援刪除 Blob 快照集。 在 Blob 或 Blob 快照集上每項規則至少需定義一個動作。

|  動作        | 基底 Blob                                   | 快照      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | 支援目前在經常性儲存層的 Blob         | 不支援 |
| tierToArchive | 支援目前在經常儲存性或非經常性儲存層的 Blob | 不支援 |
| delete        | 支援                                   | 支援     |

> [!NOTE]
> 如果在同一個 Blob 上定義多個動作，生命週期管理會將最便宜的動作套用至 Blob。 例如，動作 `delete` 比動作 `tierToArchive` 更便宜。 而動作 `tierToArchive` 比動作 `tierToCool` 更便宜。

在預覽中，動作執行條件會以存在時間為依據。 基底 Blob 使用上次修改時間來追蹤存在時間，而 Blob 快照集使用快照集建立時間來追蹤存在時間。

| 動作執行條件 | 條件值 | 描述 |
|----------------------------|-----------------|-------------|
| daysAfterModificationGreaterThan | 表示存在時間的整數值 (以天數為單位) | 基底 Blob 動作的有效條件 |
| daysAfterCreationGreaterThan     | 表示存在時間的整數值 (以天數為單位) | Blob 快照集動作的有效條件 | 

## <a name="examples"></a>範例
下列範例示範如何解決生命週期原則規則的常見案例。

### <a name="move-aging-data-to-a-cooler-tier"></a>將過時資料移至較少存取的階層

此範例示範了如何轉換前置詞為 `container1/foo` 或 `container2/bar` 的區塊 Blob。 該原則會將超過 30 天未修改的 Blob 轉換到非經常性儲存體，並將 90 天內未修改的 Blob 轉換到封存層：

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "agingRule",
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo", "container2/bar" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="archive-data-at-ingest"></a>封存內嵌資料 

有些資料在雲端維持閒置狀態，而且儲存後就很少存取。 將資料內嵌後，即立刻封存資料。 下列生命週期原則已設定為封存內嵌資料。 此範例會將容器 `archivecontainer` 內儲存體帳戶中的區塊 Blob 立即轉換到封存層。 您可以在上次修改時間後不到一天對 Blob 採取動作來完成立即轉換：

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "archiveRule",
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "archivecontainer" ]
        },
        "actions": {
          "baseBlob": {
              "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
          }
        }
      }
    }
  ]
}

```

### <a name="expire-data-based-on-age"></a>根據存在時間使資料過期

有些資料必須在建立後幾天或幾個月過期，以降低成本或遵守政府規定。 您可以設定生命週期管理原則，根據資料存在時間進行刪除，以便使資料過期。 下列範例所示範的原則會刪除超過 365 天的所有區塊 Blob (不指定前置詞)。

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "expirationRule",
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 365 }
          }
        }
      }
    }
  ]
}
```

### <a name="delete-old-snapshots"></a>刪除舊的快照集

針對在其生命週期內定期修改及存取的資料，通常會使用快照集來追蹤舊版資料。 您可以建立原則，根據快照集存在時間來刪除舊的快照集。 快照集存在時間是透過評估快照集建立時間來判斷。 此原則規則會將快照集建立後超過 90 天 (含) 的容器 `activedata` 內區塊 Blob 快照集刪除。

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "snapshotRule",
      "type": "Lifecycle",
    "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "activedata" ]
        },
        "actions": {
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```
## <a name="faq---i-created-a-new-policy-why-are-the-actions-not-run-immediately"></a>常見問題集：我建立了新原則，為什麼動作不會立即執行？ 

平台會每天執行一次生命週期原則。 您設定新的原則後，某些動作 (例如階層處理和刪除) 可能需要 24 小時的時間才會啟動並執行。  

## <a name="next-steps"></a>後續步驟

了解如何復原意外刪除的資料：

- [Azure 儲存體 blob 的虛刪除](../blobs/storage-blob-soft-delete.md)
