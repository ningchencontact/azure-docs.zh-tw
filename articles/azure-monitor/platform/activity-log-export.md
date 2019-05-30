---
title: 匯出 Azure 活動記錄檔
description: 匯出 Azure 活動記錄檔至儲存體封存或 Azure 事件中樞來匯出 Azure 外部。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: acf2526e79519e610614dc5217efbfe5e327b90f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66248140"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>將 Azure 活動記錄檔匯出至儲存體或 Azure 事件中樞
[Azure 活動記錄檔](activity-logs-overview.md)可深入了解您的 Azure 訂用帳戶中發生的訂用帳戶層級事件。 除了 Azure 入口網站中檢視活動記錄檔，或將它複製到 Log Analytics 工作區，與其他 Azure 監視器所收集的資料分析，您可以建立活動記錄封存至 Azure 儲存體帳戶或串流至記錄檔設定檔 事件中樞。

## <a name="archive-activity-log"></a>封存活動記錄
封存活動記錄檔的儲存體帳戶是很有用，如果您想要保留記錄資料超過 90 天 （具有完整控制權的保留原則的詳細資訊），適用於稽核、 靜態分析或備份。 如果您只需要保留事件 90 天或更短，則不需要設定封存至儲存體帳戶，因為活動記錄事件時，會保留在 Azure 平台上，90 天。

## <a name="stream-activity-log-to-event-hub"></a>Stream 活動記錄到事件中樞
[Azure 事件中樞](/azure/event-hubs/)是資料串流平台和事件擷取服務，可接收與處理數以百萬計的每秒的事件。 傳送至事件中樞的資料可以透過任何即時分析提供者或批次/儲存體配接器來轉換和儲存。 您可能會用於活動記錄串流功能的兩種方式是：
* **串流至協力廠商記錄與遙測系統**：隨著時間進展，「Azure 事件中樞」串流會成為將「活動記錄」輸送到協力廠商 SIEM 與記錄分析解決方案的機制。
* **建置自訂遙測及記錄平台**：如果您已經有自訂建置的遙測平台或正考慮建置一個，「事件中樞」所具備的高度可調整發佈訂閱特質將可讓您靈活擷取活動記錄。 

## <a name="prerequisites"></a>必要條件

### <a name="storage-account"></a>儲存體帳戶
如果您要封存活動記錄檔，您需要[建立儲存體帳戶](../../storage/common/storage-quickstart-create-account.md)如果您還沒有其中一個。 您不應該使用具有其他非監視資料，讓您更能夠控制存取監視資料，在其中儲存的現有儲存體帳戶。 如果您也要封存診斷記錄和度量至儲存體帳戶之外，您可以選擇將所有的監視資料保留在中央位置使用該相同的儲存體帳戶。

儲存體帳戶不一定要和訂用帳戶發出記錄屬於相同的訂用帳戶，只要使用者有適當的設定可 RBAC 存取這兩個訂用帳戶即可。
> [!NOTE]
>  您目前無法將資料封存到位在安全虛擬網路後面的儲存體帳戶。

### <a name="event-hubs"></a>事件中樞
如果您要將您的活動記錄檔傳送至事件中樞，則需要[建立事件中樞](../../event-hubs/event-hubs-create.md)如果您還沒有其中一個。 如果您先前串流活動記錄事件，此事件中樞命名空間，將會重複使用該事件中樞。

共用存取原則會定義串流機制具有的權限。 串流至事件中樞需要管理、 傳送和接聽的權限。 您可以建立或修改事件中樞命名空間的事件中樞命名空間，在 Azure 入口網站中設定索引標籤底下的共用的存取原則。

若要更新活動記錄檔記錄設定檔，以加入串流，您必須具有的 ListKey 權限，該事件中樞授權規則。 事件中樞命名空間不一定要和發出記錄的訂用帳戶屬於相同的訂用帳戶，只要進行設定的使用者有這兩個訂用帳戶的適當 RBAC 存取權，而且這兩個訂用帳戶都屬於同一個 ADD 租用戶。

