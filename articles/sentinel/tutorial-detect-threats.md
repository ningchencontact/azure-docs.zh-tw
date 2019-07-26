---
title: 使用 Azure Sentinel 預覽來調查警示 |Microsoft Docs
description: 使用本教學課程, 瞭解如何使用 Azure Sentinel 來調查警示。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: b5fbc5ac-68b2-4024-9c1b-bd3cc41a66d0
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2019
ms.author: rkarlin
ms.openlocfilehash: ad9c752898733286701db2d0f0b1fc40029b7521
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2019
ms.locfileid: "68370700"
---
# <a name="tutorial-detect-threats-with-azure-sentinel-preview"></a>教學課程：使用 Azure Sentinel Preview 偵測威脅

> [!IMPORTANT]
> Azure Sentinel 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

在您將[資料來源連接](quickstart-onboard.md)到 Azure Sentinel 之後, 您會想要在發生可疑的情況時收到通知。 為了讓您執行這項操作, Azure Sentinel 可讓您建立高階警示規則, 以產生可指派的案例, 並用於深入調查環境中的異常和威脅。 

本教學課程可協助您偵測 Azure Sentinel 的威脅。
> [!div class="checklist"]
> * 建立偵測規則
> * 將威脅回應自動化

## <a name="create-detection-rules"></a>建立偵測規則

若要調查案例, 您必須先建立偵測規則。 

