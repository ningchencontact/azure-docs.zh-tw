---
title: 容器的需求和建議
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: 47c889a428ec2916dc8cad73e30ef5ff630372a7
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129809"
---
> [!NOTE]
> 需求和建議是以每秒單一要求的基準測試為基礎，使用包含29行且總計為803個字元的掃描商務信件的 8 MB 影像。

#### <a name="readtabread"></a>[讀取](#tab/read)

下表描述每個讀取容器的資源的最低和建議配置。

| 容器 | 最小值 | 建議 |TPS<br>（最小值，最大值）|
|-----------|---------|-------------|--|
| 閱讀 | 1個核心，8 GB 記憶體，0.24 TPS | 8核心，16 GB 記憶體，1.17 TPS | 0.24、1.17 |

#### <a name="recognize-texttabrecognize-text"></a>[辨識文字](#tab/recognize-text)

下表描述每個辨識文字容器的資源的最低和建議配置。

| 容器 | 最小值 | 建議 |TPS<br>（最小值，最大值）|
|-----------|---------|-------------|--|
| 辨識文字 | 1個核心，8 GB 記憶體，0.12 TPS | 8核心，16 GB 記憶體，0.60 TPS | 0.12、0.60 |

***

* 每個核心必須至少 2.6 GHz 或更快。
* TP-每秒交易數。

核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來作為 `docker run` 命令的一部分。
