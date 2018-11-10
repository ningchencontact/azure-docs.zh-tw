---
title: 包含檔案
description: 包含檔案
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 506270b1828e98f14e3fe7a84b7f780e209e2669
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165404"
---
傳回的資料具有預期值以及預設的上下限。 實務上，您可以定義 [sensitivity] 參數，然後使用 (ExpectedValue + sensitivity * UpperMargin) 作為上限並使用 (ExpectedValue - sensitivity * LowerMargin) 作為下限，自行調整異常點。 [sensitivity] 的值必須大於 1。 以下是一些可調整的圖表。

> [!NOTE]
> 此範例應用程式不會產生圖表。 它們是利用範例應用程式，透過個別工具所建立的。

![Tunning: sensitivity = 1.0](../media/sensitivity_1.png)
![Tunning: sensitivity = 1.5](../media/sensitivity_1.5.png)
![Tunning: sensitivity = 2](../media/sensitivity_2.png)
![Tunning: sensitivity = 3.5](../media/sensitivity_3.5.png)