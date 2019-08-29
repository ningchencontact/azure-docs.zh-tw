---
title: 容器需求和建議。
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 8/26/2019
ms.author: dapine
ms.openlocfilehash: 5da088bf0356dd54d98ec5393fd2db8068f9c666
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034477"
---
下表說明每個「辨識文字」容器的最低和建議的 CPU 核心與記憶體配置。

| 容器 | 最小值 | 建議 |TPS<br>(最小值, 最大值)|
|-----------|---------|-------------|--|
|辨識文字|1個核心, 8 GB 記憶體, 0.5 TPS|2個核心, 8 GB 記憶體, 1 個 TPS|0.5、1|

* 每個核心必須至少 2.6 GHz 或更快。
* TPS - 每秒的交易數

核心和記憶體會對應至 `--cpus` 和 `--memory` 設定，用來作為 `docker run` 命令的一部分。