---
title: 開始使用自訂語音語音服務
titleSuffix: Azure Cognitive Services
description: 「自訂語音」是一組線上工具, 可讓您評估及改善應用程式、工具和產品的語音轉換文字準確度。 開始使用是幾個測試音訊檔案。 請遵循下列連結, 開始建立自訂的語音轉換文字體驗。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: a5ced51eabe708dc7883fc91e5b6fa67f9211381
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559640"
---
# <a name="what-is-custom-speech"></a>什麼是自訂語音？

「[自訂語音](https://aka.ms/custom-speech)」是一組線上工具, 可讓您針對應用程式、工具和產品評估及改善 Microsoft 的語音轉換文字準確度。 開始使用是幾個測試音訊檔案。 請遵循下列連結, 開始建立自訂的語音轉換文字體驗。

## <a name="whats-in-custom-speech"></a>自訂語音有哪些功能？

您需要有 Azure 帳戶和語音服務訂用帳戶, 才能使用自訂語音進行任何動作。 一旦您擁有帳戶, 就可以準備您的資料、訓練和測試您的模型、檢查辨識品質、評估精確度, 最後部署和使用自訂的語音轉換文字模型。

此圖重點說明組成自訂語音入口網站的部分。 若要深入瞭解每個步驟, 請使用下列連結。

![反白顯示組成自訂語音入口網站的不同元件。](./media/custom-speech/custom-speech-overview.png)

1. [訂閱並建立專案](#set-up-your-azure-account)-建立 Azure 帳戶並訂閱語音服務。 此整合訂閱可讓您存取語音轉換文字、文字轉換語音、語音翻譯和自訂語音入口網站。 然後, 使用您的語音服務訂用帳戶, 建立您的第一個自訂語音專案。

2. [上傳測試資料](how-to-custom-speech-test-data.md)-上傳測試資料 (音訊檔案), 為您的應用程式、工具和產品評估 Microsoft 的語音轉換文字供應專案。

3. [檢查辨識品質](how-to-custom-speech-inspect-data.md)-使用自訂語音入口網站播放上傳的音訊, 並檢查測試資料的語音辨識品質。 如需量化度量, 請參閱[檢查資料](how-to-custom-speech-inspect-data.md)。

4. [評估精確度](how-to-custom-speech-evaluate-data.md)-評估語音轉換文字模型的精確度。 自訂語音入口網站會提供一個*文字*錯誤率, 可用來判斷是否需要額外的訓練。 如果您對正確性感到滿意, 可以直接使用語音服務 Api。 如果您想要以 5%-20% 的相對平均來改善精確度, 請使用入口網站中的 [**定型**] 索引標籤上傳其他訓練資料, 例如人為標記的文字記錄和相關的文字。

5. [將模型定型](how-to-custom-speech-train-model.md)-藉由提供撰寫的文字記錄 (10-1000 小時) 和相關文字 (< 200 MB) 以及您的音訊測試資料, 來改善語音轉換文字模型的精確度。 這種資料有助於訓練語音轉換文字模型。 定型之後, 請重新測試, 如果您對結果感到滿意, 可以部署您的模型。

6. [部署模型](how-to-custom-speech-deploy-model.md)-針對您的語音轉換文字模型建立自訂端點, 並在您的應用程式、工具或產品中使用它。

## <a name="set-up-your-azure-account"></a>設定您的 Azure 帳戶

必須要有語音服務訂用帳戶, 才能使用自訂語音入口網站來建立自訂模型。 請依照下列指示來建立標準語音服務訂用帳戶:[建立語音訂用](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-azure-account)帳戶。

> [!NOTE]
> 請務必建立標準 (S0) 訂閱, 不支援免費試用 (F0) 訂用帳戶。

建立 Azure 帳戶和語音服務訂用帳戶之後, 您必須登入自訂語音入口網站並聯機您的訂用帳戶。

1. 從 Azure 入口網站取得您的語音服務訂用帳戶金鑰。
2. 登入[自訂語音入口網站](https://aka.ms/custom-speech)。
3. 選取您需要使用的訂用帳戶, 並建立語音專案。
4. 如果您想要修改訂用帳戶, 請使用位於頂端導覽中的**齒輪**圖示。

## <a name="how-to-create-a-project"></a>如何建立專案

像是資料、模型、測試和端點等內容, 會組織成自訂語音入口網站中的**專案**。 每個專案都是網域和國家/地區所特有。 例如, 您可以針對在美國使用英文的撥接中心建立專案。

若要建立您的第一個專案, 請選取 [**語音轉換文字]/[自訂語音**], 然後按一下 [**新增專案**]。 依照 wizard 提供的指示來建立您的專案。 建立專案之後, 您應該會看到四個索引標籤:**資料**、**測試**、**定型**和**部署**。 使用[後續步驟](#next-steps)中提供的連結, 以瞭解如何使用每個索引標籤。

## <a name="next-steps"></a>後續步驟

* [準備和測試您的資料](how-to-custom-speech-test-data.md)
* [檢查您的資料](how-to-custom-speech-inspect-data.md)
* [評估您的資料](how-to-custom-speech-evaluate-data.md)
* [訓練您的模型](how-to-custom-speech-train-model.md)
* [部署您的模型](how-to-custom-speech-deploy-model.md)
