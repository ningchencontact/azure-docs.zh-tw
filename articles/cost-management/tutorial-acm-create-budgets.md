---
title: 教學課程 - 建立和管理 Azure 預算 | Microsoft Docs
description: 此教學課程可協助規劃和說明您取用之 Azure 服務的成本。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: cost-management
manager: adwise
ms.custom: seodec18
ms.openlocfilehash: 0cae5166fbbba650b270829b9c8e3711b12a574e
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2019
ms.locfileid: "71073942"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>教學課程：建立和管理 Azure 預算

成本管理中的預算可協助您進行規劃並促進組織責任歸屬。 透過預算，您可以說明您在特定期間所取用或訂閱的 Azure 服務。 它們可協助您通知其他人其費用的相關資訊以主動管理成本，並監視費用在經過一段時間的進展方式。 當超過您所建立的預算閾值時，只會觸發通知。 您的資源都會不受到影響，而您的使用量並不會停止。 當您分析成本時，您可以使用預算來比較及追蹤費用。

成本和使用方式資料通常會在8-12 小時內提供，而預算會每四小時評估一次這些成本。 通常會在12-16 小時內收到電子郵件通知。

當您在未來選取到期日時，預算會在期間 (每月、每季或每年) 結束時自動重設相同的預算金額。 因為它們會使用相同的預算金額重設，所以，在預算的貨幣金額與未來期間不同時您需要另外建立預算。

此教學課程的範例會引導您針對 Azure Enterprise 合約 (EA) 訂用帳戶建立和編輯預算。

