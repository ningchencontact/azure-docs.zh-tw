---
title: 使用 v3 Api-Azure 進行開發 |Microsoft Docs
description: 這篇文章會討論使用媒體服務 v3 進行開發時套用至實體和 Api 的規則。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 18b72ceaee0ca0747a0bf2144d5f9ffddbee8b8c
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/11/2019
ms.locfileid: "59492136"
---
# <a name="developing-with-media-services-v3-apis"></a>使用媒體服務 v3 Api 進行開發

這篇文章會討論使用媒體服務 v3 進行開發時套用至實體和 Api 的規則。

## <a name="naming-conventions"></a>命名慣例

Azure 媒體服務 v3 資源名稱 (例如資產、作業、轉換) 會受到 Azure Resource Manager 命名規則的約束。 根據 Azure Resource Manager，資源名稱永遠是唯一的。 因此，您可以對資源名稱使用任何唯一識別碼字串 (例如，GUID)。 

媒體服務資源名稱不可包含：'<'、'>'、'%'、'&'、':'、'&#92;'、'?'、'/'、'*'、'+'、'.'、單引號字元或任何控制字元。 允許所有其他字元。 資源名稱的長度上限是 260 個字元。 

如需有關 Azure Resource Manager 命名的詳細資訊，請參閱：[命名需求](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource)和[命名慣例](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。

## <a name="v3-api-design-principles-and-rbac"></a>v3 API 設計原則和 RBAC

v3 API 的金鑰設計原則之一，是讓 API 更為安全。 v3 Api 不會傳回密碼或認證上**取得**或是**清單**作業。 回應中的金鑰一律為 Null、空白或處理過的。 使用者需要呼叫個別的動作方法，以取得祕密或認證。 **讀取器**角色不能呼叫作業，使其無法呼叫 Asset.ListContainerSas，StreamingLocator.ListContentKeys，ContentKeyPolicies.GetPolicyPropertiesWithSecrets 等作業。 擁有個別的動作，可讓您設定更細微的 RBAC 安全性權限，如有需要的自訂角色。

如需詳細資訊，請參閱

- [內建角色定義](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)
- [使用 RBAC 管理存取](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [媒體服務帳戶的角色型存取控制](rbac-overview.md)
- [取得內容的索引鍵原則-.NET](get-content-key-policy-dotnet-howto.md)。

## <a name="long-running-operations"></a>長時間執行的作業

將作業標示`x-ms-long-running-operation`Azure 媒體服務中[swagger 檔案](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json)是長時間執行的作業。 

如需如何追蹤非同步 Azure 作業的詳細資訊，請參閱 <<c0> [ 非同步作業](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation)。

媒體服務有下列的長時間執行作業：

* 建立 LiveEvent
* 更新 LiveEvent
* 刪除 LiveEvent
* Start LiveEvent
* 停止 LiveEvent
* 重設 LiveEvent
* 建立 LiveOutput
* Delete LiveOutput
* 建立 StreamingEndpoint
* 更新 StreamingEndpoint
* 刪除 StreamingEndpoint
* 啟動 StreamingEndpoint
* 停止 StreamingEndpoint
* 調整 StreamingEndpoint

## <a name="filtering-ordering-paging-of-media-services-entities"></a>媒體服務實體的篩選、排序、分頁

請參閱[篩選、 排序、 分頁的 Azure 媒體服務實體](entities-overview.md)

## <a name="next-steps"></a>後續步驟

[使用 SDK/工具透過媒體服務 v3 API 開始進行開發](developers-guide.md)
