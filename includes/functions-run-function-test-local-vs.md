---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 0493de7374cffcc40f0434d84facf50b6a708c7b
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592890"
---
1. 若要執行您的函式，請按 **F5**。 您可能需要啟用防火牆例外狀況，工具才能處理 HTTP 要求。 在本機執行時，不會強制執行授權層級。

2. 從 Azure Functions 執行階段輸出複製函式的 URL。

    ![Azure 本機執行階段](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. 將 HTTP 要求的 URL 貼到瀏覽器的網址列。 將查詢字串 `?name=<YOUR_NAME>` 附加至此 URL 並執行要求。 下圖顯示瀏覽器中對於函式傳回之本機 GET 要求所做出的回應︰ 

    ![瀏覽器中的函式 localhost 回應](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. 若要停止偵錯，請按 **Shift + F5**。