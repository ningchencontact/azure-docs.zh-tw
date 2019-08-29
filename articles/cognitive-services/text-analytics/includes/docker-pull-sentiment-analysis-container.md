---
title: 情感分析容器的 Docker pull
titleSuffix: Azure Cognitive Services
description: 情感分析容器的 Docker pull 命令
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: 0954ef88f6917159156fbb73103b7b7af3283c00
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051348"
---
## <a name="pull-the-sentiment-analysis-container"></a>提取情感分析容器

您可以在 Microsoft Container Registry 上取得文字分析的容器映射。

| 容器 | Container Registry/存放庫/映射名稱 |
|-----------|------------|
| 情感分析 | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)使用命令從 Microsoft container Registry 下載容器映射。

如需文字分析容器可用標記的完整描述, 請參閱 Docker Hub 上的[情感分析](https://go.microsoft.com/fwlink/?linkid=2018654)容器。

### <a name="docker-pull-for-the-sentiment-analysis-container"></a>情感分析容器的 Docker pull

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
