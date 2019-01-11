---
title: Azure 媒體服務的轉換與工作 | Microsoft Docs
description: 使用媒體服務時，您需要建立一個轉換來描述處理視訊時的規則或規格。 本文提供何為轉換以及其用途的概觀。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 12/20/2018
ms.author: juliako
ms.openlocfilehash: 95079813cf3ade41d17393168116e4767ca26e99
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742774"
---
# <a name="transforms-and-jobs"></a>轉換與工作
 
使用[轉換](https://docs.microsoft.com/rest/api/media/transforms)可設定視訊編碼或分析的一般工作。 每個**轉換**都會描述配方或工作流程，以便處理您的視訊或音訊檔案。 

[作業](https://docs.microsoft.com/rest/api/media/jobs)是要媒體服務將已建立的**轉換**套用至指定輸入視訊或音訊內容的實際要求。 **作業**會指定輸入影片的位置、輸出的位置等資訊。 您可以使用下列項目指定輸入影片的位置：HTTPS URL、SAS URL 或[媒體服務資產](https://docs.microsoft.com/rest/api/media/assets)。  

## <a name="typical-workflow"></a>一般工作流程

1. 建立轉換 
2. 在這個轉換下提交工作 
3. 列出轉換 
4. 如果未來您不打算使用此方法，請刪除轉換。 

假設您想要以縮圖的方式擷取所有視訊的第一幀畫面，應該採取的步驟如下： 

1. 定義配方或影片處理規則，例如「使用視訊第一幀畫面當作縮圖」。 
2. 請告知服務每個影片的下列資訊： 
    1. 影片的所在位置，  
    2. 和寫入輸出縮圖映像的位置。 

**轉換**可協助您建立一次配方 (步驟 1)，並使用該配方提交作業 (步驟 2)。

## <a name="transforms"></a>轉換

您可以在媒體服務帳戶中直接使用 v3 API 或任何已發佈的 SDK 建立轉換。 媒體服務 v3 API 是由 Azure Resource Manager 所驅動，因此您也可以使用 Resource Manager 範本，在您的媒體服務帳戶中建立及部署轉換。 角色型存取控制可用於鎖定轉換的存取權。

### <a name="transform-definition"></a>轉換定義

下表顯示轉換的屬性並提供其定義。

|Name|說明|
|---|---|
|id|資源的完整資源識別碼。|
|name|資源名稱。|
|properties.created |轉換建立時的 UTC 日期和時間，格式為 'YYYY-MM-DDThh:mm:ssZ'。|
|properties.description |轉換的詳細描述 (選擇性)。|
|properties.lastModified |轉換最後更新時的 UTC 日期和時間，格式為 'YYYY-MM-DDThh:mm:ssZ'。|
|properties.outputs |一個或多個轉換應該產生的 TransformOutputs 陣列。|
|type|資源類型。|

如需完整定義，請參閱[轉換](https://docs.microsoft.com/rest/api/media/transforms)。

如前文所述，轉換可協助您建立配方或影片處理規則。 單一轉換可套用多個規則。 例如，轉換可以指定每個視訊以指定的位元速率編碼至 MP4 檔案，並從影片第一幀畫面產生縮圖映像。 您將為每個您要加入轉換的規則新增 TransformOutput 項目。

> [!NOTE]
> 雖然轉換定義支援更新作業，您應謹慎確保所有進行中的作業在您進行修改前均已完成。 一般而言，您將更新現有的轉換，以變更說明或修改基礎 TransformOutputs 的優先順序。 如需重寫此配方，請建立新的轉換。

## <a name="jobs"></a>工作

一旦建立轉換，您就可以使用媒體服務 API 或使用任何已發佈的 SDK 提交作業。 您可以透過事件格線的監視事件取得作業的進度和狀態。 如需詳細資料，請參閱[使用事件格線監視事件](job-state-events-cli-how-to.md )。

### <a name="jobs-definition"></a>作業定義

下表顯示作業的屬性並提供其定義。

|Name|說明|
|---|---|
|id|資源的完整資源識別碼。|
|name   |資源名稱。|
|properties.correlationData |客戶提供的相互關聯資料 (固定) 將傳回，作為作業的一部份和 JobOutput 狀態變更通知。 如需詳細資料，請參閱[事件結構描述](media-services-event-schemas.md)<br/><br/>屬性也可以用於媒體服務的多租用戶使用量。 您可以將租用戶 ID 加入作業。 |
|properties.created |作業建立時的 UTC 日期和時間，格式為 'YYYY-MM-DDThh:mm:ssZ'。|
|properties.description |選擇性的客戶提供作業描述。|
|properties.input|作業輸入。|
|properties.lastModified    |作業最後更新時的 UTC 日期和時間，格式為 'YYYY-MM-DDThh:mm:ssZ'。|
|properties.outputs|作業的輸出。|
|properties.priority    |處理作業時應依循的優先順序。 先處理優先順序較高的作業，再處理優先順序較低的工作。 如果沒有設定，預設值為一般。|
|properties.state   |作業的目前狀態。|
|type   |資源類型。|

如需完整定義，請參閱[作業](https://docs.microsoft.com/rest/api/media/jobs)。

> [!NOTE]
> 雖然作業定義支援更新作業，在作業提交後只有說明和優先順序可以進行修改。 此外，只有在作業仍處於排入佇列的狀態時，才能有效變更優先順序。 如果作業已開始處理，或已經處理完成，則變更優先順序不會有任何作用。

### <a name="pagination"></a>分頁

媒體服務 v3 支援分頁作業。

> [!TIP]
> 您應一律使用「下一頁」連結來列舉集合，而不應依存於特定頁面大小。

如果查詢回應包含許多項目，服務會傳回 "\@odata.nextLink" 屬性，以取得下一頁的結果。 這可用來逐頁查看整個結果集。 您無法設定頁面大小。 

如果逐頁查看集合時，有作業建立或刪除，則所做的變更會反映在傳回的結果中 (如果這些變更屬於尚未下載的集合)。 

下列 C# 範例會示範如何列舉帳戶中的作業。

```csharp            
List<string> jobsToDelete = new List<string>();
var pageOfJobs = client.Jobs.List(config.ResourceGroup, config.AccountName, "Encode");

bool exit;
do
{
    foreach (Job j in pageOfJobs)
    {
        jobsToDelete.Add(j.Name);
    }

    if (pageOfJobs.NextPageLink != null)
    {
        pageOfJobs = client.Jobs.ListNext(pageOfJobs.NextPageLink);
        exit = false;
    }
    else
    {
        exit = true;
    }
}
while (!exit);

```

如需 REST 範例，請參閱[資產 - 清單](https://docs.microsoft.com/rest/api/media/jobs/list)


## <a name="next-steps"></a>後續步驟

[串流處理視訊檔案](stream-files-dotnet-quickstart.md)
