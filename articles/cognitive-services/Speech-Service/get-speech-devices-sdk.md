---
title: 取得語音裝置 SDK
titleSuffix: Azure Cognitive Services
description: 語音服務可與各種裝置和音訊來源搭配運作。 您現在可以利用相符的硬體和軟體，將語音應用程式提升至下一個層級。 在本文中，您將了解如何取得存取語音裝置 SDK，並開始開發。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: b9a0890000cda0b3663ac29bee61fc1c702f6254
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65410698"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>取得認知服務語音裝置 SDK

語音裝置 SDK 是設計用於特殊用途的開發套件，與不同的麥克風陣列設定 pretuned 程式庫。

## <a name="choose-a-development-kit"></a>選擇一種開發套件

|裝置|規格|描述|案例|
|--|--|--|--|
|[Roobo 智慧音訊的開發人員套件](https://ddk.roobo.com)</br>[安裝程式](speech-devices-sdk-roobo-v1.md) / [快速入門](speech-devices-sdk-android-quickstart.md)![Roobo 智慧音訊的開發人員套件](media/speech-devices-sdk/device-roobo-v1.jpg)|7 的 mic 陣列，ARM SOC WIFI、 音訊外，IO。 </br>Android|第一個語音裝置 SDK，來調整 Microsoft Mic 陣列和前端處理 SDK，開發高品質的轉譯及語音的案例|交談文字記錄、 智慧型的主講人，語音代理程式，可穿戴式|
|[Azure 的 Kinect DK](https://azure.microsoft.com/services/kinect-dk/)![Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 的 mic 陣列 RGB 和深度相機。 </br>Windows/Linux|使用進階的人工智慧 (AI) 感應器建置複雜的電腦視覺與語音模型開發人員套件。 它會結合同級產品中的空間麥克風陣列和深度攝影機的視訊攝影機和方向感應器 — 全都在一個小型的裝置，具有多個模式、 選項和 Sdk 為容納廣泛的計算類型。|交談文字記錄，機器人智慧建築|
|Roobo 智慧音訊的開發人員套件 2![Roobo 智慧音訊的開發人員套件 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 的 mic 陣列，ARM SOC WIFI、 藍芽、 IO。 </br>Linux|第 2 代語音裝置 SDK 提供了替代的 OS 和更多的功能，在符合成本效益的參考設計。|交談文字記錄、 智慧型的主講人，語音代理程式，可穿戴式|
|URbetter T11 開發面板![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 的 mic 陣列，ARM SOC WIFI、 乙太網路、 HDMI、 USB 網路攝影機。 </br>Linux|工業級語音裝置 SDK，可配合 Microsoft 的 Mic 陣列，並且支援擴充 HDMI/乙太網路等更多的 USB 週邊設備的 I/O|交談文字記錄、 教育版、 醫院、 機器人、 OTT 方塊中，語音代理程式，以到的磁碟機|

## <a name="download-the-speech-devices-sdk"></a>下載語音裝置 SDK

下載[語音裝置 SDK](https://aka.ms/sdsdk-download)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [開始使用語音裝置 SDK](https://aka.ms/sdsdk-quickstart)
