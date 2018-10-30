---
title: 快速入門：發佈知識庫 - REST (Go) - QnA Maker
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
ms.openlocfilehash: 67914cccd4b1cee2bb43f18fc00346f15d79cafa
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646087"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-go"></a>快速入門：使用 Go 在 QnA Maker 中發佈知識庫

本快速入門會逐步引導您以程式設計方式發佈知識庫 (KB)。 發佈作業會將最新版的知識庫推送到專用 Azure 搜尋服務索引，並建立端點以供應用程式或聊天機器人呼叫。

本快速入門會呼叫 QnA Maker API：
* [發佈](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) - 此 API 不需要在要求主體中有任何資訊。

1. 在您最愛的 IDE 中建立新的 Go 專案。
2. 新增下方提供的程式碼。
3. 以訂用帳戶有效的存取金鑰來取代 `key` 值。
4. 執行程式。

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io/ioutil"
    "net/http"
    "strconv"
)

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
var subscriptionKey string = "ENTER KEY HERE"

// NOTE: Replace this with a valid knowledge base ID.
var kb string = "ENTER ID HERE";

var host string = "https://westus.api.cognitive.microsoft.com"
var service string = "/qnamaker/v4.0"
var method string = "/knowledgebases/"

func pretty_print(content string) string {
    var obj map[string]interface{}
    json.Unmarshal([]byte(content), &obj)
    result, _ := json.MarshalIndent(obj, "", "  ")
    return string(result)
}

func post(uri string, content string) string {
    req, _ := http.NewRequest("POST", uri, bytes.NewBuffer([]byte(content)))
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Content-Length", strconv.Itoa(len(content)))
    client := &http.Client{}
    response, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer response.Body.Close()
    body, _ := ioutil.ReadAll(response.Body)

    if(response.StatusCode == 204) {
        return "{'result' : 'Success.'}"
    } else {
        return string(body)
    }
}

func publish(uri string, req string) string {
    fmt.Println("Calling " + uri + ".")
    return post(uri, req)
}

func main() {
    var uri = host + service + method + kb
    body := publish(uri, "")
    fmt.Printf(body + "\n")

}
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