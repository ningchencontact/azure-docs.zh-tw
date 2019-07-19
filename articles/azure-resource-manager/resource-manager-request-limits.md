---
title: 要求限制和節流設定 - Azure Resource Manager
description: 描述如何在到達訂用帳戶限制時，對 Azure Resource Manager 要求使用節流。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: f457b316d9f499f2cab02452c1b03ad07a9aef27
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302821"
---
# <a name="throttling-resource-manager-requests"></a>對 Resource Manager 要求進行節流

針對每個 Azure 訂用帳戶和租用戶，Resource Manager 允許每小時最多有 12,000 個讀取要求和 1,200 個寫入要求。 這些限制的範圍是提出要求的安全性主體 (使用者或應用程式), 以及訂用帳戶識別碼或租使用者識別碼。 如果您的要求來自一個以上的安全性主體, 則訂用帳戶或租使用者的限制會大於12000和 1200 (每小時)。

要求數適用於您的訂用帳戶或您的租用戶。 訂用帳戶要求是涉及傳遞訂用帳戶識別碼的訂閱, 例如, 在您的訂用帳戶中抓取資源群組。 租用戶要求則未包含訂用帳戶識別碼，例如擷取有效的 Azure 位置。

這些限制適用於每個 Azure Resource Manager 執行個體。 每個 Azure 區域中都有多個執行個體，且 Azure Resource Manager 會部署到所有 Azure 區域。  因此，實際上的限制比這些限制還要高，因為通常是由多個不同的執行個體來服務使用者要求。

如果應用程式或指令碼到達這些限制，便需要對要求進行節流。 本文說明如何在達到限制之前判斷剩餘的要求, 以及當您到達限制時的回應方式。

當您到達限制時，您會收到 HTTP 狀態碼 **429 太多要求**。

Azure Resource Graph 會限制其作業的要求數目。 本文中的步驟可判斷剩餘的要求, 以及當達到限制時如何回應, 也適用于 Resource Graph。 不過, Resource Graph 會設定自己的限制和重設速率。 如需詳細資訊, 請參閱[Azure Resource Graph 中的節流](../governance/resource-graph/overview.md#throttling)。

## <a name="remaining-requests"></a>剩餘的要求
您可以藉由檢查回應標頭來判斷剩餘的要求數。 讀取要求會針對剩餘的讀取要求數目, 傳回標頭中的值。 寫入要求包括剩餘寫入要求數目的值。 下表描述可供檢查這些值的回應標頭︰

| 回應標頭 | 描述 |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-reads |受訂用帳戶限制的剩餘讀取。 讀取作業會傳回此值。 |
| x-ms-ratelimit-remaining-subscription-writes |受訂用帳戶限制的剩餘寫入。 寫入作業會傳回此值。 |
| x-ms-ratelimit-remaining-tenant-reads |受租用戶限制的剩餘讀取 |
| x-ms-ratelimit-remaining-tenant-writes |受租用戶限制的剩餘寫入 |
| x-ms-ratelimit-remaining-subscription-resource-requests |受訂用帳戶限制的剩餘資源類型要求。<br /><br />只有在服務已覆寫預設限制時，才會傳回此標頭值。 Resource Manager 會新增此值，而非訂用帳戶讀取或寫入。 |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |受訂用帳戶限制的剩餘資源類型集合要求。<br /><br />只有在服務已覆寫預設限制時，才會傳回此標頭值。 這個值會提供剩餘的集合要求 (列出資源) 數目。 |
| x-ms-ratelimit-remaining-tenant-resource-requests |受租用戶限制的剩餘資源類型要求。<br /><br />只有租用戶層級的要求且在服務已覆寫預設限制時，才會新增此標頭。 Resource Manager 會新增此值，而非租用戶讀取或寫入。 |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |受租用戶限制的剩餘資源類型集合要求。<br /><br />只有租用戶層級的要求且在服務已覆寫預設限制時，才會新增此標頭。 |

## <a name="retrieving-the-header-values"></a>擷取標頭值
在程式碼或指令碼中擷取這些標頭值與擷取任何標頭值並無不同。 

例如，在 **C#** 中，您可以使用下列程式碼從 **HttpWebResponse** 物件 (名為 **response**) 擷取標頭值︰

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

在 **PowerShell** 中，您可以從 Invoke-WebRequest 作業擷取標頭值。

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

如需完整的 PowerShell 範例，請參閱 [Check Resource Manager Limits for a Subscription](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI) (檢查訂用帳戶的 Resource Manager 限制)。

如果您想要查看可用於偵錯的剩餘要求，您可以在 **PowerShell** Cmdlet 提供 **-Debug** 參數。

```powershell
Get-AzResourceGroup -Debug
```

這會傳回許多值，包括下列回應值︰

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

若要取得寫入限制，請使用寫入作業： 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

這會傳回許多值，包括下列值︰

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

在 **Azure CLI** 中，您可以使用更詳細的選項擷取標頭值。

```azurecli
az group list --verbose --debug
```

這會傳回許多值，包括下列值︰

```azurecli
msrest.http_logger : Response status: 200
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Content-Encoding': 'gzip'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'Vary': 'Accept-Encoding'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '11998'
```

若要取得寫入限制，請使用寫入作業： 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

這會傳回許多值，包括下列值︰

```azurecli
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="waiting-before-sending-next-request"></a>在傳送下一個要求前先稍候
當您到達要求限制時，Resource Manager 會在標頭中傳回 **429** HTTP 狀態碼和 **Retry-After** 值。 **Retry-After** 值會指定應用程式在傳送下一個要求之前所應等待 (或睡眠) 的秒數。 如果重試值沒過您就傳送要求，則不會處理要求，並且會傳回新的重試值。

## <a name="next-steps"></a>後續步驟

* 如需完整的 PowerShell 範例，請參閱 [Check Resource Manager Limits for a Subscription](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI) (檢查訂用帳戶的 Resource Manager 限制)。
* 如需有關限制和配額的詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-subscription-service-limits.md)。
* 若要了解如何處理非同步 REST 要求，請參閱[追蹤非同步 Azure 作業 (英文)](resource-manager-async-operations.md)。
