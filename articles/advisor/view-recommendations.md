---
title: 檢視 Azure Advisor 建議，對您來說重要
description: 檢視和篩選 Azure Advisor 的建議，以減少雜訊。
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 04/03/2019
ms.author: kasparks
ms.openlocfilehash: 9f599a63fd5f52420f1b79e769d4f7bca9683b32
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60467843"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>檢視 Azure Advisor 建議，對您來說重要

Azure Advisor 提供建議來協助您最佳化您的 Azure 部署。 內 Advisor，您有幫助您縮小您的建議只對您來說重要的幾個功能的存取權。

## <a name="configure-subscriptions-and-resource-groups"></a>設定訂用帳戶和資源群組

Advisor 可讓您選取的訂用帳戶和對您和貴組織的資源群組。 此外，您只會看到建議的訂用帳戶和您選取的資源群組。 根據預設，會選取所有。 組態設定會套用至訂用帳戶或資源群組，所以相同的設定會套用到每個人都可以存取該訂用帳戶或資源群組的範圍。 在 Azure 入口網站中或以程式設計方式，就可以變更組態設定。

若要在 Azure 入口網站中進行變更：

1. 開啟[Azure Advisor](https://aka.ms/azureadvisordashboard)在 Azure 入口網站中。

1. 選取 **組態**從功能表。

   ![Advisor 設定功能表](./media/view-recommendations/configuration.png)

1. 核取方塊**Include**任何訂用帳戶或資源群組，若要收到 Advisor 建議的資料行。 如果已停用 方塊中，您可能沒有進行組態變更該訂用帳戶或資源群組的權限。 深入了解[Azure Advisor 中的權限](permissions.md)。

1. 按一下 **套用**底部之後進行變更。

## <a name="filtering-your-view-in-the-azure-portal"></a>篩選您在 Azure 入口網站中的檢視

組態設定保持作用中，直到變更為止。 如果您想要限制的單一檢視建議的檢視，您可以使用提供的 [Advisor] 面板頂端的下拉式清單。 您可以從概觀、 高可用性、 安全性、 效能、 成本和所有的建議面板中，選取訂用帳戶、 資源類型，以及您想要查看的建議狀態。

   ![Advisor 篩選功能表](./media/view-recommendations/filtering.png)

## <a name="dismissing-and-postponing-recommendations"></a>關閉和延後建議

Azure Advisor 可讓您關閉或延後到單一資源的建議。 如果您關閉建議，您看不它再次除非您以手動方式啟動它。 不過，正在延後建議可讓您指定 持續時間之後，建議會自動重新啟動。 正在延後即可在 Azure 入口網站中或以程式設計的方式。

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>延後在 Azure 入口網站中的單一建議 

1. 開啟[Azure Advisor](https://aka.ms/azureadvisordashboard)在 Azure 入口網站中。
1. 選取一個建議類別以檢視建議
1. 從建議清單中選取建議
1. 選取您想要延期或關閉的建議延期或關閉

     ![Advisor 篩選功能表](./media/view-recommendations/postpone-dismiss.png)

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>延期或關閉 Azure 入口網站中的多個建議

1. 開啟[Azure Advisor](https://aka.ms/azureadvisordashboard)在 Azure 入口網站中。
1. 選取一個建議類別以檢視建議。
1. 從建議清單中選取建議。
1. 選取您想要延期或關閉建議的所有資源的資料列左邊的核取方塊。
1. 選取 **延遲**或是**解除**在左上方的資料表。

     ![Advisor 篩選功能表](./media/view-recommendations/postpone-dismiss-multiple.png)

> [!NOTE]
> 您需要關閉或延後建議的 「 參與者 」 或 「 擁有者權限。 深入了解 Azure Advisor 中的權限。

> [!NOTE]
> 如果選取方塊會停用，可能仍會載入建議。 請等候載入嘗試要延期或關閉之前的所有建議。

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>重新啟動延後或已解除的建議

您可以啟用延後或已解除的建議。 在 Azure 入口網站中或以程式設計方式，就可以完成此動作。 在 Azure 入口網站中：

1. 開啟[Azure Advisor](https://aka.ms/azureadvisordashboard)在 Azure 入口網站中。

1. 變更至 [概觀] 面板中的篩選條件**延後**。 然後，advisor 會顯示延後或已解除的建議。

    ![Advisor 篩選功能表](./media/view-recommendations/activate-postponed.png)

1. 選取類別，請參閱**延後**並**已解除**建議。

1. 從建議清單中選取建議。 這會開啟與建議**延後和關閉**已經選取要顯示的資源，這項建議已延遲或關閉索引標籤。

1. 按一下  **Activate**結尾的資料列。 一旦按下，建議會是該資源的使用中，因此從這個資料表中移除。 建議現在會顯示在**Active**  索引標籤。
 
     ![Advisor 篩選功能表](./media/view-recommendations/activate-postponed-2.png)

## <a name="next-steps"></a>後續步驟

這篇文章說明如何檢視 Azure Advisor 中，對您來說重要的建議。 若要深入了解 Advisor，請參閱︰ 

- [何謂 Azure 建議程式？](advisor-overview.md)
- [開始使用 Advisor](advisor-get-started.md)
- [Azure Advisor 中的權限](permissions.md)



