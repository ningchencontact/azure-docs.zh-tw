---
title: 容器的需求和建議
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/20/2019
ms.author: dapine
ms.openlocfilehash: 2fa97a3a18741f506e10e5e4d61da286db3f744c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481751"
---
> [!NOTE]
> 需求和建議是以每秒單一要求的基準測試為基礎，使用包含29行且總計為803個字元的掃描商務信件的 8 MB 影像。

下表描述每個讀取容器的資源的最低和建議配置。

| 容器 | 最小值 | 建議 |本<br>（最小值，最大值）|
|-----------|---------|-------------|--|
| 讀取 | 1個核心，8 GB 記憶體，0.24 TPS | 8核心，16 GB 記憶體，1.17 TPS | 0.24、1.17 |

* 每個核心必須至少 2.6 GHz 或更快。
* TP-每秒交易數。

核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來作為 `docker run` 命令的一部分。
