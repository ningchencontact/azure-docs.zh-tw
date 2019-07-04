---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: a3f75b7273164abc5318f16e9ab8d9883ff0c0aa
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173761"
---
## <a name="test"></a>在 Azure 測試函式

使用 cURL 來測試已部署的函式。 使用您從上一個步驟複製的 URL，將查詢字串 `&name=<yourname>` 附加至 URL，如下列範例所示：

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

![使用 cURL 來呼叫 Azure 中的函式。](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

您也可以將複製的 URL 貼至網頁瀏覽器的位址。 同樣地，先將查詢字串 `&name=<yourname>` 附加至 URL，然後再執行要求。

![使用網頁瀏覽器來呼叫函式。](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
