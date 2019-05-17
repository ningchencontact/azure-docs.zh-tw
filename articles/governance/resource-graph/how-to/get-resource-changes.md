---
title: 取得資源變更
description: 了解如何尋找資源已變更時，並取得一份變更的屬性。
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 4e28ca15197f89caeaeaca0aabb648755b8235f1
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2019
ms.locfileid: "65551548"
---
# <a name="get-resource-changes"></a>取得資源變更

取得變更過程的每日使用情況、 重新設定和甚至是重新部署的資源。
變更可能來自個別或透過自動化程序。 大部分的變更是根據設計，但有時不。 過去 14 天的變更歷程記錄，使用 Azure 資源的圖表可讓您：

- 了解何時在 Azure Resource Manager 屬性上偵測到變更。
- 查看該變更事件中有哪些屬性變更。

變更偵測和詳細資料會有價值的下列範例案例：

- 在 事件管理，以了解_潛在_相關的變更。 查詢特定的時間範圍期間變更事件，並評估變更詳細資料。
- 保留設定管理資料庫，又稱為 CMDB，最新狀態。 而不要重新整理所有資源和其完整的屬性集的排程頻率，只會取得變更的項目。
- 了解哪些其他屬性可能已經變更，當資源變更合規性狀態。 這些額外的屬性評估可深入了解可能需要管理透過 Azure 原則定義其他屬性。

本文說明如何收集此資訊，透過圖形資源的 SDK。 若要查看 Azure 入口網站中的這項資訊，請參閱 Azure 原則[修訂歷程記錄](../../policy/how-to/determine-non-compliance.md#change-history-preview)或 Azure 活動記錄檔[修訂歷程記錄](../../../azure-monitor/platform/activity-logs-overview.md#view-change-history)。

> [!NOTE]
> 資源圖形中的變更詳細資料適用於資源管理員屬性。 追蹤虛擬機器內的變更，請參閱 Azure 自動化[變更追蹤](../../../automation/automation-change-tracking.md)或 「 Azure 原則[Vm 的客體設定](../../policy/concepts/guest-configuration.md)。

> [!IMPORTANT]
> Azure 資源的圖形中的變更歷程記錄處於公開預覽狀態。

## <a name="find-when-changes-were-detected"></a>尋找當偵測到變更

查看變更內容的資源上的第一個步驟是尋找與該時段內的資源相關的變更事件。 這個步驟透過**resourceChanges** REST 端點。

**ResourceChanges**端點需要要求主體中的兩個參數：

- **resourceId**:要尋找的變更的 Azure 資源。
- **間隔**:具有的屬性_開始_並_結束_日期，來檢查是否有變更的事件使用的時機**Zulu 時區 (Z)**。

要求本文範例：

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "interval": {
        "start": "2019-03-28T00:00:00.000Z",
        "end": "2019-03-31T00:00:00.000Z"
    }
}
```

使用上述的要求內文，REST API URI **resourceChanges**是：

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChanges?api-version=2018-09-01-preview
```

回應看起來類似此範例：

```json
{
    "changes": [{
            "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
            "beforeSnapshot": {
                "timestamp": "2019-03-29T01:32:05.993Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-29T01:54:24.42Z"
            }
        },
        {
            "changeId": "9dc352cb-b7c1-4198-9eda-e5e3ed66aec8",
            "beforeSnapshot": {
                "timestamp": "2019-03-28T10:30:19.68Z"
            },
            "afterSnapshot": {
                "timestamp": "2019-03-28T21:12:31.337Z"
            }
        }
    ]
}
```

每個偵測到變更事件**resourceId**已**changeId**是重複該資源。 雖然**changeId**字串有時候可能包含其他屬性，它具有只保證是唯一的。 變更記錄包含時間的快照集的前後。
變更事件發生在這個視窗在某個時間點的時間。

## <a name="see-what-properties-changed"></a>請參閱變更的屬性

具有**changeId**從**resourceChanges**端點**resourceChangeDetails** REST 端點，然後用來取得變更事件的細節。

**ResourceChangeDetails**端點需要要求主體中的兩個參數：

- **resourceId**:要尋找的變更的 Azure 資源。
- **changeId**:唯一的變更事件**resourceId**從收集**resourceChanges**。

要求本文範例：

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

使用上述的要求內文，REST API URI **resourceChangeDetails**是：

```http
POST https://management.azure.com/providers/Microsoft.ResourceGraph/resourceChangeDetails?api-version=2018-09-01-preview
```

回應看起來類似此範例：

```json
{
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}",
    "beforeSnapshot": {
        "timestamp": "2019-03-29T01:32:05.993Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": false,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    },
    "afterSnapshot": {
        "timestamp": "2019-03-29T01:54:24.42Z",
        "content": {
            "sku": {
                "name": "Standard_LRS",
                "tier": "Standard"
            },
            "kind": "Storage",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
            "name": "mystorageaccount",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "westus",
            "tags": {},
            "properties": {
                "networkAcls": {
                    "bypass": "AzureServices",
                    "virtualNetworkRules": [],
                    "ipRules": [],
                    "defaultAction": "Allow"
                },
                "supportsHttpsTrafficOnly": true,
                "encryption": {
                    "services": {
                        "file": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        },
                        "blob": {
                            "enabled": true,
                            "lastEnabledTime": "2018-07-27T18:37:21.8333895Z"
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "provisioningState": "Succeeded",
                "creationTime": "2018-07-27T18:37:21.7708872Z",
                "primaryEndpoints": {
                    "blob": "https://mystorageaccount.blob.core.windows.net/",
                    "queue": "https://mystorageaccount.queue.core.windows.net/",
                    "table": "https://mystorageaccount.table.core.windows.net/",
                    "file": "https://mystorageaccount.file.core.windows.net/"
                },
                "primaryLocation": "westus",
                "statusOfPrimary": "available"
            }
        }
    }
}
```

**beforeSnapshot**並**afterSnapshot**每個提供擷取快照當時的時間和屬性在該時間。 在某個時間點，這些快照集之間發生的變更。 查看上述範例中，我們可以看到已變更之屬性的已**supportsHttpsTrafficOnly**。

若要以程式設計的方式比較的結果，比較**內容**每個快照集來判斷差異的部分。 如果您比較整個快照集，**時間戳記**一律會顯示為儘管所預期的差異。

## <a name="next-steps"></a>後續步驟

- 請參閱中的使用中的語言[入門查詢](../samples/starter.md)。
- 在中使用進階，請參閱[進階查詢](../samples/advanced.md)。
- 了解如何[探索資源](../concepts/explore-resources.md)。