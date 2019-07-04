---
title: 資源和金鑰管理 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 服務會處理兩種類型的金鑰，即訂用帳戶金鑰和端點金鑰。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b9be1db9be1d4dd57994e101c07ed430425a5912
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447429"
---
# <a name="how-to-manage-keys-in-qna-maker"></a>如何管理 QnA Maker 中的金鑰

QnA Maker 服務會處理兩種類型的金鑰，即**訂用帳戶金鑰**和**端點金鑰**。

![金鑰管理](../media/qnamaker-how-to-key-management/key-management.png)

1. **訂用帳戶金鑰**：這些金鑰可用來存取 [QnA Maker 管理服務 API](https://go.microsoft.com/fwlink/?linkid=2092179)。 這些 API 可讓您對知識庫執行編輯。  

2. **端點金鑰**：這些金鑰可用來存取知識庫端點，以取得使用者問題的回應。 您通常會在您的聊天機器人或使用 QnA Maker 服務的應用程式程式碼中使用此端點。
 
## <a name="subscription-keys"></a>訂用帳戶金鑰
您可以從您建立 QnA Maker 資源的 Azure 入口網站來檢視和重設訂用帳戶金鑰。 
1. 在 Azure 入口網站中，移至 QnA Maker 資源。

    ![QnA Maker 資源清單](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. 移至 [金鑰]  。

    ![訂用帳戶金鑰](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>端點金鑰

您可以從 [QnA Maker 入口網站](https://qnamaker.ai)來管理端點金鑰。

1. 登入 [QnA Maker 入口網站](https://qnamaker.ai)，移至您的設定檔，然後按一下 [服務設定]  。

    ![端點金鑰](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. 檢視或重設您的金鑰。

    ![端點金鑰管理員](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >如果您認為金鑰遭到入侵，請加以重新整理。 為此，您可能需要對用戶端應用程式或 Bot 程式碼進行對應的變更。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [以不同語言建立知識庫](./language-knowledge-base.md)
