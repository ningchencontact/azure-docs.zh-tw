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
ms.date: 04/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: a336f7faae24517aa4e29c991886107c6a82d2b5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64692566"
---
# <a name="developing-with-media-services-v3-apis"></a>使用媒體服務 v3 Api 進行開發

這篇文章會討論使用媒體服務 v3 進行開發時套用至實體和 Api 的規則。

## <a name="accessing-the-azure-media-services-api"></a>存取 Azure 媒體服務 API

若要存取 Azure 媒體服務資源，您可以使用 Azure Active Directory (AD) 服務主體驗證。
媒體服務 API 要求的使用者或應用程式進行 REST API 要求媒體服務帳戶資源的存取權，並使用**參與者**或是**擁有者**角色。 API 可以使用存取**讀者**角色但只**取得**或**清單** 作業可使用。 如需詳細資訊，請參閱 <<c0> [ 媒體服務帳戶的角色型存取控制](rbac-overview.md)。

而不是建立服務主體，請考慮使用適用於 Azure 資源管理的身分識別存取媒體服務 API 透過 Azure Resource Manager。 若要深入了解適用於 Azure 資源管理的身分識別，請參閱[什麼是適用於 Azure 資源管理的身分識別](../../active-directory/managed-identities-azure-resources/overview.md)。

### <a name="azure-ad-service-principal"></a>Azure AD 服務主體 

如果您要建立的 Azure AD 應用程式和服務主體，應用程式必須在它自己的租用戶。 建立應用程式之後，指定應用程式**參與者**或是**擁有者**到媒體服務帳戶的角色存取權。 

如果您不確定是否有權限建立 Azure AD 應用程式，請參閱[必要的權限](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)。

在下圖中，數字代表依時間先後順序的要求流量：

![中介層應用程式](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. 中介層應用程式要求 Azure AD 存取權杖具有下列參數：  

   * Azure AD 租用戶端點。
   * 媒體服務資源 URI。
   * REST 媒體服務的資源 URI。
   * Azure AD 應用程式的值：用戶端識別碼和用戶端祕密。
   
   若要取得所有所需的值，請參閱[存取 Azure 媒體服務 API 使用 Azure CLI](access-api-cli-how-to.md)

2. Azure AD 存取權杖會傳送至中介層。
4. 中介層使用該 Azure AD 權杖傳送要求至 Azure 媒體 REST API。
5. 媒體服務回傳資料給中介層。

## <a name="naming-conventions"></a>命名慣例

Azure 媒體服務 v3 資源名稱 (例如資產、作業、轉換) 會受到 Azure Resource Manager 命名規則的約束。 根據 Azure Resource Manager，資源名稱永遠是唯一的。 因此，您可以對資源名稱使用任何唯一識別碼字串 (例如，GUID)。 

媒體服務資源名稱不可包含：'<'、'>'、'%'、'&'、':'、'&#92;'、'?'、'/'、'*'、'+'、'.'、單引號字元或任何控制字元。 允許所有其他字元。 資源名稱的長度上限是 260 個字元。 

如需有關 Azure Resource Manager 命名的詳細資訊，請參閱：[命名需求](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource)和[命名慣例](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。

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

## <a name="provide-feedback"></a>提供意見反應

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

[開始使用媒體服務 v3 API 使用 Sdk/工具進行開發](developers-guide.md)
