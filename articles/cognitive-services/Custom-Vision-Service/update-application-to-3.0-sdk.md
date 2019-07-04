---
title: 如何將您的專案移轉至 3.0 API
titlesuffix: Azure Cognitive Services
description: 了解如何從舊版的 API 時使用的自訂視覺專案移轉至 3.0 API。
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: 9dd473aadd7123cafc27209f5c34322fdbcffb71
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60816446"
---
# <a name="migrate-to-the-30-api"></a>移轉至 3.0 API

自訂辨識現在已開始正式運作，並已經過 API 更新。
此更新包含一些新功能和重要的是，少數的重大變更：

* 預測 API 現在會分割成兩個根據專案類型。
* 視覺 AI 開發人員套件 (VAIDK) 匯出選項需要以特定方式建立專案。
* 預設反覆項目已移除改用 「 發佈/取消發行具名的反覆項目。

本指南將示範如何更新專案以使用新的 API 版本。 請參閱[版本資訊](release-notes.md)如需完整的變更清單。

## <a name="use-the-updated-prediction-api"></a>使用更新的預測 API

2\.x Api 會使用相同的預測呼叫，影像分類器和物件偵測器專案。 這兩種專案類型所能接受**PredictImage**並**PredictImageUrl**呼叫。 從 3.0 開始，我們必須分割此 API，因此您必須對應至呼叫的專案類型：

* 使用 **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** 並 **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** 取得映像分類專案的預測。
* 使用 **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** 並 **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** 來取得物件偵測專案的預測。

## <a name="use-the-new-iteration-publishing-workflow"></a>使用新的反覆項目發行工作流程

2\.x Api 使用的預設反覆項目或指定的反覆項目識別碼，來選擇要用於預測的反覆項目。 從 3.0 開始，我們已採用發佈流程，藉此您第一次發行的反覆項目，以指定的名稱，從訓練 API。 然後，將名稱傳遞至預測方法，來指定要使用哪一個反覆項目。

> [!IMPORTANT]
> 3\.0 Api 不會使用預設的反覆項目功能。 我們會取代舊的 Api，直到您可以繼續使用 2.x Api 切換為預設反覆項目。 這些 Api 仍會維護一段時間，而且您可以呼叫 **[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** 方法將標示為預設反覆項目。

### <a name="publish-an-iteration"></a>發行的反覆項目

一旦已定型的反覆項目，您可以使其可供使用預測 **[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** 方法。 若要發行的反覆項目，您將需要預測的資源識別碼，可在 CustomVision 網站的 [設定] 頁面取得。

![自訂視覺網站設定 頁面所述的預測資源識別碼。](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> 您也可以取得這項資訊從[Azure 入口網站](https://portal.azure.com)移至自訂辨識預測資源，並選取**屬性**。

反覆項目的發行之後，應用程式可以使用它來預測在其預測 API 呼叫中指定的名稱。 若要讓反覆項目無法預測呼叫，使用 **[UnpublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** API。

## <a name="additional-export-options"></a>其他匯出選項

使用 3.0 Api，我們會公開兩個額外匯出目標：ARM 架構與願景 AI 開發人員套件。

* 若要使用 ARM，您只需要挑選 Compact 的網域，然後選擇 DockerFile 和然後 ARM 的匯出選項為。
* 針對視覺 AI 開發人員套件，必須建立專案與__一般 (Compact)__ 網域，以及在目標中指定 VAIDK 匯出平台引數。

## <a name="next-steps"></a>後續步驟

* [訓練 API 參考文件 (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [預測 API 參考文件 (REST)](https://go.microsoft.com/fwlink/?linkid=865445)