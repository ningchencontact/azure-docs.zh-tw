---
title: 疑難排解語音裝置 SDK-語音服務
titleSuffix: Azure Cognitive Services
description: 本文章提供當您使用語音裝置 SDK 時，可能會遇到的資訊可協助您解決問題。
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wellsi
ms.openlocfilehash: f55171a177dfcbebb9bc6df5ce125a8f29494946
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606305"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>針對語音裝置 SDK 進行疑難排解

本文章提供當您使用語音裝置 SDK 時，可能會遇到的資訊可協助您解決問題。

## <a name="certificate-failures"></a>憑證失敗

如果使用語音服務時，您會收到憑證失敗，，請確定您的裝置有正確的日期和時間：

1. 移至 [設定]  。 在 [System] \(系統\)  底下，選取 [Date & time] \(日期與時間\)  。

    ![在 [Settings] \(設定\) 底下，選取 [Date & time] \(日期與時間\)](media/speech-devices-sdk/qsg-12.png)

1. 將 [Automatic date & time] \(自動設定日期與時間\)  選項保持選取。 在 [Select time zone] \(選取時區\)  底下，選取您目前的時區。

    ![選取日期與時區選項](media/speech-devices-sdk/qsg-13.png)

    當您看到開發套件的時間與您電腦的時間相符時，即表示開發套件已連線到網際網路。

## <a name="next-steps"></a>後續步驟

* [檢閱版本資訊](devices-sdk-release-notes.md)
