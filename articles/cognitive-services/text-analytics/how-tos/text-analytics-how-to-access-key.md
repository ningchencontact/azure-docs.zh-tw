---
title: 取得文字 Analytics API 的存取金鑰
titleSuffix: Azure Cognitive Services
description: 取得文字分析作業的存取金鑰，以便提交在認知服務中處理的原始文字。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 512417a228ed99a2f5b516f14084a5785bc0c1dd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58096736"
---
# <a name="how-to-find-endpoints-and-access-keys-for-the-text-analytics-cognitive-service"></a>如何對於文字分析認知服務尋找端點和存取金鑰

當您註冊文字分析時，就是取得訂用帳戶獨有的個人化存取金鑰。 每次呼叫文字分析 API 時，都需要這個金鑰。 如果您尚未[註冊](text-analytics-how-to-signup.md)，請立即註冊，以便取得您的金鑰。 

如果您需要協助尋找金鑰，或者，若要判斷您訂用帳戶是否已經有文字分析，請使用下列指示來取得必要的資訊。 

## <a name="find-your-service-endpoint-and-access-key"></a>尋找您的服務端點和存取金鑰

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在左導覽窗格中，選取 [所有服務]。

3. 在 [篩選] 中，輸入*認知服務*。 如果您的訂用帳戶有文字分析，它會出現在清單中，而且 API 類型是**文字分析 API**。

4. 按一下連結來開啟服務刀鋒視窗。 您現在即可從 [資源] > [金鑰] 中取得金鑰，也可以按一下基本資訊窗格中的 [顯示存取金鑰]。 端點也會出現在基本資訊窗格中。

   ![有端點和金鑰的入口網站頁面](../media/portal-keys-endpoint.png)

## <a name="see-also"></a>請參閱 

 [文字分析概觀](../overview.md)  
 [常見問題集 (FAQ)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [呼叫文字分析 API](text-analytics-how-to-call-api.md)
