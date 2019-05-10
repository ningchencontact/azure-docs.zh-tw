---
title: 案例可用性-語音服務
titlesuffix: Azure Cognitive Services
description: 「語音服務」區域的參考。
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cbasoglu
ms.openlocfilehash: a1bd8ba36f208936ce52b3a46a72516da89a0e9a
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65507866"
---
# <a name="scenario-availability"></a>案例的可用性

語音服務 SDK 功能跨各種不同的程式設計語言和環境的許多案例。  並非所有的案例還目前適用於所有的程式設計語言或所有環境。  下列是每個案例的可用性。

- **語音辨識 (SR)、 片語清單、 意圖、 轉譯和內部部署容器**
  - 所有的程式設計語言/環境所在的箭號連結 <img src="media/index/link.jpg" height="15" width="15"></img> 在 快速入門資料表[此處](https://aka.ms/csspeech)。
- **Text-to-Speech (TTS)**
  - C++/ Windows 和 Linux
  - C#/Windows
  - TTS REST API 可用於每一種情況。
- **網路喚醒字 (關鍵字車/KWS)**
  - C++/ Windows 和 Linux
  - C#/Windows & Linux
  - Python/Windows & Linux
  - Java/Windows Linux & Android （語音裝置 SDK）
  - 線上醒機字 (關鍵字車/KWS) 功能可能會使用任何麥克風型別，官方 KWS 支援，不過，目前僅限麥克風陣列中找到 Azure Kinect DK 硬體或語音裝置 SDK
- **語音第一個虛擬助理**
  - C#/ UWP
  - Java/Windows
- **對話轉譯**
  - C++/ Windows 和 Linux
  - C#（framework 和.NET Core） / Windows UWP & Linux
  - Java/Windows Linux & Android （語音裝置 SDK）
- **撥接中心轉譯**
  - REST API，可用在任何情況下
- **轉碼器壓縮音訊輸入**
  - C++/Linux
  - C#/Linux
  - Java/Linux 和 Android
