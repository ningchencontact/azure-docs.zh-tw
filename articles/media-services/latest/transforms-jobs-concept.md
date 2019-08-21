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
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: 466ab0737aa5af40bd1bc137b98ab57a48feafde
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637345"
---
# <a name="transforms-and-jobs"></a>轉換與工作

本主題提供有關[轉換](https://docs.microsoft.com/rest/api/media/transforms)和[作業](https://docs.microsoft.com/rest/api/media/jobs)的詳細資料, 並說明這些實體之間的關聯性。 

## <a name="overview"></a>總覽 

### <a name="transformsjobs-workflow"></a>轉換/作業工作流程

下列圖表顯示轉換/作業工作流程。

![轉換](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>一般工作流程

1. 建立轉換 
2. 在這個轉換下提交工作 
3. 列出轉換 
4. 如果未來您不打算使用此方法，請刪除轉換。 

#### <a name="example"></a>範例

假設您想要以縮圖的方式擷取所有視訊的第一幀畫面，應該採取的步驟如下： 

1. 定義配方或影片處理規則，例如「使用視訊第一幀畫面當作縮圖」。 
2. 請告知服務每個影片的下列資訊： 
    1. 影片的所在位置，  
    2. 和寫入輸出縮圖映像的位置。 

**轉換**可協助您建立一次配方 (步驟 1)，並使用該配方提交作業 (步驟 2)。

> [!NOTE]
> 屬於日期時間類型的**轉換**和**作業**屬性一律為 UTC 格式。

## <a name="transforms"></a>轉換

使用**轉換**可設定視訊編碼或分析的一般工作。 每個**轉換**都會描述配方或工作流程，以便處理您的視訊或音訊檔案。 單一轉換可套用多個規則。 例如，轉換可以指定每個視訊以指定的位元速率編碼至 MP4 檔案，並從影片第一幀畫面產生縮圖映像。 您將為每個您要加入轉換的規則新增 TransformOutput 項目。 您可以使用 [預設值] 來告知轉換輸入媒體檔案的處理方式。

### <a name="viewing-schema"></a>正在查看架構

在媒體服務 v3 中, 預設為 API 本身的強型別實體。 您可以在[OPEN API 規格 (或 Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)中找到這些物件的「架構」定義。 您也可以在[REST API](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)、 [.net SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (或其他媒體服務 v3 SDK 參考檔) 中, 查看預設的定義 (例如**StandardEncoderPreset**)。

### <a name="creating-transforms"></a>建立轉換

您可以使用 REST、CLI, 或使用任何已發佈的 Sdk 來建立轉換。 媒體服務 v3 API 是由 Azure Resource Manager 所驅動，因此您也可以使用 Resource Manager 範本，在您的媒體服務帳戶中建立及部署轉換。 角色型存取控制可用於鎖定轉換的存取權。

### <a name="updating-transforms"></a>正在更新轉換

如果您需要更新[轉換](https://docs.microsoft.com/rest/api/media/transforms), 請使用**更新**作業。 其目的是要變更描述或基礎 Transformoutputs 優先順序的優先順序。 建議您在所有進行中的工作都完成時，再執行這類更新。 如果您想要重寫配方, 您必須建立新的轉換。

### <a name="transform-object-diagram"></a>轉換物件圖表

下圖顯示**轉換**物件以及它所參考的物件, 包括衍生關聯性。 灰色箭號顯示作業所參考的類型, 而綠色箭號則顯示類別衍生關聯性。<br/>按一下影像可以完整大小檢視。  

<a href="./media/api-diagrams/transform-large.png" target="_blank"><img src="./media/api-diagrams/transform-small.png"></a> 

## <a name="jobs"></a>工作 (Job)

**作業**是要媒體服務將已建立的**轉換**套用至指定輸入視訊或音訊內容的實際要求。 一旦建立轉換，您就可以使用媒體服務 API 或使用任何已發佈的 SDK 提交作業。 **作業**會指定輸入影片的位置、輸出的位置等資訊。 您可以使用下列項目指定輸入影片的位置：HTTPS URL、SAS URL 或[資產](https://docs.microsoft.com/rest/api/media/assets)。  

### <a name="job-input-from-https"></a>來自 HTTPS 的作業輸入

如果您的內容已透過 URL 存取, 而且您不需要將來源檔案儲存在 Azure 中 (例如, 從 S3 匯入), 請使用[來自 HTTPS 的作業輸入](job-input-from-http-how-to.md)。 如果您有 Azure Blob 儲存體中的內容, 但不需要將檔案儲存在資產中, 則此方法也適用。 目前, 這個方法僅支援輸入的單一檔案。

### <a name="asset-as-job-input"></a>資產作為工作輸入

如果輸入內容已在資產中, 或內容儲存在本機檔案中, 請使用[資產做為工作輸入](job-input-from-local-file-how-to.md)。 如果您打算發佈輸入資產進行串流處理或下載, 這也是個不錯的選項 (假設您想要發佈可供下載的的, 但也想要進行語音轉換文字或臉部偵測)。 這個方法支援多檔案資產 (例如, 在本機編碼的 MBR 串流集)。

### <a name="checking-job-progress"></a>正在檢查工作進度

您可以透過事件格線的監視事件取得作業的進度和狀態。 如需詳細資料，請參閱[使用事件格線監視事件](job-state-events-cli-how-to.md)。

### <a name="updating-jobs"></a>正在更新作業

[作業](https://docs.microsoft.com/rest/api/media/jobs)實體上的更新作業可用於修改「描述」，以及修改作業提交後的「優先順序」屬性。 只有在作業仍處於排入佇列的狀態時，才能有效變更「優先順序」屬性。 如果作業已開始處理，或已經處理完成，則變更優先順序不會有任何作用。

### <a name="job-object-diagram"></a>工作物件圖表

下圖顯示**工作**物件和它所參考的物件, 包括衍生關聯性。<br/>按一下影像可以完整大小檢視。  

<a href="./media/api-diagrams/job-large.png" target="_blank"><img src="./media/api-diagrams/job-small.png"></a> 

## <a name="configure-media-reserved-units"></a>設定媒體保留單位

針對由媒體服務 v3 或影片索引子觸發的音訊分析和影片分析作業，強烈建議您使用 10 個 S3 媒體保留單位 (MRU) 佈建帳戶。 如果您需要 10 個以上的 S3 MRU，請使用 [Azure 入口網站](https://portal.azure.com/)開立支援票證。

如需詳細資訊，請參閱[使用 CLI 調整媒體處理的規模](media-reserved-units-cli-how-to.md)。

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="see-also"></a>另請參閱

* [錯誤碼](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
* [媒體服務實體的篩選、排序、分頁](entities-overview.md)

## <a name="next-steps"></a>後續步驟

- 在您開始開發之前, 請先參閱[使用媒體服務 V3 api 進行開發](media-services-apis-overview.md)(包含存取 api、命名慣例等的資訊)。
- 查看下列教學課程:

    - [教學課程：編碼以 URL 為基礎的遠端檔案和串流影片](stream-files-tutorial-with-rest.md)
    - [教學課程：上傳、編碼和串流影片](stream-files-tutorial-with-api.md)
    - [教學課程：使用媒體服務 v3 分析影片](analyze-videos-tutorial-with-api.md)
