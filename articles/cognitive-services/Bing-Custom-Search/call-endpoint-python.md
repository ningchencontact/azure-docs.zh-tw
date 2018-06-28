---
title: 使用 Python 呼叫端點 - Bing 自訂搜尋 - Microsoft 認知服務
description: 本快速入門說明如何使用 Python 呼叫自訂的 Bing 搜尋端點，以從您的自訂搜尋執行個體要求搜尋結果。
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 889762ae0b401438f25546738268c584ddd58389
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2018
ms.locfileid: "35370743"
---
# <a name="call-bing-custom-search-endpoint-python"></a>呼叫 Bing 自訂搜尋端點 (Python)

本快速入門說明如何使用 Python 呼叫自訂的 Bing 搜尋端點，以從您的自訂搜尋執行個體要求搜尋結果。 

## <a name="prerequisites"></a>先決條件
若要完成本快速入門，您需要：

- 自訂搜尋執行個體。 請參閱[建立您的第一個 Bing 自訂搜尋執行個體](quick-start.md)。

-  已安裝 [Python](https://www.python.org/)。

- 具有 **Bing 搜尋 API** 的[認知服務 API 帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 [免費試用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search)即足以供本快速入門使用。 您必須要有啟動免費試用版時所提供的存取金鑰，或者您可以從 Azure 儀表板使用付費訂用帳戶金鑰。 

## <a name="run-the-code"></a>執行程式碼

若要呼叫 Bing 自訂搜尋端點，請遵循下列步驟：

1. 建立程式碼的資料夾。
2. 從管理員命令提示字元或終端機，瀏覽至您剛剛建立的資料夾。
3. 安裝**要求** Python 模組：
    <pre>
    pip install pipenv
    pipenv install requests
    </pre>
7. 會建立 BingCustomSearch.py 檔案，並將下列程式碼複製到該檔案。
8. 將 **YOUR-SUBSCRIPTION-KEY** 和 **YOUR-CUSTOM-CONFIG-ID** 取代為您的金鑰和組態識別碼 (請參閱步驟 1)。

    ``` Python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=' + searchTerm + '&customconfig=' + customConfigId
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```
9. 使用下列命令來執行程式碼。
    ```
    python BingCustomSearch.py
    ```

## <a name="next-steps"></a>後續步驟
- [設定託管的 UI 體驗](./hosted-ui.md)
- [使用裝飾標記醒目提示文字](./hit-highlighting.md)
- [頁面網頁](./page-webpages.md)