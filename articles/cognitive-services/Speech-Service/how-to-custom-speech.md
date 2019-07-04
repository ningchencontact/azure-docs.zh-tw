---
title: 開始使用自訂語音-語音服務
titlesuffix: Azure Cognitive Services
description: 客製化語音是一組線上工具可讓您評估及改善您的應用程式、 工具和產品的 Microsoft 的語音轉換文字精確度。 就能開始有幾種測試音訊檔案。 請遵循下列連結，開始建立自訂的語音轉換文字體驗。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: f9b9fc0a2939f601cbddafb6ac400130e794da2b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67060875"
---
# <a name="what-is-custom-speech"></a>自訂語音是什麼？

[客製化語音](https://aka.ms/custom-speech)是一組線上工具可讓您評估及改善您的應用程式、 工具和產品的 Microsoft 的語音轉換文字精確度。 就能開始有幾種測試音訊檔案。 請遵循下列連結，開始建立自訂的語音轉換文字體驗。

## <a name="whats-in-custom-speech"></a>什麼是自訂的語音中？

您可以執行任何動作以自訂語音之前，您需要 Azure 帳戶和語音服務的訂用帳戶。 一旦帳戶後，您可以準備資料、 定型和測試您的模型、 檢查辨識品質、 評估精確度，和最終部署和使用自訂的語音轉換文字模型。

此圖中反白顯示組成自訂語音入口網站。 若要深入了解每個步驟，請使用下列連結。

![反白顯示之不同元件的自訂語音入口網站所組成。](./media/custom-speech/custom-speech-overview.png)

1. [訂閱，並建立專案](#set-up-your-azure-account)-建立 Azure 帳戶，並訂閱語音服務。 此統一的訂用帳戶可讓您存取語音轉換文字、 文字轉換語音，語音翻譯，以及自訂語音入口網站。 然後，使用您的語音服務訂用帳戶，建立第一個自訂語音專案。

2. [將測試資料上傳](how-to-custom-speech-test-data.md)-要評估 Microsoft 的語音轉換文字供應項目，為您的應用程式、 工具和產品的上傳測試資料 （音訊檔案）。

3. [檢查辨識品質](how-to-custom-speech-inspect-data.md)-使用自訂語音入口網站播放已上傳的音訊，並檢查您的測試資料的語音辨識品質。 量化度量，請參閱 <<c0> [ 檢查資料](how-to-custom-speech-inspect-data.md)。

4. [評估精確度](how-to-custom-speech-evaluate-data.md)-評估語音轉換文字模型的精確度。 自訂語音入口網站會提供*Word 錯誤率*，這可以用來判斷是否需要額外的訓練。 如果您滿意的精確度，您可以直接使用語音服務 Api。 如果您想要改善精確度相對的平均的 5%-20%，使用**訓練**入口網站上傳另有其他訓練資料，例如人類看得標示的文字記錄和相關的文字方塊中的索引標籤。

5. [定型模型](how-to-custom-speech-train-model.md)-藉由提供書面文字記錄 （10 到 1,000 個小時） 改善您的語音轉換文字模型的精確度，以及相關的文字 (< 200MB) 以及音訊測試資料。 這項資料可協助語音轉換文字模型定型。 在訓練之後重新測試，如果您滿意結果，您可以部署您的模型。

6. [將模型部署](how-to-custom-speech-deploy-model.md)-建立您的語音轉換文字模型的自訂端點，並在您的應用程式、 工具或產品中使用它。

## <a name="set-up-your-azure-account"></a>設定您的 Azure 帳戶

您可以使用自訂語音入口網站來建立自訂模型之後，才需要語音服務的訂用帳戶。 請遵循這些指示來建立標準的語音服務訂用帳戶：[建立語音訂閱](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-azure-account)。

> [!NOTE]
> 請務必建立標準 (S0) 訂用帳戶，免費試用 (F0) 訂用帳戶不支援。

一旦您已建立 Azure 帳戶和語音服務的訂用帳戶，您必須登入自訂語音入口網站，並連接您的訂用帳戶。

1. 從 Azure 入口網站中取得您的語音服務訂用帳戶金鑰。
2. 登入[自訂語音入口網站](https://aka.ms/custom-speech)。
3. 選取您要處理，並建立語音專案的訂用帳戶。
4. 如果您想要修改您的訂用帳戶，使用**齒輪**圖示位在頂端導覽列中。

## <a name="how-to-create-a-project"></a>如何建立專案

內容資料、 模型、 測試及端點會組織成像是**專案**自訂語音入口網站中。 每個專案是網域和國家 （地區）/語言特有的。 例如，您可以建立專案，以供話務中心使用美國的英文版。

若要建立第一個專案，請選取**語音以文字/自訂語音**，然後按一下**新的專案**。 遵循精靈所提供的指示，建立您的專案。 您已建立專案之後，您應該會看到四個索引標籤：**資料**，**測試**，**訓練**，和**部署**。 使用提供的連結[後續步驟](#next-steps)以了解如何使用每個索引標籤。

## <a name="next-steps"></a>後續步驟

* [準備和測試您的資料](how-to-custom-speech-test-data.md)
* [檢查您的資料](how-to-custom-speech-inspect-data.md)
* [評估您的資料](how-to-custom-speech-evaluate-data.md)
* [訓練您的模型](how-to-custom-speech-train-model.md)
* [將模型部署](how-to-custom-speech-deploy-model.md)
