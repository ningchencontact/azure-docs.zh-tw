---
title: 什麼是自訂語音？ 語音服務
titlesuffix: Azure Cognitive Services
description: 自訂語音是一組線上工具可讓您建立可辨識、 一個獨特的聲音您的品牌。 就能開始是少數幾個音訊檔和相關聯的轉譯。 請遵循下列連結，開始建立自訂的語音轉換文字體驗。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: 016dcf32f2f846e43362f17bc9f4627113908352
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075140"
---
# <a name="get-started-with-custom-voice"></a>開始使用自訂語音

自訂語音是一組線上工具可讓您建立可辨識、 一個獨特的聲音您的品牌。 就能開始是少數幾個音訊檔和相關聯的轉譯。 請遵循下列連結，開始建立自訂的文字轉換語音體驗。

## <a name="whats-in-custom-voice"></a>什麼是自訂的語音中？

開始之前加上自訂語音，您需要 Azure 帳戶和語音服務的訂用帳戶。 當您建立帳戶後時，您可以準備您的資料、 定型和測試您的模型、 評估語音品質，並最終部署您的自訂語音模型。

下圖會反白顯示的步驟，建立使用自訂語音入口網站的自訂語音模型。 若要了解更多使用的連結。

![自訂語音架構圖](media/custom-voice/custom-voice-diagram.png)

1.  [訂閱，並建立專案](#set-up-your-azure-account)-建立 Azure 帳戶，並建立語音服務的訂用帳戶。 此統一的訂用帳戶可讓您存取語音轉換文字、 文字轉換語音，語音翻譯，以及自訂語音入口網站。 然後，使用您的語音服務訂用帳戶，建立第一個自訂語音專案。

2.  [將資料上傳](how-to-custom-voice-create-voice.md#upload-your-datasets)-上傳資料 （「 音訊 」 和 「 文字 」） 使用自訂語音入口網站或自訂語音 API。 從入口網站中，您可以調查，並評估發音分數和雜訊訊號比率。 如需詳細資訊，請參閱 <<c0> [ 如何準備資料以自訂語音](how-to-custom-voice-prepare-data.md)。

3.  [訓練您的模型](how-to-custom-voice-create-voice.md#build-your-custom-voice-model)– 使用您的資料來建立自訂文字轉換語音的語音模型。 您可以訓練的模型以不同的語言。 定型之後, 測試您的模型，以及如果您滿意結果，您可以將模型部署。

4.  [將模型部署](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint)-建立您的文字轉換語音的語音模型的自訂端點，並將它用於您的產品、 工具和應用程式中的語音合成。

## <a name="set-up-your-azure-account"></a>設定您的 Azure 帳戶

您可以使用自訂語音入口網站來建立自訂模型之後，才需要語音服務的訂用帳戶。 請遵循這些指示來建立在 Azure 中的語音服務的訂用帳戶。 如果您沒有 Azure 帳戶，您可以註冊一個新的。  

一旦您已建立 Azure 帳戶和語音服務的訂用帳戶，您必須登入自訂語音入口網站，並連接您的訂用帳戶。

1. 從 Azure 入口網站中取得您的語音服務訂用帳戶金鑰。
2. 登入[自訂語音入口網站](https://aka.ms/custom-voice)。
3. 選取您的訂用帳戶，並建立語音的專案。
4. 如果您想要切換至另一個語音的訂用帳戶，請使用頂端導覽列中的齒輪圖示。

> [!NOTE]
> 自訂語音服務不支援 30 天免費試用版金鑰。 您必須擁有 F0 或 S0 金鑰才能使用服務，在 Azure 中建立。

## <a name="how-to-create-a-project"></a>如何建立專案

內容資料、 模型、 測試及端點會組織成像是**專案**自訂語音入口網站中。 每個專案是特定國家 （地區）/語言與您想要建立之語音的性別。 例如，您可以建立為使用英文，美國 (EN-US) 的撥接中心的聊天機器人，女性聲音的專案。

若要建立第一個專案，請選取**Text-要-Speech/自訂語音**索引標籤，然後按一下**新的專案**。 遵循精靈所提供的指示，建立您的專案。 您已建立專案之後，您會看到四個索引標籤：**資料**，**訓練**，**測試**，和**部署**。 使用提供的連結[後續步驟](#next-steps)以了解如何使用每個索引標籤。

## <a name="next-steps"></a>後續步驟

- [準備自訂語音資料](how-to-custom-voice.md)
- [建立自訂的語音](how-to-custom-voice-create-voice.md)
- [快速入門：記錄您的語音範例](record-custom-voice-samples.md)