Stream 活動記錄到事件中樞所[建立記錄設定檔](#create-a-log-profile)。

## <a name="create-a-log-profile"></a>建立記錄檔設定檔
您定義如何將 Azure 活動記錄檔使用匯出**記錄檔設定檔**。 每個 Azure 訂用帳戶只能有一個記錄檔設定檔。 這些設定可以透過設定**匯出**入口網站中的 [活動記錄] 刀鋒視窗中的選項。 也可以[使用 Azure 監視器 REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx)、PowerShell Cmdlet 或 CLI 以程式設計方式設定。

記錄設定檔定義下列項目。

**活動記錄檔應該要傳送至何處。** 目前，可用的選項是儲存體帳戶或事件中樞。

**應該傳送何種事件分類。** 意義*分類*在記錄檔設定檔和活動記錄檔事件會不同。 在記錄設定檔*分類*代表作業類型 (Write、 Delete、 Action)。 在 活動記錄事件，*分類*"* 屬性表示的來源或類型的事件 （例如，管理、 ServiceHealth、 和警示）。

**應該要匯出哪一個區域 （位置）。** 您應該包含所有位置，因為活動記錄檔中的許多事件都是全域事件。

**活動記錄應該保留多久的儲存體帳戶。** 保留期為 0 天表示會永遠保留記錄。 否則，此值可以是 1 到 2147483647 之間的任意天數。

如果有設定保留原則，但將儲存體帳戶的儲存記錄檔已停用，保留原則會有任何作用。 保留原則是每天套用，因此在一天結束時 (UTC)，這一天超過保留原則的記錄會被刪除。 例如，如果您的保留原則為一天，在今天一開始，昨天之前的記錄會被刪除。 刪除程序會從 UTC 午夜開始，但是請注意，可能需要長達 24 小時的時間，記錄才會從您的儲存體帳戶中刪除。



> [!WARNING]
> 2018 年 11 月 1 日起，儲存體帳戶中的記錄資料格式已變更為 JSON 資料行。 [請參閱本文以了解影響的描述，以及如何更新您的工具，來處理新的格式。](diagnostic-logs-append-blobs.md)
>
>

### <a name="create-log-profile-using-the-azure-portal"></a>建立使用 Azure 入口網站的記錄設定檔

建立或編輯的記錄檔設定檔**匯出至事件中樞**在 Azure 入口網站中的選項。

1. 從**監視器**在 Azure 入口網站中，選取功能表**匯出至事件中樞**。

    ![入口網站中的匯出按鈕](media/activity-log-export/portal-export.png)

3. 在出現的刀鋒視窗中，指定下列項目：
   * 若要匯出事件的區域。 您應該選取所有區域，以確保，您就別錯過重要事件，因為活動記錄檔是全域的 （非區域性） 記錄，因此大部分的事件沒有與其相關聯的區域。 
   * 如果您想要寫入儲存體帳戶：
       * 您想要將事件儲存至儲存體帳戶。
       * 您想要保留在儲存體中的這些事件的天數。 如果設定為 0 天會永遠保留記錄。
   * 如果您想要寫入至事件中樞：
       * 服務匯流排命名空間，以您想要建立這些事件的串流事件中樞。

     ![匯出活動記錄檔刀鋒視窗](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. 按一下 [儲存]  來儲存這些設定。 这些设置将立即应用于你的订阅。


### <a name="configure-log-profile-using-powershell"></a>設定使用 PowerShell 的記錄檔設定檔

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

如果記錄設定檔已經存在，必須先移除現有的記錄設定檔，然後建立一個新。

1. 使用 `Get-AzLogProfile` 來識別記錄設定檔是否存在。  如果記錄設定檔不存在，請注意*名稱*屬性。

1. 使用 `Remove-AzLogProfile` 透過 name  屬性中的值來移除記錄設定檔。

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. 使用 `Add-AzLogProfile` 來建立新的記錄設定檔：

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | 屬性 | 必要項 | 描述 |
    | --- | --- | --- |
    | Name |是 |記錄檔設定檔的名稱。 |
    | StorageAccountId |否 |用來儲存活動記錄檔的儲存體帳戶的資源識別碼。 |
    | serviceBusRuleId |否 |服务总线命名空间（需在其中创建事件中心）的服务总线规则 ID。 這是以格式字串： `{service bus resource ID}/authorizationrules/{key name}`。 |
    | 位置 |是 |以逗號分隔的區域清單，其中列出您要收集的活動記錄檔事件的區域。 |
    | RetentionInDays |是 |事件應保留在儲存體帳戶中，介於 1 到 2147483647 之間的天數。 值為 0 會無限期地儲存記錄。 |
    | 類別 |否 |以逗號分隔的類別清單，其中列出應該收集的事件類別。 可能的值為_撰寫_，_刪除_，並_動作_。 |

### <a name="example-script"></a>範例指令碼
以下是建立活動記錄寫入儲存體帳戶 」 和 「 事件中樞的記錄檔設定檔的範例 PowerShell 指令碼。

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>設定使用 Azure CLI 的記錄檔設定檔

如果記錄設定檔已經存在，您必須先移除現有的記錄設定檔，然後再建立新的記錄設定檔。

1. 使用 `az monitor log-profiles list` 來識別記錄設定檔是否存在。
2. 使用 `az monitor log-profiles delete --name "<log profile name>` 透過 name  屬性中的值來移除記錄設定檔。
3. 使用 `az monitor log-profiles create` 來建立新的記錄設定檔：

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | 屬性 | 必要項 | 描述 |
    | --- | --- | --- |
    | name |是 |記錄檔設定檔的名稱。 |
    | storage-account-id |是 |資源識別碼，活動記錄應該要儲存至此儲存體帳戶。 |
    | 位置 |是 |以空格分隔的區域清單，其中列出您要收集的活動記錄事件的區域。 您可以使用 `az account list-locations --query [].name` 來檢視您訂用帳戶的所有區域清單。 |
    | days |是 |哪些事件應保留，介於 1 到 365 之間的日數。 值為 0 會無限期地 (永遠) 儲存記錄。  如果是零，則啟用的參數應設為 true。 |
    |已啟用 | 是 |True 或 False。  用來啟用或停用保留原則。  如果為 True，則 days 參數必須是大於 0 的值。
    | 類別 |是 |以空格分隔的類別清單，其中列出應收集的事件類別。 可能的值有 Write、Delete、Action。 |



## <a name="activity-log-schema"></a>活動記錄檔結構描述
是否傳送至 Azure 儲存體或事件中樞，將 json 寫入活動記錄資料，以下列格式。

``` JSON
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```
下表詳述此 JSON 中的項目。

| 元素名稱 | 描述 |
| --- | --- |
| time |处理与事件对应的请求的 Azure 服务生成事件时的时间戳。 |
| resourceId |受影響資源的資源識別碼。 |
| operationName |操作的名称。 |
| category |事件的類別，例如 寫入、讀取、動作。 |
| resultType |結果的類型，例如 成功、失敗、開始 |
| resultSignature |取決於資源類型。 |
| durationMs |作業的持續時間 (以毫秒為單位) |
| callerIpAddress |已執行作業的使用者的 IP 地址，根據可用性的 UPN 宣告或 SPN 宣告。 |
| correlationId |通常是字串格式的 GUID。 具有相同 correlationId、屬於同一 uber 動作的事件。 |
| identity |描述授權和宣告的 JSON blob。 |
| 授權 |事件的 RBAC 屬性的 blob。 通常包括 action、role 和 scope 屬性。 |
| 層級 |事件的層級。 下列其中一個值：_關鍵_，_錯誤_，_警告_，_參考_，以及_詳細資訊_ |
| location |在 location 發生 (或全球) 的區域。 |
| properties |描述事件詳細資料的一組 `<Key, Value>` 配對 (也就是字典)。 |

> [!NOTE]
> 屬性和這些屬性的使用方式而異的資源。



## <a name="next-steps"></a>後續步驟

* [深入了解活動記錄](../../azure-resource-manager/resource-group-audit.md)
* [收集活動記錄到 Azure 監視器記錄檔](activity-log-collect.md)
