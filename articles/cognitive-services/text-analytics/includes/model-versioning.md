---
title: 模型版本設定
titleSuffix: Azure Cognitive Services
description: 在 V3 端點中指定模型版本
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/25/2019
ms.author: aahi
ms.openlocfilehash: 5a06e26e5f1640024e343c714db3df134422115c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488645"
---
第 3 版的文字分析 API 可讓您選擇用於資料的文字分析模型。 使用選擇性的 `model-version` 參數，在您的要求中選取模型版本。 如果未指定此參數，API 會預設為 `latest`，即最新的穩定模型版本。

可用的模型版本：
* `2019-10-01` (`latest`)

v3 端點的每個回應都包含一個 `model-version` 欄位，其指定使用的模型版本。

```json
{
    “documents”: […]
    “errors”: []
    “model-version”: “2019-10-01”
}
```
