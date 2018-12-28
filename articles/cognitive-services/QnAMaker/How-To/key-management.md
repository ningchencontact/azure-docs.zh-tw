---
title: 資源和金鑰管理 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 服務會處理兩種類型的金鑰，即訂用帳戶金鑰和端點金鑰。
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 11/26/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 25b23d280aca9ef13b8820596686a1f9dbecd2a1
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2018
ms.locfileid: "53085938"
---
# <a name="how-to-manage-keys-in-qna-maker"></a>如何管理 QnA Maker 中的金鑰

QnA Maker 服務會處理兩種類型的金鑰，即**訂用帳戶金鑰**和**端點金鑰**。

![金鑰管理](../media/qnamaker-how-to-key-management/key-management.png)

1. **訂用帳戶金鑰**：這些金鑰可用來存取 [QnA Maker 管理服務 API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)。 這些 API 可讓您對知識庫執行多種 CRUD 作業。  

2. **端點金鑰**：這些金鑰可用來存取知識庫端點，以取得使用者問題的回應。 您通常會在使用 QnA Maker 服務的聊天機器人/應用程式程式碼中使用此端點。
 
## <a name="subscription-keys"></a>訂用帳戶金鑰
您可以從您建立 QnA Maker 資源的 Azure 入口網站來檢視和重設訂用帳戶金鑰。 
1. 在 Azure 入口網站中，移至 QnA Maker 資源。

    ![QnA Maker 資源清單](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. 移至 [金鑰]。

    ![訂用帳戶金鑰](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>端點金鑰

您可以從 [QnA Maker 入口網站](https://qnamaker.ai)來管理端點金鑰。

1. 登入 [QnA Maker 入口網站](https://qnamaker.ai)，並移至 [管理金鑰]。

    ![端點金鑰](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. 檢視或重設您的金鑰。

    ![端點金鑰管理員](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >如果您認為金鑰遭到入侵，請加以重新整理。 為此，您可能需要對應用程式/聊天機器人程式碼進行對應的變更。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [以不同語言建立知識庫](./language-knowledge-base.md)
