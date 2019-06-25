---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: d90ef1737f0107e5e323c1e90e3f9021efd4e4f7
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205794"
---
### <a name="prerequisites"></a>必要條件
* [SMTP](https://wikipedia.org/wiki/Simple_Mail_Transfer_Protocol) 帳戶  

您必須先授權邏輯應用程式連線到您的 SMTP 帳戶，才能在該邏輯應用程式中使用您的 SMTP 帳戶。幸運的是，您可以在「Azure 入口網站」上，從邏輯應用程式內輕鬆完成此操作。  

若要授權邏輯應用程式連線到您的 SMTP 帳戶，其步驟如下：  

1. 若要建立與 SMTP 的連線，請在邏輯應用程式設計工具中，從下拉式清單中選取 [顯示 Microsoft 受控 API]  ，然後在搜尋方塊中輸入「SMTP」  。 選取您要使用的觸發程序或動作：  
   ![](./media/connectors-create-api-smtp/smtp-1.png)  
2. 如果您之前尚未建立任何 SMTP 連線，系統會提示您提供 SMTP 認證。 這些認證將用來授權邏輯應用程式連線及存取您 SMTP 帳戶的資料：  
   ![](./media/connectors-create-api-smtp/smtp-2.png)  
3. 請注意，此時已建立連接，您現可進行邏輯應用程式中的其他步驟：  
   ![](./media/connectors-create-api-smtp/smtp-3.png)  

