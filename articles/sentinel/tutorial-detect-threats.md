---
title: 調查警示與 Azure Sentinel 預覽 |Microsoft Docs
description: 您可以使用本教學課程，了解如何調查警示與 Azure 的 Sentinel。
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
ms.date: 03/20/2019
ms.author: rkarlin
ms.openlocfilehash: e20f6fc0dc8dbe02b09490f62ce84af12aa31b87
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621241"
---
# <a name="tutorial-detect-threats-with-azure-sentinel-preview"></a>教學課程：偵測到以 Azure Sentinel 預覽版的威脅

> [!IMPORTANT]
> Azure Sentinel 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

之後您[連接的資料來源](quickstart-onboard.md)Sentinel Azure，您要在發生可疑發生時收到通知。 為了讓您執行這項操作，Azure Sentinel 可讓您建立進階產生情況下，您可以將指定的警示規則和用以深入調查異常和您的環境中的威脅。 

本教學課程可協助您偵測與 Azure Sentinel 的威脅。
> [!div class="checklist"]
> * 建立 偵測規則
> * 回應威脅

## <a name="create-detection-rules"></a>建立 偵測規則

若要調查的情況下，您首先必須建立偵測規則。 

> [!NOTE]
> 在 Azure Sentinel 所產生的警示都是透過[Microsoft Graph Security](https://aka.ms/securitygraphdocs)。 請參閱[Microsoft Graph 安全性警示文件](https://aka.ms/graphsecurityreferencebetadocs)詳情及整合合作夥伴。

偵測規則類型為基礎的威脅與異常狀況可能是您想要立即了解您環境中的可疑確保它們呈現、 調查，並進行補救。 

1. 在 Azure 入口網站中 Azure Sentinel 底下，選取**Analytics**。

   ![分析](./media/tutorial-detect-threats/alert-rules.png)

2. 在頂端功能表列中，按一下 **+ 新增**。  

   ![建立警示規則](./media/tutorial-detect-threats/create-alert-rule.png)

3. 底下**建立警示規則**、 提供描述性的名稱，以及設定**嚴重性**視。 

4. 在 Log Analytics 中建立查詢，並貼到**設定警示規則**欄位。 以下是範例查詢會異常的資源數量會在 Azure 活動時警示您。

        AzureActivity
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"
        | where ActivityStatus == "Succeeded"
        | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller

   > [!NOTE]
   > 查詢長度必須介於 1 到 10000 個字元，而且不能包含 「 搜尋 *"和"union *"。


5. 在 **實體對應**區段中，使用底下的欄位**實體類型**在查詢中的資料行對應至 Azure Sentinel 可辨識的實體欄位。 針對每個欄位，對應中建立 Log Analytics 中的適當實體欄位的查詢的相關資料行。 選取相關的資料行名稱之下**屬性**。 每個實體都包含多個欄位，例如 SID、 GUID、 等等。您可以將對應的欄位，而不只是上方的層級實體的任何實體。

6. 定義警示的觸發程序條件**警示觸發程序**。 這會定義觸發警示的條件。 

7. 設定**頻率**針對查詢的執行頻率-經常每 5 分鐘，或為一天一次不常。 

8. 設定**期間**控制開啟-執行查詢的資料量的時間間隔比方說，它可以執行每隔一小時在 60 分鐘的資料。

9. 您也可以設定**歸併**。 當您想要停止從相同的事件所觸發的重複警示，隱藏是很有用。 如此一來，您可以停止在特定期間內觸發的警示。 這可協助您避免警示重複相同的事件，並讓您隱藏連續警示一段時間。 比方說，如果**警示排程** **頻率**設定為 60 分鐘，而**警示排程週期**為兩個小時，和查詢結果超過定義臨界值，就會觸發警示兩次之後當第一次偵測到在過去的 60 分鐘，, 並再次時在 2 小時的資料取樣的前 60 分鐘。 我們建議如果便會觸發警示，隱藏項目應該可以在警示期間所設定的時間。 在我們的範例中，您可能要設定隱藏項目 60 分鐘，以便在最近一小時期間發生的事件，只會觸發警示。

8. 貼上您查詢後**設定警示規則**欄位中，您可以立即查看在警示的模擬**邏輯警示模擬**，讓您能夠了解資料量會產生一段特定時間間隔，如您所建立的警示。 這將取決於您為設定**頻率**並**閾值**。 如果您看到，平均而言，您就會觸發警示頻率太高，您會想要設定較高的結果數目，使其值高於平均的基準。

9. 按一下 **建立**來初始化您的警示規則。 建立警示之後，案例會建立包含警示。 您可以看到已定義的偵測規則中的資料列**安全性分析** 索引標籤。您也可以看到符合的項目集的每個規則觸發的警示數目。 從這份清單中，您可以啟用、 停用，或刪除每個規則。 您可以也右邊選取來編輯、 停用、 複製、 顯示相符項目，或刪除規則的每個警示的資料列結尾的省略符號 （...）。 **Analytics**頁面是所有作用中警示規則的資源庫，包括範本您啟用並根據範本建立的警示規則。

1. 中可以看到的警示規則的結果**案例**頁面上，其中您可以分類[調查案例](tutorial-investigate-cases.md)，和修復威脅。



## <a name="respond-to-threats"></a>回應威脅

Azure 的 Sentinel，可讓您使用劇本的威脅回應的兩個主要選項。 您可以設定觸發警示時，或您可以手動執行以回應警示的腳本時自動執行的劇本。

- 設定當您設定劇本時，會觸發警示時自動執行的劇本。 

- 手動執行按一下的 警示，在從腳本**檢視劇本**，然後選取要執行的劇本。




## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何開始使用 Azure Sentinel 的潛在威脅的偵測。 

若要了解如何以自動化您的回應威脅，[如何回應潛在威脅，使用自動化的腳本](tutorial-respond-threats-playbook.md)。
> [!div class="nextstepaction"]
> [因應威脅](tutorial-respond-threats-playbook.md)自動化威脅的回應。