觀看[如何建立預算以透過 Azure 成本管理影片監視您的支出](https://www.youtube.com/watch?v=ExIVG_Gr45A)，以瞭解如何在 Azure 中建立預算來監視支出。


在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 在 Azure 入口網站中建立預算
> * 編輯預算

## <a name="prerequisites"></a>必要條件

預算功能支援各種不同的 Azure 帳戶類型。 若要檢視所支援帳戶類型的完整清單，請參閱[了解成本管理資料](understand-cost-mgt-data.md)。 若要檢視預算，您至少需要 Azure 帳戶的讀取存取。

 針對 Azure EA 訂用帳戶，您必須具備檢視預算的讀取存取權。 若要建立及管理預算，您必須具有參與者權限。 您可以個別為 EA 訂用帳戶和資源群組建立預算。 不過，您無法為 EA 帳單帳戶建立預算。

依使用者和群組的預算，每個訂用帳戶都支援下列 Azure 許可權（或範圍）。 如需有關範圍的詳細資訊，請參閱[了解並使用範圍](understand-work-scopes.md)。

- 擁有者：可以建立、修改或刪除訂用帳戶的預算。
- 參與者和成本管理參與者 – 可以建立、修改或刪除他們自己的預算。 可以修改其他人所建立之預算的預算金額。
- 讀者和成本管理讀者 – 可以檢視他們有權限的預算。

如需成本管理資料的指派權限詳細資訊，請參閱[指派成本管理資料的存取權](assign-access-acm-data.md)。

## <a name="sign-in-to-azure"></a>登入 Azure

- 在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="create-a-budget-in-the-azure-portal"></a>在 Azure 入口網站中建立預算

您可以針對每月、每季或每年期間建立 Azure 訂用帳戶預算。 您在 Azure 入口網站中的導覽內容會決定您是要為訂用帳戶或管理群組建立預算。

若要建立或查看預算，請在 Azure 入口網站中開啟所需的範圍，然後選取功能表中的 [**預算**]。 例如，流覽至 [訂用帳戶]、從清單中選取**訂閱，然後**選取功能表中的 [**預算**]。 使用**範圍**膠囊按鈕切換至不同的範圍，例如預算中的管理群組。 如需有關範圍的詳細資訊，請參閱[了解並使用範圍](understand-work-scopes.md)。

建立預算之後，會針對它們顯示您目前費用的簡單檢視。

按一下 [新增]。

![顯示已建立預算清單的範例](./media/tutorial-acm-create-budgets/budgets01.png)

在 [**建立預算**] 視窗中，確認顯示的範圍正確。 選擇您想要新增的任何篩選。 篩選器可讓您針對特定成本建立預算，例如訂用帳戶中的資源群組或虛擬機器之類的服務。 您可以在成本分析中使用的任何篩選準則也可以套用至預算。

在您識別出範圍和篩選器之後，請輸入預算名稱。 然後，選擇 [每月]、[每季] 或 [年度預算重設週期]。 這個重設期間會決定由預算分析的時間範圍。 預算所評估的成本從每個新期間開頭的零開始。 當您建立每季預算時，它的運作方式與與每月預算相同。 差異在於當季預算金額會平均分配於當季的三個月。 年度預算金額會平均分配給日曆年度的所有12個月。

如果您有隨用隨付、MSDN 或 Visual Studio 訂用帳戶，則您發票的計費期間可能與行事曆月份不同。 針對這些訂用帳戶類型和資源群組，您可以建立與您的發票期間或日曆月份一致的預算。 若要建立與發票期間一致的預算，請選取 [**計費月份**]、[**計費季**] 或 [**計費年份**] 的重設期間。 若要建立與日曆月份對齊的預算，請選取**每月**、**每季**或**每年**的重設週期。

接下來，識別預算失效的到期日，並停止評估成本。

根據目前在預算中選擇的欄位，會顯示圖表以協助您選取要用於預算的臨界值。 建議的預算是根據您在未來期間可能產生的最高預測成本。 您可以變更預算金額。

![顯示以每月成本資料建立預算的範例 ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

設定預算金額之後，請按 **[下一步]** 設定預算警示。 預算需要至少一個成本閾值 (預算的百分比) 與對應的電子郵件地址。 您可以選擇性地在單一預算中最多包含五個閾值與五個電子郵件地址。 當達到預算閾值時，通常會在 8 小時內收到電子郵件通知。 如需有關通知的詳細資訊，請參閱[使用成本警示](cost-mgt-alerts-monitor-usage-spending.md)。 在下列範例中，當達到 90% 的預算時，便會產生電子郵件警示。

![顯示警示條件的範例](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

建立預算之後，即會在成本分析中顯示它。 當您開始[分析成本和費用](quick-acm-cost-analysis.md)時，首要步驟之一就是檢視與您費用趨勢相關的預算。

![成本分析中顯示的預算和支出範例](./media/tutorial-acm-create-budgets/cost-analysis.png)

在上述範例中，您已針對訂用帳戶建立預算。 不過，您也可以針對資源群組建立預算。 如果您想要針對資源群組建立預算，請瀏覽至[成本管理 + 計費] &gt;[訂用帳戶] &gt; 選取訂用帳戶 > [資源群組] > 選取資源群組 > [預算] > 然後**新增**預算。

## <a name="trigger-an-action-group"></a>觸發動作群組

當您建立或編輯訂用帳戶或資源群組範圍的預算時，可以將它設定為呼叫動作群組。 當達到預算閾值時，動作群組可以執行各種不同的動作。 如需有關動作群組的詳細資訊，請參閱在[Azure 入口網站中建立和管理動作群組](../azure-monitor/platform/action-groups.md)。 如需使用以預算為基礎的自動化和動作群組的詳細資訊，請參閱[使用 Azure 預算管理成本](../billing/billing-cost-management-budget-scenario.md)。

若要建立或更新動作群組，請在建立或編輯預算時，按一下 [**管理動作群組**]。

![建立預算以顯示管理動作群組的範例](./media/tutorial-acm-create-budgets/manage-action-groups01.png)


接下來，按一下 [**新增動作群組**] 並建立動作群組。


![[新增動作群組] 方塊的影像](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

建立動作群組之後，請關閉此方塊以返回您的預算。

設定您的預算，以在符合個別閾值時使用您的動作群組。 最多支援五個不同的閾值。

![顯示警示條件之動作群組選取的範例](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

下列範例顯示將預算閾值設定為 50%、75% 和 100%。 每個都會設定為在指定的動作群組內觸發指定的動作。

![顯示使用各種動作群組和動作類型設定之警示條件的範例](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 在 Azure 入口網站中建立預算
> * 編輯預算

請前進到下一個教學課程，以建立成本管理資料的週期性匯出。

> [!div class="nextstepaction"]
> [建立和管理匯出的資料](tutorial-export-acm-data.md)
