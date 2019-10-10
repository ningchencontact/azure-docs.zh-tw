---
title: 案例可用性-語音服務
titleSuffix: Azure Cognitive Services
description: 「語音服務」區域的參考。
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cbasoglu
ms.openlocfilehash: af5bb7126af65a755cb1d58788d39cb8bdcbbb3b
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959030"
---
# <a name="scenario-availability"></a>案例可用性

語音服務 SDK 具有各種不同程式設計語言和環境的多種案例。  並非所有的案例目前都適用于所有的程式設計語言或所有環境。  以下列出每個案例的可用性。

- **語音辨識（SR）、片語清單、意圖、翻譯和內部部署容器**
  - 具有箭號連結的所有程式設計語言/環境 <img src="media/index/link.jpg" height="15" width="15"></img> 在[這裡](https://aka.ms/csspeech)的快速入門表中。
- **文字轉換語音（TTS）**
  - C++/Windows & Linux
  - C#/Windows & UWP & Unity
  - JAVA （Jre 和 Android）
  - Python
  - Swift
  - Objective-C
  - TTS REST API 可以在其他所有情況下使用。
- **喚醒字（關鍵字偵查器/KWS.TABLE）**
  - C++/Windows & Linux
  - C#/Windows & Linux
  - Python/Windows & Linux
  - JAVA/Windows & Linux & Android （語音裝置 SDK）
  - 喚醒字（關鍵字偵查器/KWS.TABLE）功能可能適用于任何麥克風類型，但官方 KWS.TABLE 支援目前僅限於在 Azure Kinect DK 硬體或語音裝置 SDK 中找到的麥克風陣列
- **語音優先虛擬助理**
  - C++/Windows & Linux & macOS
  - C#/Windows
  - JAVA/Windows & Linux & macOS & Android （語音裝置 SDK）
- **對話轉譯**
  - C++/Windows & Linux
  - C#（Framework & .NET Core）/Windows & UWP & Linux
  - JAVA/Windows & Linux & Android （語音裝置 SDK）
- **撥打電話中心轉譯**
  - REST API 和可以在任何情況下使用
- **編解碼器壓縮的音訊輸入**
  - C++/Linux
  - C#/Linux
  - JAVA/Linux、Android 和 iOS
