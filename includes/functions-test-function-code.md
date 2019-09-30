---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: 44eb25be12b908c8f951cb20948068da3bfc2928
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203186"
---
## <a name="test"></a>驗證 Azure 中的函式

使用 cURL 來驗證已部署的函式。 使用您從上一個步驟複製的 URL (包括函式索引鍵)，將查詢字串 `&name=<yourname>` 附加至 URL。

![使用 cURL 來呼叫 Azure 中的函式。](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

您也可以將複製的 URL (包括函式索引鍵) 貼至網頁瀏覽器的位址列。 同樣地，先將查詢字串 `&name=<yourname>` 附加至 URL，然後再執行要求。

![使用網頁瀏覽器來呼叫函式。](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
