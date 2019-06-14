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
ms.date: 05/02/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ca4e343ea4774bbe4ff992ad671575b150b3c045
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65890977"
---
# <a name="developing-with-media-services-v3-apis"></a>使用媒體服務 v3 Api 進行開發

身為開發人員，您可以使用媒體服務 [REST API](https://aka.ms/ams-v3-rest-ref)，或是可讓您與 REST API 互動的用戶端程式庫，輕鬆建立、管理及維護自訂媒體工作流程。 [媒體服務 v3](https://aka.ms/ams-v3-rest-sdk) API 以 OpenAPI 規格 (先前稱為 Swagger) 作為基礎。

這篇文章會討論使用媒體服務 v3 進行開發時套用至實體和 Api 的規則。

## <a name="accessing-the-azure-media-services-api"></a>存取 Azure 媒體服務 API

在獲得存取媒體服務資源和媒體服務 API 的授權之前，您必須先進行驗證。 媒體服務支援[Azure Active Directory (Azure AD) 為基礎](../../active-directory/fundamentals/active-directory-whatis.md)驗證。 兩個常見的驗證選項如下：
 
* **服務主體驗證**-用來驗證服務 (例如： web 應用程式、 函式應用程式、 logic apps、 API 和微服務)。 通常使用這種驗證方法的應用程式有執行精靈服務、中介層服務或排程的工作的應用程式。 比方說，適用於 Web 應用程式有一律應該是中介層連接到媒體服務與服務主體。
* **使用者驗證**-用來驗證使用應用程式互動與媒體服務資源的人員。 互動式應用程式應該會先提示使用者輸入使用者的認證。 例如，授權的使用者用來監控編碼工作或即時串流的管理主控台應用程式。

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

### <a name="samples"></a>範例

請參閱下列範例示範如何使用 Azure AD 服務主體連線：

* [使用 REST 連線](media-rest-apis-with-postman.md)  
* [使用 Java 進行連線](configure-connect-java-howto.md)
* [使用 .NET 進行連線](configure-connect-dotnet-howto.md)
* [使用 Node.js 進行連線](configure-connect-nodejs-howto.md)
* [使用 Python 進行連線](configure-connect-python-howto.md)

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


## <a name="sdks"></a>SDK

> [!NOTE]
> Azure 媒體服務 v3 SDK 不一定是安全執行緒。 在開發多執行緒應用程式時，您應新增本身的執行緒同步處理邏輯以保護用戶端，或為每個執行緒使用新的 AzureMediaServicesClient 物件。 您也應留意程式碼提供給用戶端 (例如 .NET 中的 HttpClient 執行個體) 的選擇性物件所引起的多執行緒處理問題。

|SDK|參考|
|---|---|
|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[.NET 參考](https://aka.ms/ams-v3-dotnet-ref)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Java 參考](https://aka.ms/ams-v3-java-ref)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Python 參考](https://aka.ms/ams-v3-python-ref)|
|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Node.js 參考](https://aka.ms/ams-v3-nodejs-ref)| 
|[Go SDK](https://aka.ms/ams-v3-go-sdk) |[Go 參考](https://aka.ms/ams-v3-go-ref)|
|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>請參閱

- [包含媒體服務事件的 EventGrid .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [媒體服務事件的定義](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Azure 媒體服務總管

[Azure 媒體服務總管](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) 是想要了解媒體服務的 Windows 客戶可用的工具。 AMSE 是 Winforms/C# 應用程式，可利用媒體服務上傳、下載、編碼、串流 VOD 和即時內容。 AMSE 工具適用於想要測試媒體服務，而不要撰寫任何程式碼的用戶端。 AMSE 程式碼會當作資源提供給想要使用媒體服務開發的客戶。

AMSE 是一個開放原始碼專案，由社群提供支援 (可將問題回報給 https://github.com/Azure/Azure-Media-Services-Explorer/issues) 。 此專案採用 [Microsoft 開放原始碼管理辦法](https://opensource.microsoft.com/codeofconduct/)。 如需詳細資訊，請參閱[管理辦法常見問題集](https://opensource.microsoft.com/codeofconduct/faq/)，如有任何其他問題或意見請連絡 opencode@microsoft.com。

## <a name="filtering-ordering-paging-of-media-services-entities"></a>媒體服務實體的篩選、排序、分頁

請參閱[篩選、 排序、 分頁的 Azure 媒體服務實體](entities-overview.md)

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="see-also"></a>請參閱

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>後續步驟

* [連接到使用 Java 的媒體服務](configure-connect-java-howto.md)
* [連接到使用.NET 的媒體服務](configure-connect-dotnet-howto.md)
* [連線到媒體服務搭配 Node.js](configure-connect-nodejs-howto.md)
* [連線到媒體服務，使用 Python](configure-connect-python-howto.md)
