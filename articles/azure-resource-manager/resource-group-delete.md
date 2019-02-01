---
title: '刪除資源群組和資源 - Azure Resource Manager '
description: 描述刪除資源群組時，Azure Resource Manager 如何排列刪除資源的順序。 其會描述回應碼和資源管理員的處理方式，以判斷刪除作業是否成功。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2018
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: b16c67990099291381b6f11d9bd804bf4bef84e8
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2019
ms.locfileid: "55078486"
---
# <a name="azure-resource-manager-resource-group-deletion"></a>Azure Resource Manager 資源群組刪除

本文描述當您刪除資源群組時，Azure Resource Manager 如何排列刪除資源的順序。

## <a name="determine-order-of-deletion"></a>決定刪除的順序

當您刪除資源群組時，Resource Manager 會決定刪除資源的順序。 它使用下列順序：

1. 刪除所有子 (巢狀) 資源。

2. 接著刪除管理其他資源的資源。 資源可以設定 `managedBy` 屬性，指出由不同的資源來管理它。 設定此屬性時，會先刪除管理其他資源的資源，再刪除這些資源。

3. 在前兩個類別之後，刪除其餘資源。

## <a name="resource-deletion"></a>資源刪除

決定順序之後，Resource Manager 會針對每個資源發出 DELETE 作業。 它會等候任何相依性完成，再繼續進行。

針對同步作業，預期的成功回應碼為：

* 200
* 204
* 404

針對非同步作業，預期的成功回應碼為 202。 Resource Manager 會追蹤位置標頭和 azure-async 作業標頭，以判斷非同步刪除作業的狀態。
  
### <a name="errors"></a>Errors

當刪除作業傳回錯誤時，Resource Manager 會重試 DELETE 呼叫。 若狀態碼為 5xx、429 和 408，則會發生重試。 根據預設，重試的時間週期為 15 分鐘。

## <a name="after-deletion"></a>刪除後

Resource Manager 會在嘗試刪除的每個資源上發出 GET 呼叫。 此 GET 呼叫的回應必須是 404。 當 Resource Manager 取得 404 時，則刪除已成功完成。 Resource Manager 會從其快取中移除資源。

不過，如果資源上的 GET 呼叫傳回 200 或 201，Resource Manager 會重新建立資源。

### <a name="errors"></a>Errors

如果 GET 作業傳回錯誤，Resource Manager 會針對下列錯誤碼重試 GET：

* 小於 100
* 408
* 429
* 大於 500

若是其他錯誤碼，則 Resource Manager 無法刪除資源。

## <a name="next-steps"></a>後續步驟

* 若要了解 Resource Manager 概念，請參閱 [Azure Resource Manager 概觀](resource-group-overview.md)。
* 如需刪除命令，請參閱 [PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup)、[Azure CLI](/cli/azure/group?view=azure-cli-latest#az-group-delete) 和 [REST API](/rest/api/resources/resourcegroups/delete)。
