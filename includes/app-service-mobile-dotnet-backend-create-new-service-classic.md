---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: e087a1db008422aeec8fd4e073a7476eebe4d54b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66140636"
---
1. 登入 [Azure 入口網站]。
2. 選取 [+ 新增] > [Web + 行動] > [行動應用程式]，然後為您的行動應用程式後端提供名稱。
3. 針對 [資源群組]，選取現有的資源群組或建立新的資源群組 (使用與應用程式相同的名稱)。 
4. 針對 **App Service 方案**，系統會選取預設方案 (在[標準層](https://azure.microsoft.com/pricing/details/app-service/)中)。 您也可以選取不同的方案，或[建立新的方案](../articles/app-service/app-service-plan-manage.md#create-an-app-service-plan)。 

   App Service 方案的設定會決定與您應用程式相關聯的[位置、功能、成本和計算資源](https://azure.microsoft.com/pricing/details/app-service/)。 如需關於應用程式服務方案以及如何在不同的定價層和您所要的位置建立新方案的詳細資訊，請參閱 [Azure App Service 方案深入概觀](../articles/app-service/overview-hosting-plans.md)。
   
5. 選取 [建立] 。 此步驟會建立新的 Mobile Apps 後端。 
6. 在新 Mobile Apps 後端的 [設定] 窗格中，選取 [快速啟動] > 您的用戶端應用程式平台 > [連接資料庫]。 
   
   ![連接資料庫的選取項目](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
7. 在 [新增資料連線] 窗格中，選取 [SQL Database] > [建立新的資料庫]。 輸入資料庫名稱、選擇定價層，然後選取 [伺服器]。 您可以重複使用這個新資料庫。 如果您在同樣的位置內已有資料庫，則可以改為選擇 [使用現有的資料庫] 。 我們不建議您使用不同位置中的資料庫，因為這會產生頻寬成本和更高的延遲。
   
   ![選取資料庫](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
8. 在 [新增伺服器] 窗格的 [伺服器名稱] 方塊中輸入唯一的伺服器名稱、提供登入和密碼、選取 [允許 Azure 服務存取伺服器]，然後選取 [確定]。 此步驟會建立新的資料庫。
9. 回到 [新增資料連接] 窗格中，選取 [連接字串]、輸入資料庫的登入和密碼值，然後選取 [確定]。 

   等候幾分鐘的時間讓資料庫成功部署，然後再繼續進行。

<!-- URLs. -->
[Azure 入口網站]: https://portal.azure.com/
