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
ms.date: 02/03/2019
ms.author: juliako
ms.openlocfilehash: e84f74fe4678a65a33c9cc728f290e7c905b2261
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2019
ms.locfileid: "55743730"
---
# <a name="transforms-and-jobs"></a>轉換與工作
 
使用[轉換](https://docs.microsoft.com/rest/api/media/transforms)可設定視訊編碼或分析的一般工作。 每個**轉換**都會描述配方或工作流程，以便處理您的視訊或音訊檔案。 單一轉換可套用多個規則。 例如，轉換可以指定每個視訊以指定的位元速率編碼至 MP4 檔案，並從影片第一幀畫面產生縮圖映像。 您將為每個您要加入轉換的規則新增 TransformOutput 項目。 您可以在媒體服務帳戶中使用媒體服務 v3 API 或任何已發佈的 SDK 建立轉換。 媒體服務 v3 API 是由 Azure Resource Manager 所驅動，因此您也可以使用 Resource Manager 範本，在您的媒體服務帳戶中建立及部署轉換。 角色型存取控制可用於鎖定轉換的存取權。

[轉換](https://docs.microsoft.com/rest/api/media/transforms)實體上的更新作業主要用於變更描述，或變更基礎 TransformOutputs 的優先順序。 建議您在所有進行中的工作都完成時，再執行這類更新。 如需重寫此配方，您必須建立新的轉換。

[作業](https://docs.microsoft.com/rest/api/media/jobs)是要媒體服務將已建立的**轉換**套用至指定輸入視訊或音訊內容的實際要求。 一旦建立轉換，您就可以使用媒體服務 API 或使用任何已發佈的 SDK 提交作業。 **作業**會指定輸入影片的位置、輸出的位置等資訊。 您可以使用下列項目指定輸入影片的位置：HTTPS URL、SAS URL 或[資產](https://docs.microsoft.com/rest/api/media/assets)。 您可以透過事件格線的監視事件取得作業的進度和狀態。 如需詳細資料，請參閱[使用事件格線監視事件](job-state-events-cli-how-to.md)。

[作業](https://docs.microsoft.com/rest/api/media/jobs)實體上的更新作業可用於修改「描述」，以及修改作業提交後的「優先順序」屬性。 只有在作業仍處於排入佇列的狀態時，才能有效變更「優先順序」屬性。 如果作業已開始處理，或已經處理完成，則變更優先順序不會有任何作用。

> [!NOTE]
> 屬於日期時間類型的**轉換**和**作業**屬性一律為 UTC 格式。

## <a name="typical-workflow"></a>一般工作流程

1. 建立轉換 
2. 在這個轉換下提交工作 
3. 列出轉換 
4. 如果未來您不打算使用此方法，請刪除轉換。 

### <a name="example"></a>範例

假設您想要以縮圖的方式擷取所有視訊的第一幀畫面，應該採取的步驟如下： 

1. 定義配方或影片處理規則，例如「使用視訊第一幀畫面當作縮圖」。 
2. 請告知服務每個影片的下列資訊： 
    1. 影片的所在位置，  
    2. 和寫入輸出縮圖映像的位置。 

**轉換**可協助您建立一次配方 (步驟 1)，並使用該配方提交作業 (步驟 2)。

## <a name="paging"></a>分頁

請參閱[媒體服務實體的篩選、排序、分頁](entities-overview.md)。

## <a name="next-steps"></a>後續步驟

[上傳、編碼和串流影片檔案](stream-files-tutorial-with-api.md)
