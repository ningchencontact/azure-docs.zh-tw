---
title: 使用 Azure Sentinel 來建立自訂分析規則，以偵測可疑威脅 |Microsoft Docs
description: 使用此教學課程來瞭解如何建立自訂分析規則，以使用 Azure Sentinel 來偵測可疑的威脅。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 5d4c549eaded78c69d3e7fa7173b5ad9b1d82f2f
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333151"
---
# <a name="tutorial-create-custom-analytic-rules-to-detect-suspicious-threats"></a>教學課程：建立自訂分析規則來偵測可疑威脅

將 [資料來源連線](quickstart-onboard.md)@no__t 1to Azure Sentinel 之後，您可以建立自訂規則，以便在您的環境中搜尋特定準則，並在符合準則時產生事件，以便您進行調查。 本教學課程可協助您建立自訂規則，以使用 Azure Sentinel 來偵測威脅。

本教學課程可協助您偵測 Azure Sentinel 的威脅。
> [!div class="checklist"]
> * 建立分析規則
> * 將威脅回應自動化

## <a name="create-custom-analytic-rules"></a>建立自訂分析規則

您可以建立自訂分析規則，協助您搜尋環境中可疑的威脅和異常類型。 此規則可確保您立即收到通知，讓您可以對威脅進行分級、調查及補救。

1. 在 Azure 入口網站的 [Azure Sentinel] 底下，選取 [分析]。

1. 在頂端功能表列中，選取 [ **+ 建立**]，然後選取 [已**排程的查詢規則**]。 這會開啟 [**自訂規則建立嚮導]** 。

    ![建立排程的查詢](media/tutorial-detect-threats-custom/create-scheduled-query.png)

1. 在 [**一般**] 索引標籤中，提供描述性的名稱和描述。 視需要設定**警示嚴重性**。 當您建立規則時，您可以啟用它，這樣會在您完成建立之後立即執行。 如果您將它建立為停用，規則將會新增至 [作用中**規則**] 索引標籤，而您可以在需要時從該處加以啟用。

    ![開始建立自訂分析規則](media/tutorial-detect-threats-custom/general-tab.png)

1. 在 [**設定**] 索引標籤中，您可以直接撰寫查詢，或在 Log Analytics 中建立查詢，然後將它貼到 [**搜尋查詢**] 欄位中。 當您變更並設定查詢時，Azure Sentinel 會在右側的 [**結果] 預覽**視窗中模擬查詢結果。 這可讓您深入瞭解您所建立警示的特定時間間隔內，會產生多少資料。 數量取決於您為 [**執行查詢**] 所設定的每個和**查閱資料（最後一個**）。 如果您看到您的警示平均觸發警示的頻率太高，您可以將結果數目設定為高於您的平均基準。

   ![在 Azure Sentinel 中建立查詢](media/tutorial-detect-threats-custom/settings-tab.png)

   以下是一個範例查詢，會在 Azure 活動中建立了異常數量的資源時發出警示。

    `AzureActivity
    \| where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    \| where ActivityStatus == "Succeeded"
    \| make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller`

   > [!NOTE]
   > 查詢長度應介於1到1000個字元之間，且不能包含 "search \*" 或 "union \*"。

    1. 在 [**查詢排程**] 底下，設定下列參數：

        1.  設定 [**執行查詢**] 以設定查詢執行頻率的**頻率**-頻率為每隔5分鐘，或一天不常進行一次。

        1.  **從最後一個設定查閱資料**，以控制查詢執行的資料量的時間範圍，例如，它可以每小時執行一次，橫跨60分鐘的資料。

        1. 如果您只想要在事件發生後收到警示，您可以設定 Azure Sentinel 在**產生警示之後停止執行查詢**。 您必須設定視窗，在此期間，查詢應該會停止執行，最多24小時。

    1. 在 [**警示觸發**程式] 下定義警示觸發程式條件。 在 [**實體對應**] 下，您可以將查詢中的資料行對應至 Azure Sentinel 所識別的實體欄位。 針對每個欄位，將您在 Log Analytics 中建立之查詢中的相關資料行對應至適當的實體欄位。 每個實體都包含多個欄位，例如 SID 和 GUID。 您可以根據任何欄位對應實體，而不只是最上層實體。

1.  在 [**自動回應**] 索引標籤中，選取您想要在自訂規則產生警示時自動執行的任何腳本。 如需建立和自動化操作手冊的詳細資訊，請參閱 [回應威脅](tutorial-respond-threats-playbook.md)。

    ![自動回應 Azure Sentinel 中的威脅](media/tutorial-detect-threats-custom/response-automation-custom.png)

1. 選取 [**審查**] 來檢查新警示規則的所有設定，然後選取 [**建立] 以初始化您的警示規則**。

   ![檢查您的自訂查詢](media/tutorial-detect-threats-custom/review-tab.png)

1.  建立警示之後，會將自訂規則新增至 [作用中**規則**] 底下的資料表。 從這份清單中，您可以啟用、停用或刪除每個規則。

1.  若要查看您所建立之警示規則的結果，請移至 [**事件**] 頁面，您可以在其中分級、 [調查事件](tutorial-investigate-cases.md)，以及補救威脅。


> [!NOTE]
> Azure Sentinel 中產生的警示可透過 [Microsoft Graph 安全性](https://aka.ms/securitygraphdocs)來取得。 如需詳細資訊，請參閱 [Microsoft Graph 安全性警示檔](https://aka.ms/graphsecurityreferencebetadocs)。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已瞭解如何使用 Azure Sentinel 開始偵測威脅。

若要瞭解如何將威脅的回應自動化，請 [在 Azure Sentinel 中設定自動威脅回應](tutorial-respond-threats-playbook.md)。

