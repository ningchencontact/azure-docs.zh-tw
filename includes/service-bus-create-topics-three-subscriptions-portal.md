---
title: 包含檔案
description: 包含檔案
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/20/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ace42278269ff6af31902dbecead81329815af12
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2019
ms.locfileid: "56588665"
---
## <a name="create-a-topic-using-the-azure-portal"></a>使用 Azure 入口網站建立主題
1. 在 [服務匯流排命名空間] 頁面上，選取左側功能表上的 [主題]。
2. 選取工具列上的 [+ 主題]。 
4. 輸入主題的**名稱**。 保留其他選項的預設值。
5. 選取 [建立] 。

    ![建立主題](./media/service-bus-create-topics-subscriptions-portal/create-topic.png)

## <a name="create-subscriptions-to-the-topic"></a>建立主題的訂用帳戶
1. 選取您在上一節中建立的**主題**。 
    
    ![選取主題](./media/service-bus-create-topics-subscriptions-portal/select-topic.png)
2. 在 [服務匯流排主題] 頁面上，從左側功能表中選取 [訂用帳戶]，然後選取工具列上的 [+ 訂用帳戶]。 
    
    ![新增訂用帳戶按鈕](./media/service-bus-create-topics-subscriptions-portal/add-subscription-button.png)
3. 在 **建立訂用帳戶**頁面上，輸入**S1** for**名稱**for 訂用帳戶，然後選取**建立**。 

    ![建立訂用帳戶頁面](./media/service-bus-create-topics-subscriptions-portal/create-subscription-page.png)
4. 重複上述步驟兩次以建立名為訂用帳戶**S2**並**S3**。