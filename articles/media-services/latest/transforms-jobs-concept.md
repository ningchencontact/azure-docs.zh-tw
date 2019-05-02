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
ms.date: 02/20/2019
ms.author: juliako
ms.openlocfilehash: 4a4f8478eef96ebe10d729e0ff380faf6f4b6779
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2019
ms.locfileid: "64937373"
---
# <a name="transforms-and-jobs"></a>轉換與工作

本主題提供有關的詳細資料[轉換](https://docs.microsoft.com/rest/api/media/transforms)並[作業](https://docs.microsoft.com/rest/api/media/jobs)，並說明這些實體之間的關聯性。 

## <a name="overview"></a>概觀 

### <a name="transformsjobs-workflow"></a>轉換/作業的工作流程

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

下圖顯示**轉換**物件和它所參考包括衍生關聯性的物件。 灰色箭號會顯示作業參考和綠色箭號顯示類別衍生關聯性類型。<br/>按一下影像可以完整大小檢視。  

<a href="./media/api-diagrams/transform-large.png" target="_blank"><img src="./media/api-diagrams/transform-small.png"></a> 

使用**轉換**可設定視訊編碼或分析的一般工作。 每個**轉換**都會描述配方或工作流程，以便處理您的視訊或音訊檔案。 單一轉換可套用多個規則。 例如，轉換可以指定每個視訊以指定的位元速率編碼至 MP4 檔案，並從影片第一幀畫面產生縮圖映像。 您將為每個您要加入轉換的規則新增 TransformOutput 項目。 您可以在媒體服務帳戶中使用媒體服務 v3 API 或任何已發佈的 SDK 建立轉換。 媒體服務 v3 API 是由 Azure Resource Manager 所驅動，因此您也可以使用 Resource Manager 範本，在您的媒體服務帳戶中建立及部署轉換。 角色型存取控制可用於鎖定轉換的存取權。

[轉換](https://docs.microsoft.com/rest/api/media/transforms)實體上的更新作業主要用於變更描述，或變更基礎 TransformOutputs 的優先順序。 建議您在所有進行中的工作都完成時，再執行這類更新。 如需重寫此配方，您必須建立新的轉換。

## <a name="jobs"></a>工作

下圖顯示**作業**物件和它所參考包括衍生關聯性的物件。<br/>按一下影像可以完整大小檢視。  

<a href="./media/api-diagrams/job-large.png" target="_blank"><img src="./media/api-diagrams/job-small.png"></a> 

**作業**是要媒體服務將已建立的**轉換**套用至指定輸入視訊或音訊內容的實際要求。 一旦建立轉換，您就可以使用媒體服務 API 或使用任何已發佈的 SDK 提交作業。 **作業**會指定輸入影片的位置、輸出的位置等資訊。 您可以使用下列項目指定輸入影片的位置：HTTPS URL、SAS URL 或[資產](https://docs.microsoft.com/rest/api/media/assets)。  

使用[作業的 HTTPS 輸入](job-input-from-http-how-to.md)如果您的內容已可透過 URL 存取，而且您不需要在 Azure 中儲存的原始程式檔 （例如，從匯入 S3）。 這個方法也是適合的如果您在 Azure Blob 儲存體中有內容，但不需要針對要在資產中的檔案。 目前，這個方法只會支援單一檔案的輸入。
 
使用[做為作業輸入資產](job-input-from-local-file-how-to.md)如果輸入的內容已經在資產中，或內容會儲存在本機檔案。 也很不錯的選項。 如果您打算發佈串流或下載 （假設您想要發佈的 mp4 下載，但也要看語音轉換文字或臉部偵測） 的輸入的資產。 這個方法支援多檔案資產 (例如，串流集在本機編碼的 MBR)。
 
您可以透過事件格線的監視事件取得作業的進度和狀態。 如需詳細資料，請參閱[使用事件格線監視事件](job-state-events-cli-how-to.md)。

[作業](https://docs.microsoft.com/rest/api/media/jobs)實體上的更新作業可用於修改「描述」，以及修改作業提交後的「優先順序」屬性。 只有在作業仍處於排入佇列的狀態時，才能有效變更「優先順序」屬性。 如果作業已開始處理，或已經處理完成，則變更優先順序不會有任何作用。

## <a name="configure-media-reserved-units"></a>設定媒體保留單位

針對由媒體服務 v3 或影片索引子觸發的音訊分析和影片分析作業，強烈建議您使用 10 個 S3 媒體保留單位 (MRU) 佈建帳戶。 如果您需要 10 個以上的 S3 MRU，請使用 [Azure 入口網站](https://portal.azure.com/)開立支援票證。

如需詳細資訊，請參閱[使用 CLI 調整媒體處理的規模](media-reserved-units-cli-how-to.md)。

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、 意見、 取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="see-also"></a>請參閱

* [錯誤碼](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
* [篩選、 排序、 分頁的媒體服務實體](entities-overview.md)

## <a name="next-steps"></a>後續步驟

- [教學課程：使用 .NET 上傳、編碼和串流影片](stream-files-tutorial-with-api.md)
- [教學課程：透過媒體服務 v3 使用 .NET 分析影片](analyze-videos-tutorial-with-api.md)
