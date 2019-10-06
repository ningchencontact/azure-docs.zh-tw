---
title: 取得資源變更
description: 瞭解如何在資源變更時尋找，並取得已變更屬性的清單。
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 2027f56d44be14895a40550d78a79d9e9dda9d97
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980306"
---
# <a name="get-resource-changes"></a>取得資源變更

資源會在每日使用、重新設定，甚至重新部署的過程中有所變更。
變更可以來自個人或自動化程式。 大部分的變更都是根據設計，但有時不會。 在過去14天的變更歷程記錄中，Azure Resource Graph 可讓您：

- 了解何時在 Azure Resource Manager 屬性上偵測到變更。
- 查看該變更事件中有哪些屬性變更。

變更偵測和詳細資料對於下列範例案例很有用：

- 在事件管理期間，瞭解_潛在_的相關變更。 在特定時間範圍內查詢變更事件，並評估變更詳細資料。
- 將設定管理資料庫（稱為 CMDB）保持在最新狀態。 並不是以排程的頻率重新整理所有資源及其完整的屬性集，而是只會取得變更的內容。
- 瞭解當資源變更合規性狀態時，其他哪些屬性可能已變更。 評估這些額外的屬性可提供其他可能需要透過 Azure 原則定義來管理的屬性的深入解析。

本文說明如何透過 Resource Graph 的 SDK 收集這項資訊。 若要在 Azure 入口網站中查看這項資訊，請參閱 Azure 原則的[變更歷程記錄](../../policy/how-to/determine-non-compliance.md#change-history-preview)或 Azure 活動記錄[變更歷程記錄](../../../azure-monitor/platform/activity-log-view.md#azure-portal)。

> [!NOTE]
> Resource Graph 中的變更詳細資料適用于 Resource Manager 屬性。 如需追蹤虛擬機器內部的變更，請參閱 Azure 自動化的[變更追蹤](../../../automation/automation-change-tracking.md)或 Azure 原則的[vm 來賓](../../policy/concepts/guest-configuration.md)設定。

> [!IMPORTANT]
> Azure Resource Graph 中的變更歷程記錄處於公開預覽狀態。

## <a name="find-when-changes-were-detected"></a>尋找偵測到變更的時間

查看資源變更的第一個步驟，是在一段時間內尋找與該資源相關的變更事件。 此步驟是透過**resourceChanges** REST 端點完成。

**ResourceChanges**端點需要要求主體中的兩個參數：

- **resourceId**：要在其中尋找變更的 Azure 資源。
- **間隔**：具有_開始_和_結束_日期的屬性，可供何時使用**祖魯時間區域（Z）** 檢查變更事件。

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

使用上述要求主體時， **resourceChanges**的 REST API URI 為：

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

**ResourceId**的每個偵測到的變更事件都有該資源獨有的**changeId** 。 雖然**changeId**字串有時可能會包含其他屬性，但它只保證是唯一的。 變更記錄包含建立快照集之前和之後的時間。
變更事件發生在此時段的某個時間點。

## <a name="see-what-properties-changed"></a>查看哪些屬性已變更

透過**resourceChanges**端點的**changeId** ，接著會使用**resourceChangeDetails** REST 端點來取得變更事件的詳細資訊。

**ResourceChangeDetails**端點需要要求主體中的兩個參數：

- **resourceId**：要在其中尋找變更的 Azure 資源。
- **changeId**：從**resourceChanges**收集的**resourceId**的唯一變更事件。

要求本文範例：

```json
{
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount",
    "changeId": "{\"beforeId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-09T00:00:00.000Z\",\"afterId\":\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"beforeTime\":'2019-05-10T00:00:00.000Z\"}"
}
```

使用上述要求主體時， **resourceChangeDetails**的 REST API URI 為：

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

**beforeSnapshot**和**afterSnapshot**會分別提供快照集的建立時間，以及當時的屬性。 這項變更會發生在這些快照集之間的某個時間點。 查看上述範例，我們可以看到變更的屬性已**supportsHttpsTrafficOnly**。

若要以程式設計方式比較結果，請比較每個快照的**內容**部分，以判斷差異。 如果您比較整個快照集，則**時間戳記**一律會顯示為差異，而不是預期。

## <a name="next-steps"></a>後續步驟

- 請參閱[入門查詢](../samples/starter.md)中使用的語言。
- 請參閱 advanced[查詢](../samples/advanced.md)中的 advanced 使用。
- 瞭解如何[探索資源](../concepts/explore-resources.md)。