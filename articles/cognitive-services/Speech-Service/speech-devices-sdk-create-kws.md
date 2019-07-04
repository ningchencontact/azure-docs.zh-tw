---
title: 建立自訂的喚醒字 - 語音服務
titleSuffix: Azure Cognitive Services
description: 您的裝置隨時聆聽等候喚醒字 (或片語)。 當使用者說出喚醒字時，裝置便會將所有後續的音訊傳送到雲端，直到使用者停止說話。 自訂您的喚醒字是區隔裝置並強化品牌的有效方法。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 2280af4bf37fdb3cd12482da855f979a9180f0ec
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65020520"
---
# <a name="create-a-custom-wake-word-by-using-the-speech-service"></a>使用語音服務來建立自訂的喚醒字

您的裝置隨時聆聽等候喚醒字 (或片語)。 例如，「嗨 Cortana」是 Cortana 助理的喚醒字。 當使用者說出喚醒字時，裝置便會將所有後續的音訊傳送到雲端，直到使用者停止說話。 自訂您的喚醒字是區隔裝置並強化品牌的有效方法。

在本文中，您將了解如何為裝置建立自訂的喚醒字。

## <a name="choose-an-effective-wake-word"></a>選擇有效的喚醒字

在選擇喚醒字時，請考慮以下指導方針：

* 您的喚醒字應是英文單字或片語。 說出喚醒字所需的時間不應超過兩秒。

* 由 4 到 7 個音節所構成的單字，是效果最佳的喚醒字。 例如 "Hey, Computer" 是不錯的喚醒字。 只有 "Hey" 則不好。

* 喚醒字應遵循一般英文發音規則。

* 遵循一般英文發音規則的獨特或甚至虛構的單字可減少誤判的情況。 例如，"computerama" 會是好的喚醒字。

* 請勿選擇一般單字。 例如，"eat" 和 "go" 是一般對話中常說的單字。 它們可能會不小心觸發您的裝置。

* 避免使用可能有其他發音的喚醒字。 使用者將必須知道讓裝置回應的「正確」發音。 例如，"509" 可能會讀為 "five zero nine"、"five oh nine" 或 "five hundred and nine"。 "R.E.I." 可能會讀為 "r-e-i" 或 "ray"。 "Live" 可能會讀為"/līv/" 或 "/liv/"。

* 請勿使用特殊字元、符號或數字。 例如，"Go#" 和 "20 + cats" 不是好的喚醒字。 不過，"go sharp" 或 "twenty plus cats" 是可用的喚醒字。 您仍然可以使用品牌中的符號，並使用行銷管道和文件來強調適當的發音。

> [!NOTE]
> 如果您使用註冊商標單字作為喚醒字，請務必確認您擁有該商標，或者已從該商標擁有者取得使用該字組的權限。 Microsoft 無須對您選擇喚醒字而產生的任何法律問題負擔任何責任。

## <a name="create-your-wake-word"></a>建立您的喚醒字

您可以使用自訂的網路喚醒 word 與您的裝置之前，您必須建立網路喚醒 word 與 Microsoft 自訂喚醒 Word 產生服務。 之後您提供網路喚醒 word，服務會產生，您將部署到您的開發套件，以啟用網路喚醒 word，您的裝置上的檔案。

1. 移至[自訂語音服務入口網站](https://aka.ms/sdsdk-speechportal)並**登入**或如果您沒有選擇語音訂用帳戶[**建立訂用帳戶**](https://go.microsoft.com/fwlink/?linkid=2086754)

    ![自訂語音服務入口網站](media/speech-devices-sdk/wake-word-4.png)

1. 在[自訂喚醒 Word](https://aka.ms/sdsdk-wakewordportal)頁面上您的選擇，然後按一下 網路喚醒 word 中的型別**新增喚醒字詞**。 我們有一些[指導方針](#choose-an-effective-wake-word)若要協助選擇有效的關鍵字。 目前僅支援 EN-US 語言。

    ![輸入您的喚醒字](media/speech-devices-sdk/wake-word-5.png)

1. 將建立三個其他的發音的線上醒機 word。 您可以選擇要使用的所有發音。 然後選取**送出**產生喚醒 word。 如果您想要變更網路喚醒 word 請移除現有第一，發音該行暫留時，會出現 [刪除] 圖示。

    ![檢閱您的網路喚醒 word](media/speech-devices-sdk/wake-word-6.png)

1. 可能需要一分鐘的時間產生模型。 系統會提示您下載檔案。

    ![下載您的網路喚醒 word](media/speech-devices-sdk/wake-word-7.png)

1. 將 .zip 檔案儲存到您的電腦。 您將需要此檔案來部署您的自訂網路喚醒 word 到開發套件。

## <a name="next-steps"></a>後續步驟

測試具有您自訂的網路喚醒單字[語音裝置 SDK 快速入門](https://aka.ms/sdsdk-quickstart)。
