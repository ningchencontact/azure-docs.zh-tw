---
title: 調查與分析使用者在 Azure Sentinel 預覽版中的使用者 |Microsoft Docs
description: 深入了解如何調查與分析使用者在 Azure Sentinel 的使用者。
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5b06aef0-16be-4ca0-83e4-a33795a82353
ms.service: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: ee871f90838b32be3c950ab23a0f01568c309269
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/02/2019
ms.locfileid: "57246316"
---
# <a name="investigate-users-with-user-analytics"></a>調查與分析使用者的使用者

> [!IMPORTANT]
> Azure 的 Sentinel 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用者是您想要密切監視的實體。 為了協助您深入了解您的使用者，Azure Sentinel 緊密整合，使用 Azure 進階威脅防護。 這項整合可讓您分析使用者行為，並優先處理您應該調查首先，根據其警示和可疑的活動模式來跨 Azure Sentinel 和 Microsoft 365 的使用者。

Azure 的 Sentinel 來擴充您的使用者資料與 Microsoft 365，若要啟用 Azure Active Directory 中的所有使用者的完整使用者分析和風險分析的分析。 

## <a name="how-it-works"></a>運作方式

1. 根據您分析的規則，相符項目時偵測到，Azure Sentinel 將警示傳送至 Azure ATP 的安全性。
1. Azure ATP 會檢查相關警示，哪些使用者實體，並計算這些使用者的調查優先順序。
3. 它已針對 Azure Sentinel 增添分析規則的資料之後，azure ATP 然後重新計算分數的使用者。


## <a name="prerequisites"></a>必要條件

- Azure 進階威脅防護授權 
- 若要啟用此功能，您需要安裝 Azure Sentinel 的租用戶的全域系統管理員權限

> [!NOTE]
> - 針對每個 Azure ATP 租用戶中，您可以連接一個 Azure Sentinel 的執行個體。
> - 分析使用者是目前僅適用於 Azure Active Directory 使用者。 

## <a name="enable-user-analytics"></a>讓使用者分析

1. 若要啟用在 Azure Sentinel，在入口網站移至的使用者分析**使用者分析**頁面，然後按一下**啟用**。 這將工作區有關的資訊傳送至 Azure ATP 時。

1. 然後，它會帶您到 Azure ATP。 底下**安全性延伸模組**中**Microsoft Azure Sentinel**索引標籤上，按一下  **+ 加上**新增，然後選取 工作區。 
1. 按一下 [ **連接**]。

## <a name="investigate-a-user"></a>調查使用者

1. 在底下的 [Azure Sentinel] 功能表**使用者分析**，檢閱根據調查優先順序的使用者清單。 分數根據 Azure Sentinel 警示和 Microsoft 365 警示。

2. 搜尋您想要調查的使用者。 按一下 上移至 使用者 頁面的使用者。 經過一段時間，時間軸上，檢閱使用者的活動和警示。 您可以看到所有的活動，從 Microsoft 365 及 Azure Sentinel。 您也可以向下切入至使用者案例中，以到達 [使用者] 頁面。
      
    ![使用者](./media/user-analytics/user-investigation.png)

 
3. 為此正常運作，您必須正確設定[自訂警示規則](tutorial-detect-threats.md)對應到正確的使用者識別碼**帳戶**實體。

    - Windows 事件的對應**帳號**到**SID**
    - Azure AD 的資料 （可在許多記錄檔，包括 Azure 活動） 或 Office 365 資料將對應**帳號**屬性設**GUID**，或**使用者主體名稱**。 

   ![查詢](./media/user-analytics/query-window.png)



例如︰ 
> [!NOTE]
> 在 Azure 活動的活動記錄檔中的呼叫端的實體會包含 UPN。

1. 此查詢會搜尋建立資源] 或 [Azure 活動記錄檔中的部署活動的異常數目。

        AzureActivity      
        | where TimeGenerated >= startofday(ago(7d))        
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"        
        | where ActivityStatus == "Succeeded"        
        | make-series dResourceCount=dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(startofday(ago(7d)), now(), 1d) by Caller        
        | extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(dResourceCount)        
        | where Slope > 0.2        
        | join kind=leftsemi (        
        // Last day's activity is anomalous        
        AzureActivity        
        | where TimeGenerated >= startofday(ago(1d))        
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"        
        | where ActivityStatus == "Succeeded"        
        | make-series dResourceCount=dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(startofday(ago(1d)), now(), 1d) by Caller        
        | extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(dResourceCount)        
        | where Slope >0.2        
        ) on Caller        
        | extend AccountCustomEntity = Caller
 
2. 自訂警示規則，在對應**帳號**屬性設**呼叫端**。
   
   ![查詢](./media/user-analytics/query-window.png)

3. 調查使用者的 [調查] 視窗中的使用者。 如需有關如何調查使用者的建議，請參閱[教學課程：調查使用者](https://docs.microsoft.com/azure-advanced-threat-protection/investigate-a-user)。



## <a name="next-steps"></a>後續步驟

本文件中，您已了解如何在您的威脅情報提供者連線到 Azure 的 Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章。

- 若要開始使用 Azure 的 Sentinel，您需要 Microsoft Azure 訂用帳戶。 如果您沒有訂用帳戶，可以註冊[免費試用](https://azure.microsoft.com/free/)。
- 了解如何[登入您的資料，Azure Sentinel](quickstart-onboard.md)，並[洞悉您的資料，與潛在的威脅](quickstart-get-visibility.md)。
