---
title: 包含檔案
description: 包含檔案
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: 39bfa5b6800c65112850faa8842b915ceedb9312
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648118"
---
在 [Azure 資源]  頁面 (左側功能表) 上的 [管理]  區段 (右上方功能表) 中，複製**範例查詢** URL，然後貼到新的瀏覽器索引標籤中。 

端點 URL 看起來類似下列格式，請使用您自己的應用程式識別碼和端點金鑰來取代 APP-ID 和 KEY-ID：

```console
https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```