---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: a3f75b7273164abc5318f16e9ab8d9883ff0c0aa
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132199"
---
## <a name="test"></a>在 Azure 測試函式

使用 cURL 來測試已部署的函式。 使用您從上一個步驟複製的 URL，將查詢字串 `&name=<yourname>` 附加至 URL，如下列範例所示：

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

![使用 cURL 來呼叫 Azure 中的函式。](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

您也可以將複製的 URL 貼至網頁瀏覽器的位址。 同樣地，先將查詢字串 `&name=<yourname>` 附加至 URL，然後再執行要求。

![使用網頁瀏覽器來呼叫函式。](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
