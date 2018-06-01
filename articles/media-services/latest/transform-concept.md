---
title: Azure 媒體服務的轉換與工作 | Microsoft Docs
description: 使用媒體服務時，您需要建立一個轉換來描述處理視訊時的規則或規格。 本文提供何為轉換以及其用途的概觀。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: b755e0573098d3dbed1bea18a40af634be609f76
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/17/2018
ms.locfileid: "34272075"
---
# <a name="transforms-and-jobs"></a>轉換與工作

## <a name="overview"></a>概觀 

Azure 媒體服務 REST API (v3) 的最新版本引進了一個稱為**轉換**的全新範本化工作流程資源，適合用來編碼及/或分析視訊。 **轉換**可用來設定視訊編碼或分析的一般工作。 每個**轉換**都會描述簡單的工作流程，以便處理您的視訊或音訊檔案。 

**轉換**物件是方法，而**工作**則是實際要求 Azure 媒體服務，將**轉換**套用至指定的輸入視訊或音訊內容。 **工作**會指定輸入視訊的位置、輸出的位置等資訊。 您可以使用下各項來指定視訊的位置：HTTP(s) URL、SAS URL 或位在本機或 Azure Blob 儲存體中的檔案路徑。 Azure 媒體服務帳戶最多可以有 100 個轉換，並在轉換下提交工作。 然後您可以利用直接與 Azure 事件格線通知系統整合的「通知」來訂閱事件，例如工作狀態變更。 

由於這個 API 是由 Azure Resource Manager 驅動的，因此您可以使用 Resource Manager 範本，在您的媒體服務帳戶中建立及部署轉換。 角色型存取控制可以設定在此 API 的資源層級，讓您能鎖定對特定資源 (如轉換) 的存取。

## <a name="transform-definition"></a>轉換定義

下表顯示轉換的屬性並提供其定義。

|Name|類型|說明|
|---|---|---|
|id|字串|資源的完整資源識別碼。|
|name|字串|資源名稱。|
|properties.created |字串|轉換建立時的 UTC 日期和時間，格式為 'YYYY-MM-DDThh:mm:ssZ'。|
|properties.description |字串|轉換的詳細描述 (選擇性)。|
|properties.lastModified |字串|轉換最後更新時的 UTC 日期和時間，格式為 'YYYY-MM-DDThh:mm:ssZ'。|
|properties.outputs |TransformOutput[]|一個或多個轉換應該產生的 TransformOutputs 陣列。|
|type|字串|資源類型。|

如需完整定義，請參閱[轉換](https://docs.microsoft.com/rest/api/media/transforms)。

## <a name="job-definition"></a>工作定義

下表顯示作業的屬性並提供其定義。

|Name|類型|說明|
|---|---|---|
|id|字串|資源的完整資源識別碼。|
|name|字串|資源名稱。|
|properties.created |字串|轉換建立時的 UTC 日期和時間，格式為 'YYYY-MM-DDThh:mm:ssZ'。|
|properties.description |字串|作業的詳細描述 (選擇性)。|
|properties.lastModified |字串|轉換最後更新時的 UTC 日期和時間，格式為 'YYYY-MM-DDThh:mm:ssZ'。|
|properties.outputs |JobOutput[]:JobOutputAsset[] |作業的輸出。|
|properties.priority |優先順序 |處理作業時應依循的優先順序。 先處理優先順序較高的作業，再處理優先順序較低的工作。 如果沒有設定，預設值為一般。
|properties.state |JobState |作業的目前狀態。
|type|字串|資源類型。|

如需完整定義，請參閱[作業](https://docs.microsoft.com/rest/api/media/jobs)。

## <a name="typical-workflow-and-example"></a>一般工作流程和範例

1. 建立轉換 
2. 在這個轉換下提交工作 
3. 列出轉換 
4. 如果未來您不打算使用此「方法」，請刪除轉換。 

假設您想要以縮圖的方式擷取所有視訊的第一幀畫面，應該採取的步驟如下： 

1. 定義處理規則，例如，使用視訊第一幀畫面當作縮圖 
2. 然後，針對每個您要作為服務輸入的視訊，您可以告訴服務： 
    1. 到何處尋找該視訊，以及 
    2. 將輸出寫入何處，例如縮圖 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [串流處理視訊檔案](stream-files-dotnet-quickstart.md)
