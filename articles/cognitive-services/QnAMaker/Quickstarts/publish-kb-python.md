---
title: 快速入門：發佈知識庫 - REST (Python) - QnA Maker
titleSuffix: Azure Cognitive Services
description: 這個以 REST 為基礎的快速入門會逐步引導您發佈知識庫，以將經過測試的最新版知識庫推送至代表已發佈知識庫的專用 Azure 搜尋服務索引。 它也會建立可在您的應用程式或聊天機器人中呼叫的端點。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: diberry
ms.openlocfilehash: e58b344102eb900ffe41bb90e541258eb3b59286
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646809"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-python"></a>快速入門：使用 Python 在 QnA Maker 中發佈知識庫

本快速入門會逐步引導您以程式設計方式發佈知識庫 (KB)。 發佈作業會將最新版的知識庫推送到專用 Azure 搜尋服務索引，並建立端點以供應用程式或聊天機器人呼叫。

本快速入門會呼叫 QnA Maker API：
* [發佈](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) - 此 API 不需要在要求主體中有任何資訊。

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-python-repo-note.md)]

1. 在您偏好的 IDE 中建立新的 Python 專案。
2. 新增下方提供的程式碼。
3. 以訂用帳戶有效的存取金鑰來取代 `key` 值。
4. 執行程式。

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json, time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE';

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'

def pretty_print (content):
# Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)

def publish_kb (path, content):
    print ('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len (content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request ("POST", path, content, headers)
    response = conn.getresponse ()

    if response.status == 204:
        return json.dumps({'result' : 'Success.'})
    else:
        return response.read ()

path = service + method + kb
result = publish_kb (path, '')
print (pretty_print(result))
```

## <a name="the-publish-a-knowledge-base-response"></a>發佈知識庫的回應

如以下範例所示，成功的回應會以 JSON 格式來傳回：

```json
{
  "result": "Success."
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API 參考](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)

[QnA Maker 概觀](../Overview/overview.md)