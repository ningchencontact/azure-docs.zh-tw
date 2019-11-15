---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2019
ms.author: aahi
ms.openlocfilehash: 2fe3104d61b5fe2fbf9624ed2fd4fdb2de5686a2
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2019
ms.locfileid: "73750185"
---
取得金鑰和端點來驗證您的應用程式。 請使用 [Azure 入口網站](../../../cognitive-services-apis-create-account.md)或 [Azure CLI](../../../cognitive-services-apis-create-account-cli.md) 在本機電腦上建立文字分析的資源。 您也可以：

* 取得可免費使用 7 天的[試用版金鑰](https://azure.microsoft.com/try/cognitive-services/#decision)。 註冊之後，即可在 [Azure 網站](https://azure.microsoft.com/try/cognitive-services/my-apis/)上取得該金鑰。  
* 在 [Azure 入口網站](https://portal.azure.com/)上檢視您的資源

從試用版訂用帳戶或資源取得金鑰和端點後，請建立兩個[環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。 名為 `TEXT_ANALYTICS_SUBSCRIPTION_KEY` 的變數用於您的金鑰，另一個名為 `TEXT_ANALYTICS_ENDPOINT` 的變數用於端點。