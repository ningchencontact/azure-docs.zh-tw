---
title: 使用 Az.Search 模組-Azure 搜尋服務的 PowerShell 指令碼
description: 建立和使用 PowerShell 設定 Azure 搜尋服務。 您可以向上或向下調整服務、 管理系統管理員和查詢 api 金鑰，以及查詢系統資訊。
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: heidist
ms.openlocfilehash: 8f07468ccff4431e1afdf66aedc72599ddc0c25b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60194269"
---
# <a name="manage-your-azure-search-service-with-powershell"></a>使用 PowerShell 管理 Azure 搜尋服務
> [!div class="op_single_selector"]
> * [入口網站](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python (英文)](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

您在 Windows，Linux，或是在可以執行 PowerShell cmdlet 及指令碼[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)來建立及設定 Azure 搜尋服務。 **Az.Search**模組會擴充 Azure PowerShell] 以完整的同位檢查，以[Azure 搜尋管理 REST Api](https://docs.microsoft.com/rest/api/searchmanagement)。 使用 Azure PowerShell 並**Az.Search**，您可以執行下列工作：

> [!div class="checklist"]
> * [列出所有您的訂用帳戶中的搜尋服務](#list-search-services)
> * [取得特定的搜尋服務的相關資訊](#get-search-service-information)
> * [建立或刪除服務](#create-or-delete-a-service)
> * [重新產生系統管理 API 金鑰](#regenerate-admin-keys)
> * [建立或刪除查詢 api 金鑰](#create-or-delete-query-keys)
> * [調整服務以增加或減少複本和分割區](#scale-replicas-and-partitions)

PowerShell 無法用來變更名稱、 區域或您的服務層中。 建立服務時，會配置專用的資源。 變更基本硬體 （位置或節點類型） 需要新的服務。 沒有任何工具或 Api 將內容從某個服務傳送到另一個。 所有的內容管理都是透過[REST](https://docs.microsoft.com/rest/api/searchservice/)或是[.NET](https://docs.microsoft.com/dotnet/api/?term=microsoft.azure.search) Api，如果您想要移動索引時，您必須重新建立，並重新載入這些新的服務上。 

沒有專用的 PowerShell 命令的內容管理時，您可以撰寫 PowerShell 指令碼，呼叫 REST 或.NET 來建立和載入索引。 **Az.Search**模組本身不會提供這些作業。

不支援透過 PowerShell 或任何其他 API （入口網站-僅限） 的其他工作包括：
+ [附加認知服務資源](cognitive-search-attach-cognitive-services.md)for [AI 輔助編製索引](cognitive-search-concept-intro.md)。 認知服務附加至技能集，不是訂用帳戶或服務。
+ [附加元件的監視解決方案](search-monitor-usage.md#add-on-monitoring-solutions)或是[搜尋流量分析](search-traffic-analytics.md)用來監視 Azure 搜尋服務。

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>檢查版本，並載入模組

這篇文章中的範例皆為互動式，而且需要提高權限的權限。 Azure PowerShell ( **Az**模組) 必須安裝。 如需詳細資訊，請參閱[安裝 Azure PowerShell](/powershell/azure/overview) (英文)。

### <a name="powershell-version-check-51-or-later"></a>PowerShell 版本檢查 （5.1 或更新版本）

5.1 或更新版本，在任何支援的作業系統上，必須是本機的 PowerShell。

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>載入 Azure PowerShell

如果您不確定是否**Az**安裝，執行下列命令驗證步驟。 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

某些系統執行不自動載入模組。 如果您收到錯誤前一個命令中，嘗試載入模組，然後如果失敗，返回以查看您如果錯過某個步驟的安裝指示。

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>使用瀏覽器登入權杖連線到 Azure

您可以使用入口網站的登入認證來連接到在 PowerShell 中的訂用帳戶。 或者，您可以[非互動方式向服務主體](../active-directory/develop/howto-authenticate-service-principal-powershell.md)。

```azurepowershell-interactive
Connect-AzAccount
```

如果您擁有多個 Azure 訂用帳戶，設定您的 Azure 訂用帳戶。 如果想查看目前的訂用帳戶清單，請執行這個命令。

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

若要指定訂用帳戶，請執行下列命令。 在下列範例中，訂用帳戶的名稱為 `ContosoSubscription`。

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-all-azure-search-services-in-your-subscription"></a>列出您訂用帳戶中的所有 Azure 搜尋服務

下列命令會從[ **Az.Resources**](https://docs.microsoft.com/powershell/module/az.resources/?view=azps-1.4.0#resources)，傳回現有的資源和服務的已佈建您的訂用帳戶中的相關資訊。 如果您不知道已經建立搜尋服務的數目，這些命令會傳回該資訊之後，儲存至入口網站的某趟車程支付。

第一個命令會傳回所有的搜尋服務。

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

從服務清單中，會傳回特定資源的相關資訊。

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

結果看起來應該類似下列的輸出。

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alpha-numeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>匯入 Az.Search

從命令[ **Az.Search** ](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search)等到載入模組。

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>列出所有 Az.Search 命令

驗證步驟中，傳回模組中提供的命令清單。

```azurepowershell-interactive
Get-Command -Module Az.Search
```

結果看起來應該類似下列的輸出。

```
CommandType     Name                                Version    Source
-----------     ----                                -------    ------
Cmdlet          Get-AzSearchAdminKeyPair            0.7.1      Az.Search
Cmdlet          Get-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          Get-AzSearchService                 0.7.1      Az.Search
Cmdlet          New-AzSearchAdminKey                0.7.1      Az.Search
Cmdlet          New-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          New-AzSearchService                 0.7.1      Az.Search
Cmdlet          Remove-AzSearchQueryKey             0.7.1      Az.Search
Cmdlet          Remove-AzSearchService              0.7.1      Az.Search
Cmdlet          Set-AzSearchService                 0.7.1      Az.Search
```

## <a name="get-search-service-information"></a>取得搜尋服務資訊

在後**Az.Search**匯入，而且您知道包含您執行的搜尋服務的資源群組[Get AzSearchService](https://docs.microsoft.com/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0)傳回服務定義，包括名稱、 區域、 層和複本和資料分割計數。

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

結果看起來應該類似下列的輸出。

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : West US
Sku               : Standard
ReplicaCount      : 1
PartitionCount    : 1
HostingMode       : Default
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-or-delete-a-service"></a>建立或刪除服務

[**新 AzSearchService** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0)是用來[建立新的搜尋服務](search-create-service-portal.md)。

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName "demo-westus" -Name "my-demo-searchapp" -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3
``` 
結果看起來應該類似下列的輸出。

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Id                : /subscriptions/<alphanumeric-subscription-ID>/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 3
PartitionCount    : 3
HostingMode       : Default
Tags
```     

## <a name="regenerate-admin-keys"></a>重新產生系統管理金鑰

[**新 AzSearchAdminKey** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0)可用來透過系統管理彙[API 金鑰](search-security-api-keys.md)。 兩個管理員金鑰會建立與每個服務進行驗證式存取。 每個要求都需要索引鍵。 這兩個系統管理金鑰的功能相同的授與完整的寫入存取權的搜尋服務，擷取任何資訊，或建立並刪除任何物件的能力。 兩個索引鍵存在，因此您可以使用其中一個取代另時。 

您只能重新產生一次，指定為`primary`或`secondary`索引鍵。 不中斷的服務，請記得更新時所要使用次要金鑰更換的主索引鍵的所有用戶端程式碼。 請避免在處理過程中作業時變更金鑰。

如您所料，而不需要更新用戶端程式碼重新產生金鑰，使用舊金鑰的要求將會失敗。 重新產生所有新的金鑰不會永久鎖定您從您的服務，以及您仍然可以透過入口網站來存取服務。 重新產生主要和次要金鑰之後，您可以更新用戶端程式碼，以使用新的金鑰，並將據以繼續作業。

由服務產生 API 金鑰的值。 您無法提供使用 Azure 搜尋服務的自訂索引鍵。 同樣地，沒有使用者定義的系統管理員 API 金鑰名稱。 索引鍵的參考固定字串，或是`primary`或`secondary`。 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

結果看起來應該類似下列的輸出。 即使您只變更一次一個，則會傳回兩個索引鍵。

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>建立或刪除查詢金鑰

[**新 AzSearchQueryKey** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0)用來建立查詢[API 金鑰](search-security-api-keys.md)進行唯讀存取從用戶端應用程式到 Azure 搜尋服務索引。 查詢金鑰用來向特定的索引，以擷取搜尋結果中。 查詢金鑰不會授與其他項目上的服務，例如索引、 資料來源或索引子的唯讀存取。

您無法提供使用 Azure 搜尋服務索引鍵。 服務會產生 API 金鑰。

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>調整複本和分割區

[**設定 AzSearchService** ](https://docs.microsoft.com/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0)是用來[增加或減少複本和分割區](search-capacity-planning.md)重新調整可計費的資源，在您的服務。 增加複本或分割區將新增至您的帳單，同時具有固定且可變的費用。 如果您有暫時需要額外的處理能力，您可以增加複本和分割區處理工作負載。 在入口網站概觀 頁面的 監視 區域會對查詢延遲，每秒和節流，指出目前的容量是否有足夠的查詢中的磚。

可能需要一段時間，以新增或移除資源。 調整容量會在背景，讓現有的工作負載，以繼續進行。 額外的容量用於連入要求，因為它已完成，不需要其他設定。 

移除容量可能會造成干擾。 若要避免已卸除的要求，建議您停止所有的編製索引和索引子作業之前減少容量。 如果此方法不可行，您可能會考慮以累加的方式，減少容量一個複本和分割區一次，直到達到新的目標層級。

一旦您提交命令時，沒有任何方法來終止它透過中間。 您必須等候命令完成之前修改計數。

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

結果看起來應該類似下列的輸出。

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 6
PartitionCount    : 6
HostingMode       : Default
Id                : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```


## <a name="next-steps"></a>後續步驟

建置[index](search-what-is-an-index.md)，[查詢索引](search-query-overview.md)使用入口網站、 REST Api 或.NET SDK。

* [在 Azure 入口網站中建立 Azure 搜尋服務索引](search-create-index-portal.md)
* [設定索引子將資料從其他服務](search-indexer-overview.md)
* [在 Azure 入口網站中使用搜尋總管查詢 Azure 搜尋服務索引](search-explorer.md)
* [如何以 .NET 使用 Azure 搜尋服務](search-howto-dotnet-sdk.md)