> [!NOTE]
> Azure Sentinel 中產生的警示可透過[Microsoft Graph 安全性](https://aka.ms/securitygraphdocs)來取得。 請參閱[Microsoft Graph 安全性警示檔](https://aka.ms/graphsecurityreferencebetadocs), 以取得進一步的詳細資料和整合合作夥伴。

偵測規則是根據您想要立即瞭解的環境中可能會有疑問的威脅和異常類型, 確保其呈現、調查及補救。 

1. 在 Azure Sentinel 的 Azure 入口網站中, 選取 **分析**。

   ![分析](./media/tutorial-detect-threats/alert-rules.png)

2. 在頂端功能表列中, 按一下 [ **+ 新增**]。  

   ![建立警示規則](./media/tutorial-detect-threats/create-alert-rule.png)

3. 在 [**建立警示規則**] 底下, 提供描述性的名稱, 並視需要設定**嚴重性**。 

4. 在 Log Analytics 中建立查詢, 然後將它貼入 [**設定警示規則**] 欄位中。 以下是一個範例查詢, 會在 Azure 活動中建立了異常數量的資源時發出警示。

        AzureActivity
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"
        | where ActivityStatus == "Succeeded"
        | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller

   > [!NOTE]
   > 查詢長度應介於1到10000個字元之間, 且不能包含 "search *" 和 "union *"。


5. 在 [**實體對應**] 區段中, 使用 [**實體類型**] 底下的欄位, 將查詢中的資料行對應至 Azure Sentinel 所識別的實體欄位。 針對每個欄位, 將您在 Log Analytics 中建立之查詢中的相關資料行對應至適當的實體欄位。 在**屬性**下選取相關的資料行名稱。 每個實體都包含多個欄位, 例如 SID、GUID 等。您可以根據任何欄位來對應實體, 而不只是最上層實體。

6. 在 [**警示觸發**程式] 下定義警示觸發程式條件。 這會定義觸發警示的條件。 

7. 設定查詢執行頻率的**頻率**-頻率是每隔5分鐘, 或一天不常進行一次。 

8. 設定**期間**以控制查詢執行多少資料的時間範圍, 例如, 它可以每小時在資料的60分鐘內執行。

9. 您也可以設定**隱藏**專案。 當您想要停止針對相同事件觸發重複的警示時, 隱藏功能就很有用。 如此一來, 您就可以在特定期間停止觸發警示。 這可協助您避免相同事件的重複警示, 並允許您在一段時間內抑制連續警示。 例如, 如果 [**警示排程** **頻率**] 設定為 [60 分鐘], 而 [**警示排程期間**] 設定為兩個小時, 且查詢結果超過定義的閾值, 則會觸發警示兩次, 並在第一次偵測到在過去60分鐘內, 如果是在取樣的2小時資料的前60分鐘內, 則再次發生。 我們建議在觸發警示時, 隱藏專案應該是在警示期間所設定的時間量。 在我們的範例中, 您可能會想要將隱藏專案設定為60分鐘, 以便只針對最近一小時發生的事件觸發警示。

8. 將查詢貼入 [**設定警示規則**] 欄位之後, 您可以立即在 [**邏輯警示模擬**] 底下看到警示的模擬, 讓您能夠瞭解在警示的特定時間間隔內將產生多少資料。您已建立。 這將取決於您為 [**頻率**] 和 [**閾值**] 所設定的內容。 如果您看到平均觸發警示的頻率太高, 您會想要將結果數目設定為高於您的平均基準。

9. 按一下 [**建立**] 以初始化您的警示規則。 建立警示之後, 會建立一個包含警示的案例。 在 [**安全性分析**] 索引標籤中, 您可以看到已定義的偵測規則做為資料列。您也可以查看每個規則的相符專案數-觸發的警示。 從這份清單中, 您可以啟用、停用或刪除每個規則。 您也可以用滑鼠右鍵選取每個警示資料列結尾的省略號 (...), 以編輯、停用、複製、顯示相符專案或刪除規則。 [**分析**] 頁面是所有作用中警示規則的資源庫, 包括您啟用的範本, 以及根據範本所建立的警示規則。

1. 您可以在 [**案例**] 頁面中看到警示規則的結果, 您可以在其中分級、[調查案例](tutorial-investigate-cases.md), 以及補救威脅。



## <a name="automate-threat-responses"></a>將威脅回應自動化

SIEM/SOC 小組可以定期收到安全性警示。 產生的警示數量很大, 因此可用的安全性系統管理員已淹沒。 在無法調查許多警示的情況下, 這會造成太多的結果, 讓組織容易遭受未察覺的攻擊。 

許多 (如果不是多數) 這些警示都符合可由特定和定義的補救動作解決的週期性模式。 Azure Sentinel 已經可讓您在腳本中定義您的補救措施。 您也可以將即時自動化設定為您腳本定義的一部分, 讓您能夠完全自動化針對特定安全性警示所定義的回應。 藉由使用即時自動化, 回應小組可以藉由完全自動化週期性警示類型的例行回應, 大幅降低工作負載, 讓您更專注于獨特的警示、分析模式、威脅搜尋等等。

若要自動化回應:

1. 選擇您想要自動化回應的警示。
1. 從 [Azure Sentinel] 工作區導覽功能表中, 選取 [**分析**]。
1. 選取您想要自動化的警示。 
1. 在 [**編輯警示規則**] 頁面的 [**即時自動化**] 底下, 選擇要在符合此警示規則時執行的**觸發**腳本。
1. 選取 [ **儲存**]。

   ![即時自動化](./media/tutorial-detect-threats/rt-configuration.png)


此外, 您也可以手動補救警示, 方法是從警示內執行腳本, 方法是按一下 [ **View**腳本], 然後選取要執行的腳本。 若要瞭解如何建立新的腳本或編輯現有的腳本, 請參閱[使用 Azure Sentinel 中的操作手冊](tutorial-respond-threats-playbook.md)。



## <a name="next-steps"></a>後續步驟
在本教學課程中, 您已瞭解如何使用 Azure Sentinel 開始偵測威脅。 

若要瞭解如何將您對威脅的回應自動化,[如何使用自動化](tutorial-respond-threats-playbook.md)的腳本來回應威脅。
> [!div class="nextstepaction"]
> [回應威脅](tutorial-respond-threats-playbook.md), 將您對威脅的回應自動化。

