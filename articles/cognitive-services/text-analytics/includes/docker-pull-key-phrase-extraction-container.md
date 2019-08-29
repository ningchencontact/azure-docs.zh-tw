---
title: 關鍵片語擷取容器的 Docker pull
titleSuffix: Azure Cognitive Services
description: 關鍵片語擷取容器的 Docker pull 命令
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: e71db5455a3d6726cd6567d568a13fcdcf9cb520
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051254"
---
## <a name="pull-the-key-phrase-extraction-container"></a>提取關鍵片語擷取容器

您可以在 Microsoft Container Registry 上取得文字分析的容器映射。

| 容器 | Container Registry/存放庫/映射名稱 |
|-----------|------------|
| 關鍵片語擷取 | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)使用命令從 Microsoft container Registry 下載容器映射。

如需文字分析容器可用標記的完整描述, 請參閱 Docker Hub 上的[關鍵片語擷取](https://go.microsoft.com/fwlink/?linkid=2018757)容器。

### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>關鍵片語擷取容器的 Docker pull

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
