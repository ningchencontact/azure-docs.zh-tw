---
title: 建立自訂關鍵字-語音服務
titleSuffix: Azure Cognitive Services
description: 您的裝置一律會接聽關鍵字（或片語）。 當使用者說出關鍵字時，裝置會將所有後續的音訊傳送到雲端，直到使用者停止說話為止。 自訂您的關鍵字是區分您的裝置並強化商標的有效方式。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: bf9afb66163532b4095e0d30b1167010320abbf8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490931"
---
# <a name="create-a-custom-keyword-by-using-the-speech-service"></a>使用語音服務建立自訂關鍵字

您的裝置一律會接聽關鍵字（或片語）。 例如，「嘿 Cortana」是 Cortana assistant 的關鍵字。 當使用者說出關鍵字時，裝置會將所有後續的音訊傳送到雲端，直到使用者停止說話為止。 自訂您的關鍵字是區分您的裝置並強化商標的有效方式。

在本文中，您將瞭解如何為您的裝置建立自訂關鍵字。

## <a name="choose-an-effective-keyword"></a>選擇有效的關鍵字

當您選擇關鍵字時，請考慮下列指導方針：

* 您的關鍵字應該是英文單字或片語。 說出喚醒字所需的時間不應超過兩秒。

* 由 4 到 7 個音節所構成的單字，是效果最佳的喚醒字。 例如，「嗨，Computer」是不錯的關鍵字。 只有 "Hey" 則不好。

* 關鍵字應遵循常用的英文發音規則。

* 遵循一般英文發音規則的獨特或甚至虛構的單字可減少誤判的情況。 例如，"computerama" 可能是不錯的關鍵字。

* 請勿選擇一般單字。 例如，"eat" 和 "go" 是一般對話中常說的單字。 它們可能會不小心觸發您的裝置。

* 請避免使用可能有其他發音的關鍵字。 使用者將必須知道讓裝置回應的「正確」發音。 例如，"509" 可能會讀為 "five zero nine"、"five oh nine" 或 "five hundred and nine"。 "R.E.I." 可能會讀為 "r-e-i" 或 "ray"。 "Live" 可能會讀為"/līv/" 或 "/liv/"。

* 請勿使用特殊字元、符號或數字。 例如，「Go #」和「20 + 貓」不是好的關鍵字。 不過，"go sharp" 或 "twenty plus cats" 是可用的喚醒字。 您仍然可以使用品牌中的符號，並使用行銷管道和文件來強調適當的發音。

> [!NOTE]
> 如果您選擇商標單字做為關鍵字，請確定您擁有該商標，或您具有商標擁有者的許可權，才能使用此字組。 Microsoft 對於您選擇的關鍵字可能會發生的任何法律問題概不負任何責任。

## <a name="create-your-keyword"></a>建立關鍵字

您必須先使用 Microsoft 自訂關鍵字產生服務來建立關鍵字，才可以在裝置上使用自訂關鍵字。 在您提供關鍵字之後，服務會產生一個檔案，您可以將它部署到開發工具組，以便在裝置上啟用關鍵字。

1. 移至[語音 Studio](https://aka.ms/sdsdk-speechportal)並登**入**，或者，如果您還沒有語音訂用帳戶，請選擇 [[**建立訂用**](https://go.microsoft.com/fwlink/?linkid=2086754)帳戶]。

1. 在 [[自訂關鍵字](https://aka.ms/sdsdk-wakewordportal)] 頁面上，輸入您選擇的關鍵字，然後按一下 [**新增關鍵字**]。 我們有一些[指導方針](#choose-an-effective-keyword)可協助您選擇有效的關鍵字。 支援目前僅限於 en-us 語言。

    ![輸入您的關鍵字](media/speech-devices-sdk/custom-kws-portal-enter-keyword.png)

1. 入口網站現在會為您的關鍵字建立候選的發音。 按一下 [播放] 按鈕，並移除任何不正確之發音旁的檢查，以接聽每個候選。 一旦核取了良好的發音，請選取 [**提交**] 以開始產生關鍵字。 如果您想要變更關鍵字，請先按一下出現在資料列右側的 [刪除] 按鈕來移除現有的關鍵字，然後將滑鼠游標移至該位置。

    ![檢查關鍵字](media/speech-devices-sdk/custom-kws-portal-review-keyword.png)

1. 最多可能需要一分鐘的時間來產生模型。 系統會提示您下載檔案。

    ![下載您的關鍵字](media/speech-devices-sdk/custom-kws-portal-download-keyword.png)

1. 將 .zip 檔案儲存到您的電腦。 您將需要此檔案，才能將自訂關鍵字部署至開發工具組。

## <a name="next-steps"></a>後續步驟

使用[語音裝置 SDK 快速入門](https://aka.ms/sdsdk-quickstart)測試您的自訂關鍵字。
