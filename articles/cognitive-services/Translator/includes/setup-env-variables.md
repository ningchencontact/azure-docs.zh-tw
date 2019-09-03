---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 156486f4f4f0df3d4bb4ab76492709bbecfb8eb5
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2019
ms.locfileid: "69906617"
---
## <a name="set-up"></a>設定

### <a name="create-a-translator-text-resource"></a>建立翻譯工具文字資源

Azure 認知服務會由您訂閱的 Azure 資源呈現。 請使用 [Azure 入口網站](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 在本機電腦上建立翻譯工具文字的資源。 您也可以：

* 取得可免費使用 7 天的[試用版金鑰](https://azure.microsoft.com/try/cognitive-services)。 註冊之後，即可在 Azure 網站上取得該金鑰。
* 在 [Azure 入口網站](https://portal.azure.com/)中檢視現有資源。

從試用版訂用帳戶或資源取得金鑰後，請建立兩個[環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)：

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` - 您翻譯工具文字資源的訂用帳戶金鑰。
* `TRANSLATOR_TEXT_ENDPOINT` -您資源的區域端點或自訂子網域名稱。
