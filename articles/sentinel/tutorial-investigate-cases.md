---
title: 調查 Azure Sentinel 預覽版的案例 |Microsoft Docs
description: 您可以使用本教學課程，了解如何調查 Azure Sentinel 的案例。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: a493cd67-dc70-4163-81b8-04a9bc0232ac
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: d1da180d3b30b57ca2b69985bf3d0261b8d70c39
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65205511"
---
# <a name="tutorial-investigate-cases-with-azure-sentinel-preview"></a>教學課程：調查 Azure Sentinel 預覽版的案例

> [!IMPORTANT]
> Azure Sentinel 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本教學課程可協助您偵測與 Azure Sentinel 的威脅。

之後您[連接的資料來源](quickstart-onboard.md)Sentinel Azure，您要在發生可疑發生時收到通知。 為了讓您執行這項操作，Azure Sentinel 可讓您建立進階產生情況下，您可以將指定的警示規則和用以深入調查異常和您的環境中的威脅。 

> [!div class="checklist"]
> * 建立案例
> * 調查案例
> * 回應威脅

## <a name="investigate-cases"></a>調查案例

案例可以包含多個警示。 它是針對特定調查所有相關的辨識項的彙總。 根據您在中定義的警示建立案例**Analytics**頁面。 案例層級會設定警示，例如嚴重性與狀態相關的屬性。 您可以讓 Azure Sentinel 知道何種您想要尋求的威脅，以及如何找到它們之後，您可以監視所調查的情況下偵測到的威脅。 

1. 選取 **情況下**。 **案例** 頁面可讓您知道您有多少個案例，多少有開啟幾個您已設定為**正在**，並關閉多少。 針對每個案例中，您可以看到它的發生的時間和案例的狀態。 看看決定所要處理第一個嚴重性。 在 [**案例**頁面上，按一下**警示**來查看與案例相關的所有警示] 索引標籤。 您在先前案例的一部分可以檢視中所對應的實體**實體** 索引標籤。您可以篩選的情況下，如有需要例如依狀態或嚴重性。 當您查看**案例**索引標籤上，您會看到開啟包含您定義中的偵測規則所觸發的警示的情況下**Analytics**。 在頂端，您會看到您的使用中的情況下，新的情況下，在進行的情況下。 您也可以依嚴重性，查看您所有的案例的概觀。

   ![警示儀表板](./media/tutorial-investigate-cases/cases.png)

2. 若要開始調查，請按一下特定的案例。 在右側，您可以看到詳細的資訊，包括其嚴重性，涉及的實體數目的摘要 （根據您的對應） 的案例。 每個案例都有唯一的識別碼。 案例的嚴重性是根據最嚴重的警示，包含在案例中決定的。  

1. 若要檢視更多詳細的警示和實體，在案例中，按一下**檢視的完整詳細資料**萬一頁面，並檢閱相關索引標籤可彙總案例的資訊。  完整案例檢視會彙總警示的相關聯的警示和實體中的所有識別項。

1. 在 **警示**索引標籤上，檢閱警示本身-它觸發時，並由它超過您所設定的臨界值的量。 您可以看到所有的相關資訊，關於警示 – 觸發警示，每個查詢，而且能夠執行腳本警示上傳回的結果數目的查詢。 若要向下鑽研相應減少更進一步成案例中，按一下 叫用次數。 這會開啟查詢產生的結果以及觸發的警示，Log Analytics 中的結果。

3. 在 **實體**索引標籤上，您可以看到您所對應的所有實體的警示規則定義的一部分。 

4. 如果您正在主動調查的情況下，它是個不錯的主意，寫狀態設為**正在**直到您關閉它為止。 您也可以關閉的情況下，其中**關閉已解決**狀態的情況下，表示事件已處理，而**關閉 已解除**是不需要處理的情況下的狀態。 需要說明您的理由關閉案例的說明。

5. 案例可以指派給特定使用者。 每個案例可以指派為擁有者，藉由設定案例**擁有者**欄位。 為 未指派的所有情況下啟動。 您可以進入的情況下，並篩選您的名稱，以查看您所擁有的所有案例。 

5. 按一下 **調查**檢視調查地圖，以及含有補救步驟缺口的範圍。 



## <a name="respond-to-threats"></a>回應威脅

Azure 的 Sentinel，可讓您使用劇本的威脅回應的兩個主要選項。 您可以設定觸發警示時，或您可以手動執行以回應警示的腳本時自動執行的劇本。

- 您可以設定當您設定劇本時，會觸發警示時自動執行的劇本。 

- 您可以手動執行從腳本內的警示中，依序按一下**檢視劇本**，然後選取要執行的劇本。




## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何開始調查情況下，使用 Azure 的 Sentinel。 繼續進行教學課程[如何回應潛在威脅，使用自動化的腳本](tutorial-respond-threats-playbook.md)。
> [!div class="nextstepaction"]
> [因應威脅](tutorial-respond-threats-playbook.md)自動化威脅的回應。

