---
title: 專案 URL 預覽的 Python 快速入門 - Microsoft 認知服務 | Microsoft Docs
description: 在 Azure 的 Microsoft 認知服務中快速開始使用專案 URL 預覽的指令碼範例。
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-url-preview
ms.topic: article
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 78b2d83b02aa9ea32509029c7456e04e420b8572
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369598"
---
# <a name="url-preview-python-quickstart"></a>URL 預覽 Python 快速入門

下列 Python 範例會建立 SwiftKey 網站的 URL 預覽：https://swiftkey.com/en。

## <a name="prerequisites"></a>先決條件

取得免費試用版[認知服務實驗室](https://aka.ms/answersearchsubscription)的存取金鑰

此範例使用 Python 3.6。

## <a name="code-scenario"></a>程式碼案例 

下列程式碼會建立 URL 預覽。
實際進行步驟如下：
1. 宣告變數來依主機及路徑指定端點。
2. 指定要預覽的查詢 URL，並加入查詢參數。  
3. 設定查詢參數。
4. 定義建立要求的搜尋函式，並加入 *Ocp-Apim-Subscription-Key* 標頭。
5. 設定 *Ocp-Apim-Subscription-Key* 標頭。 
6. 進行連線，並傳送要求。
7. 列印 JSON 結果。

此示範的完整程式碼如下：

````
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'your-subscription-key'

host = 'api.labs.cognitive.microsoft.com'
path = '/urlpreview/v7.0/search'

query = 'https://SwiftKey.com'

params = '?q=' + urllib.parse.quote (query)

def get_preview ():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_preview ()
print (json.dumps(json.loads(result), indent=4))
````
## <a name="next-steps"></a>後續步驟
- [C# 快速入門](csharp.md)
- [Java 快速入門](java-quickstart.md)
- [JavaScript 快速入門](javascript.md)
- [Node URL 快速入門](node-quickstart.md)