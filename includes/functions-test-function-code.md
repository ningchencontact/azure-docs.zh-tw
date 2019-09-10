---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: 9381f436aaeedb31732f853a6c4765ac43c6a752
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2019
ms.locfileid: "70170904"
---
## <a name="test"></a>在 Azure 測試函式

使用 cURL 來測試已部署的函式。 使用您從上一個步驟複製的 URL (包括函式索引鍵)，將查詢字串 `&name=<yourname>` 附加至 URL。

![使用 cURL 來呼叫 Azure 中的函式。](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

您也可以將複製的 URL (包括函式索引鍵) 貼至網頁瀏覽器的位址。 同樣地，先將查詢字串 `&name=<yourname>` 附加至 URL，然後再執行要求。

![使用網頁瀏覽器來呼叫函式。](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
