---
title: Azure 維護排程 (預覽版) | Microsoft Docs
description: 維護排程可讓客戶規劃必要的排程維護事件，Azure SQL 資料倉儲服務會使用它們來推出新功能、升級與修補程式。
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 04/03/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 4bc1867f0dd18a101b2e764970de4e1904b960e0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790853"
---
# <a name="change-a-maintenance-schedule"></a>變更維護排程 

## <a name="portal"></a>入口網站
維護排程可隨時更新或變更。 如果選取的執行個體正在進行作用中的維護週期，設定將會儲存。 它們將在下一個已識別的維護期間變成作用中。 [深入了解](https://docs.microsoft.com/azure/service-health/resource-health-overview)如何在作用中維護事件期間監視您的資料倉儲。 

若要使用維護排程，您必須在七天期間內選取兩個維護時段。 每個維護時段都可以是三到八個小時。 維護可能會在維護時段的任何時間進行，但不會在沒有事先通知的情況下，於時段以外進行。 您也將經歷連線短暫中斷的情況，因為服務會將新的程式碼部署到您的資料倉儲。 

## <a name="identifying-the-primary-and-secondary-windows"></a>識別主要與次要時段

主要和次要時段必須有個別的日期範圍。 範例中的主要時段是星期二到星期四，而次要時段為星期六到星期日。

若要變更資料倉儲的維護排程，請完成下列步驟：
1.  登入 [Azure 入口網站](https://portal.azure.com/)。
2.  選取您要更新的資料倉儲。 頁面會在概觀刀鋒視窗上開啟。 
3.  選取概觀刀鋒視窗上的 [維護排程 (預覽) 摘要] 連結，來開啟維護排程設定的頁面。 或者，選取左側資源功能表上的 [維護排程] 選項。  

    ![概觀刀鋒視窗選項](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. 使用頁面頂端的選項，來識別您主要維護時段的偏好日期範圍。 此選取範圍會決定您的主要時段將在週間發生或在週末發生。 您的選取範圍將會更新下拉式清單的值。 在預覽期間，某些區域可能尚未支援可用 [日期] 選項的完整集合。

   ![維護設定刀鋒視窗](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. 使用下拉式清單方塊，來選擇您偏好的主要和次要維護時段：
   - **日期**：想要在選取的時段內執行維護的偏好日期。
   - **開始時間**：偏好的維護時段開始時間。
   - **時段**：時段的偏好持續期間。

   刀鋒視窗底部的 [排程摘要] 區域會根據所選取的值來更新。 
  
6. 選取 [ **儲存**]。 隨即出現一則訊息，確認您的新排程目前為作用中。 

   如果您將排程儲存於不支援維護排程的區域中，即會出現下列訊息。 您的設定已儲存，並在功能於您選取的區域中成為可用時變成作用中。    

   ![關於區域可用性的訊息](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>後續步驟
- [深入了解](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook)適用於記錄警示規則的 Webhook 動作。
- [深入了解](https://docs.microsoft.com/azure/service-health/service-health-overview) Azure 服務健康狀態。


