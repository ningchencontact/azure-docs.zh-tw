---
title: 管理您在 LUIS 中的金鑰 | Microsoft Docs
description: 使用 Language Understanding (LUIS) 管理您的程式設計 API、端點和外部金鑰。
titleSuffix: Azure
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/21/2018
ms.author: v-geberr
ms.openlocfilehash: 8e6e363649a0bdab5525de7b8e7abe9a53d14573
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266017"
---
# <a name="manage-your-luis-keys"></a>管理您的 LUIS 金鑰
金鑰可讓您編寫和發佈您的 LUIS 應用程式，或查詢您的端點。 

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
## <a name="key-concepts"></a>重要概念
請參閱 [LUIS 中的金鑰](luis-concept-keys.md)以了解 LUIS 編寫和端點金鑰概念。

<a name="create-and-use-an-endpoint-key"></a>
## <a name="assign-endpoint-key"></a>指派端點金鑰
在 [發佈應用程式] 頁面的 [資源和金鑰] 表格中已經有一個金鑰。 這是編寫 (入門) 金鑰。 

1. 在 [Azure 入口網站](https://portal.azure.com)上建立 LUIS 金鑰。 如需進一步的指示，請參閱[使用 Azure 建立訂用帳戶金鑰](luis-how-to-azure-subscription.md)。
 
2. 若要新增在上一個步驟中建立的 LUIS 金鑰，請按一下 [新增金鑰] 按鈕以開啟 [將金鑰指派給您的應用程式] 對話方塊。 

    ![將金鑰指派給您的應用程式](./media/luis-manage-keys/assign-key.png)
3. 在對話方塊中選取租用戶。 
 
    > [!Note]
    > 在 Azure 中，租用戶代表與服務建立關聯之用戶端或組織的 Azure Active Directory 識別碼。 如果您先前已使用個人的 Microsoft 帳戶註冊 Azure 訂用帳戶，就代表您就已經有一個租用戶！ 當您登入 Azure 入口網站時，會自動登入[您的預設租用戶](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant)。 您可以任意使用此租用戶，但可能會想要建立組織系統管理員帳戶。

4. 選擇與您要新增的 Azure LUIS 金鑰相關聯的 Azure 訂用帳戶。

5. 選取 Azure LUIS 帳戶。 帳戶的區域會顯示在括弧中。 

    ![選擇金鑰](./media/luis-manage-keys/assign-key-filled-out.png)

6. 指派此端點金鑰之後，請在所有端點查詢使用此金鑰。 

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

## <a name="publishing-regions"></a>發佈區域
深入了解如何發佈[區域](luis-reference-regions.md)，包括在[歐洲](luis-reference-regions.md#publishing-to-europe)和[澳洲](luis-reference-regions.md#publishing-to-australia)發佈。 發佈區域與編寫區域不同。 請確定您建立應用程式的編寫區域對應到想要的發佈區域。

## <a name="unassign-key"></a>取消指派金鑰

* 在 [資源和金鑰] 清單中，按一下您想要取消指派之實體旁的資源回收筒圖示。 接著，在確認訊息中按一下 [確定] 以確認刪除。
 
    ![取消指派實體](./media/luis-manage-keys/unassign-key.png)

> [!NOTE]
> 取消指派 LUIS 金鑰不會將它從您的 Azure 訂用帳戶刪除。

## <a name="next-steps"></a>後續步驟

在 [發佈應用程式] 頁面中，使用您的金鑰發佈應用程式。 如需發佈的指示，請參閱[發佈應用程式](PublishApp.md)。

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website