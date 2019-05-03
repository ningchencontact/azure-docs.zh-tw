---
title: 針對自訂語音-語音服務檢查資料品質
titlesuffix: Azure Cognitive Services
description: 客製化語音會提供工具，可讓您以視覺化方式檢查辨識模型的品質，藉由比較與對應的辨識結果的音訊資料。 從自訂語音入口網站中，您可以播放已上傳的音訊，並判斷提供的辨識結果是否正確。  這項工具可讓您快速檢查 Microsoft 的基準語音轉換文字模型的訓練自訂模型的品質，而不必謄寫，進而任何音訊資料。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 682f3df362a7fbb0e95a07aa8a8f3a068367eef2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026712"
---
# <a name="inspect-custom-speech-data"></a>檢查自訂語音資料

> [!NOTE]
> 本頁面假設您已閱讀[準備測試資料的自訂語音](how-to-custom-speech-test-data.md)並已上傳的資料集進行檢查。

客製化語音會提供工具，可讓您以視覺化方式檢查辨識模型的品質，藉由比較與對應的辨識結果的音訊資料。 從自訂語音入口網站中，您可以播放已上傳的音訊，並判斷提供的辨識結果是否正確。 這項工具可讓您快速檢查 Microsoft 的基準語音轉換文字模型的訓練自訂模型的品質，而不必謄寫，進而任何音訊資料。

在本文件中，您將學習如何以視覺化方式檢查模型，使用您先前上傳的定型資料的品質。

在此頁面上，您將了解如何以視覺化方式檢查 Microsoft 的基準模型，語音轉換文字及/或您已訓練過的自訂模型的品質。 您會使用您上傳至資料**資料**的測試 索引標籤。

## <a name="create-a-test"></a>建立測試

請遵循下列指示來建立測試：

1. 瀏覽至**語音轉換文字 > 客製化語音 > 測試**。
2. 按一下 **加入測試**。
3. 選取 **檢查品質 （僅限音訊的資料）**。 為測試的名稱、 描述，然後選取 音訊資料集。
4. 選取您想要測試的最多兩個模型。
5. 按一下頁面底部的 [新增] 。

已成功建立測試之後，您可以比較模型並排顯示。

## <a name="side-by-side-model-comparisons"></a>並排顯示模型比較

測試狀態何時*Succeeded*，按一下以查看詳細資料，測試的測試項目名稱。 此詳細資料頁面會列出您的資料集，指出兩個模型，以及從提交的資料集轉譯的辨識結果中的所有談話。

為了檢查並排顯示比較，您可以切換包括插入、 刪除和替代的各種錯誤類型。 藉由接聽音訊，並比較每個資料行 （顯示人類看得標示為轉譯及兩個語音轉換文字模型的結果） 中的辨識結果，您可以決定哪一個模型符合您的需求，以及需要改進的地方。

檢查品質測試適合驗證的語音辨識端點品質是否足夠的應用程式。  目標量值的精確度，需要轉譯的音訊，請依照下列測試中的指示：評估精確度。

## <a name="next-steps"></a>後續步驟

* [評估您的資料](how-to-custom-speech-evaluate-data.md)
* [訓練您的模型](how-to-custom-speech-train-model.md)
* [將模型部署](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>其他資源

* [準備測試資料的自訂語音](how-to-custom-speech-test-data.md)
