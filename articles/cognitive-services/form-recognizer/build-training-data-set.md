---
title: 如何建置自訂的模型-表單辨識器的定型資料集
titleSuffix: Azure Cognitive Services
description: 了解如何確保您的訓練資料集已最佳化的形式辨識器模型定型。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 643f0d6dd3ee073bd19f8697346689523032ad9f
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592652"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>建置自訂的模型定型資料集

當您使用表單辨識器的自訂模型時，讓模型能夠訓練您產業特有的表單來提供您自己的定型資料。 您可以訓練具有五個填入表單或 （在 檔案名稱必須包含 「 空白 」） 的空白表單的模型，再加上兩個已填寫的表單。 即使您有足夠填滿表單，以進行訓練，將空白的表單新增至您的訓練資料集可以改善模型的精確度。

## <a name="training-data-tips"></a>訓練資料提示

請務必使用最適合的資料集進行訓練。 使用下列秘訣，以確保您取得最佳的結果，從[定型模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel)作業：

* 可能的話，請使用以文字為基礎的 PDF 文件，而不是映像為基礎的文件。 掃描電子檔的 Pdf 會當做映像處理。
* 如果您有使用的話，請使用一個空白表單和兩個已填寫的表單。
* 對於已填寫的表單，使用只有部分填滿其欄位的範例。
* 使用不同的值，每個欄位中的表單。
* 如果您的表單映像的較低的品質，使用較大的資料集 （例如，10 至 15 影像）。

## <a name="general-input-requirements"></a>一般的輸入的需求

請確定您的訓練資料集也會遵循形式辨識器的所有內容的輸入的需求。 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>上傳您的訓練資料

當您匯集了您要用於訓練的表單文件集時，您需要將它上傳至 Azure blob 儲存體容器。 如果您不知道如何建立 Azure 儲存體帳戶與容器，請遵循[Azure 入口網站的 Azure 儲存體快速入門](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)。

### <a name="organize-your-data-in-subfolders-optional"></a>組織您的資料 （選擇性） 的子資料夾中

根據預設，[定型模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel)API 只會使用表單的文件，位於您的儲存體容器的根目錄。 不過，您可以訓練與子資料夾中的資料如果 API 呼叫中指定它。 一般來說，主體[定型模型](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel)呼叫的格式如下，其中`<SAS URL>`是您的容器的共用存取簽章 URL:

```json
{
  "source":"<SAS URL>"
}
```

如果您將下列內容加入要求主體時，API 會訓練與位於子資料夾中的文件。 `"prefix"`欄位是選擇性的會限制訓練資料集檔案之路徑的開頭指定的字串。 因此值`"Test"`，比方說，會導致 API 來看看只有檔案或資料夾的開頭為"Test"。

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  }
}
```

## <a name="next-steps"></a>後續步驟

既然您已了解如何建置訓練資料集，請依照下列快速入門來將自訂的表單辨識器模型定型並開始使用您的表單上。

* [快速入門：訓練模型，並使用 cURL 來擷取表單資料](./quickstarts/curl-train-extract.md)
* [快速入門：訓練模型，並擷取表單資料，使用 REST API 與 Python](./quickstarts/python-train-extract.md)

