---
title: 如何在 Azure API 管理中建立訂用帳戶 | Microsoft Docs
description: 了解如何在 Azure API 管理中建立訂用帳戶。
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 3f4e113125ec9644aac974e47996afe290e57cee
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2018
ms.locfileid: "52621684"
---
# <a name="how-to-create-subscriptions-in-azure-api-management"></a>如何在 Azure API 管理中建立訂用帳戶

透過 Azure API 管理 (APIM) 發佈 API 時，安全地存取這些 API 的最簡單和最常用的方式是使用訂用帳戶金鑰。 換句話說，必須使用已發佈 API 的用戶端應用程式在呼叫這些 API 時，必須在 HTTP 要求中包含有效的訂用帳戶金鑰。 若要取得用於存取 API 的訂用帳戶金鑰，則需要訂用帳戶。 如需訂用帳戶的詳細資訊，請參閱 [Azure API 管理中的訂用帳戶](api-management-subscriptions.md)

本文將逐步引導您在 Azure 入口網站中建立訂用帳戶。

## <a name="prerequisites"></a>必要條件

若要完成本文中的步驟，您需要：

+ [建立 APIM 執行個體](get-started-create-service-instance.md)
+ 了解 [APIM 中的訂用帳戶](api-management-subscriptions.md)

## <a name="create-a-new-subscription"></a>建立新的訂用帳戶

1. 按一下左邊功能表的 [訂用帳戶]
2. 按一下 [新增訂用帳戶]
3. 提供訂用帳戶的名稱並選取範圍
4. 按一下 [儲存] 

![彈性的訂用帳戶](./media/api-management-subscriptions/flexible-subscription.png)

一旦建立訂用帳戶，則會佈建一組 API 金鑰 (主要和次要) 以存取 API。

## <a name="next-steps"></a>後續步驟
如需 API 管理的詳細資訊：

+ 了解 API 管理中的其他[概念](api-management-terminology.md)
+ 如需深入了解 API 管理，請依照我們的[教學課程](import-and-publish.md)
+ 針對常見的問題，請查看我們的[常見問題集頁面](api-management-faq.md)