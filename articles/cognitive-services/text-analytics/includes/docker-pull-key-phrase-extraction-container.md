---
title: 關鍵片語擷取容器的 Docker pull
titleSuffix: Azure Cognitive Services
description: 關鍵片語擷取容器的 Docker pull 命令
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 59313528e4beb343f9ac1103d3f9f2faae5dfc16
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966822"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>關鍵片語擷取容器的 Docker pull

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)使用命令從 Microsoft container Registry 下載容器映射。

如需文字分析容器可用標記的完整描述，請參閱 Docker Hub 上的[關鍵片語擷取](https://go.microsoft.com/fwlink/?linkid=2018757)容器。

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
