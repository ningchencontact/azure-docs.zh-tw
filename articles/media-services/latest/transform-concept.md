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
ms.openlocfilehash: d256d87548d54951cb77beffb88bba26a1a3de49
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="transforms-and-jobs"></a>轉換與工作

## <a name="overview"></a>概觀 

Azure 媒體服務 REST API (v3) 的最新版本引進了一個稱為**轉換**的全新範本化工作流程資源，適合用來編碼及/或分析視訊。 **轉換**可用來設定視訊編碼或分析的一般工作。 每個**轉換**都會描述簡單的工作流程，以便處理您的視訊或音訊檔案。 

**轉換**物件是方法，而**工作**則是實際要求 Azure 媒體服務，將**轉換**套用至指定的輸入視訊或音訊內容。 **工作**會指定輸入視訊的位置、輸出的位置等資訊。 您可以使用下各項來指定視訊的位置：HTTP(s) URL、SAS URL 或位在本機或 Azure Blob 儲存體中的檔案路徑。 Azure 媒體服務帳戶最多可以有 100 個轉換，並在轉換下提交工作。 然後您可以利用直接與 Azure 事件格線通知系統整合的「通知」來訂閱事件，例如工作狀態變更。 

由於這個 API 是由 Azure Resource Manager 驅動的，因此您可以使用 Resource Manager 範本，在您的媒體服務帳戶中建立及部署轉換。 角色型存取控制可以設定在此 API 的資源層級，讓您能鎖定對特定資源 (如轉換) 的存取。

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
