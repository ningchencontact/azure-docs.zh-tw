---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 975d6625f46a6c10417793764b28d0a529a5c0cd
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "58114830"
---
### <a name="prerequisites"></a>必要條件
* [ProjectOnline](https://products.office.com/Project/project-online-with-project-for-office-365) 帳戶 

您必須先授與邏輯應用程式連接到 ProjectOnline 帳戶的權限，之後才能在邏輯應用程式中使用您的 ProjectOnline 帳戶。 所幸，您可以使用 Azure 入口網站在邏輯應用程式內輕易達成這項作業。 

若要授與邏輯應用程式連接到 ProjectOnline 帳戶的權限，其步驟如下：

1. 若要建立 ProjectOnline 連線，請在邏輯應用程式設計工具中，選取下拉式清單的 [顯示 Microsoft 受控 API]，然後在搜尋方塊中輸入「ProjectOnline」。 選取您要使用的觸發程序或動作：  
   ![ProjectOnline 步驟 1](./media/connectors-create-api-projectonline/projectonline-1.png)
2. 如果您之前尚未建立任何 ProjectOnline 連線，系統會提示您提供 ProjectOnline 認證。 這些認證會用來授與邏輯應用程式連接並存取 ProjectOnline 帳戶資料的權限：  
   ![ProjectOnline 步驟 2](./media/connectors-create-api-projectonline/projectonline-2.png)
3. 提供您的 ProjectOnline 使用者名稱和密碼以授與邏輯應用程式權限：  
   ![ProjectOnline 步驟 3](./media/connectors-create-api-projectonline/projectonline-3.png)   
4. 請注意，此時已建立連接，您現可進行邏輯應用程式中的其他步驟：  
   ![ProjectOnline 步驟 4](./media/connectors-create-api-projectonline/projectonline-4.png)   

