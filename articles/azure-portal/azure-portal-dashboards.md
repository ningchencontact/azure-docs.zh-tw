---
title: 建立和共用 Azure 入口網站儀表板 | Microsoft Docs
description: 本文說明如何建立、 自訂、 發佈及共用儀表板，在 Azure 入口網站中。
services: azure-portal
documentationcenter: ''
author: sewatson
manager: mtillman
editor: tysonn
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 07/01/2019
ms.author: kfollis
ms.openlocfilehash: 8dd1349ca9ab62484eb6693291e3b869ff079dc1
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537330"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>在 Azure 入口網站中建立和共用儀表板

儀表板可讓您在 Azure 入口網站中您的雲端資源的建立專注且妥善管理檢視。 工作區，其中您可以快速啟動日常作業的工作及監視資源使用儀表板。  建置自訂的儀表板，例如根據專案、 工作或使用者角色。  Azure 入口網站會提供預設的儀表板，做為起點。 您可以編輯預設儀表板、 建立和自訂其他儀表板，和發佈共用，讓其他使用者可用的儀表板。 本文說明如何建立新的儀表板、 自訂介面，並發行並共用儀表板。

## <a name="create-a-new-dashboard"></a>建立新的儀表板

在此範例中，我們會建立新的、 私用儀表板，並指派名稱。 請遵循下列步驟來開始：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取 **儀表板**從左側資訊看板的上方區段。 預設檢視可能已設定的儀表板。
1. 選取  **+ 新的儀表板**。

    ![預設儀表板的螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-new.png)

4. 這個動作會開啟**圖格庫**，您會從此處選取圖格和空的方格，您將在其中排列磚。

    ![磚庫和空的方格窗格的螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-name.png)

5. 選取 **我的儀表板**儀表板中的文字標籤，然後輸入可協助您輕鬆地識別自訂的儀表板的名稱。
1. 選取 **完成自訂**結束頁面標頭中編輯模式。

儀表板檢視現在會顯示空白儀表板。 選取查看儀表板可供您的儀表板名稱旁的下拉式清單，清單可能包含其他使用者已建立並共用儀表板。

## <a name="edit-a-dashboard"></a>編輯儀表板

現在，讓我們編輯儀表板新增、 調整大小，並安排代表您的 Azure 資源的圖格。

### <a name="add-tiles"></a>新增圖格

若要新增磚的儀表板，請遵循下列步驟：
1. 選取 ![編輯圖示](./media/azure-portal-dashboards/dashboard-edit-icon.png)**編輯**從頁首。

    ![反白顯示 編輯儀表板的螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-edit.png)

2. 瀏覽**圖格庫**或使用 [搜尋] 欄位以尋找您想要的圖格。
1. 選取 **新增**將會自動將磚加入儀表板預設大小和位置。 或者，拖曳到格線中磚，並將它放您想要的位置。

許多資源頁面 （也稱為 「 刀鋒視窗 」） 的命令列中包含圖釘圖示。 如果您選取的圖示，表示 [來源] 頁面的圖格是釘選到目前作用中的儀表板中。 這個方法是將磚新增到您的儀表板的替代方式。

![將命令列頁面釘選圖示的螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-pin-blade.png)

> [!TIP]
> 如果您使用多個組織時，新增**組織身分識別**能夠清楚顯示資源的組織在儀表板的圖格屬於。
>
>
### <a name="resize-or-rearrange-tiles"></a>調整大小或重新排列磚
若要變更磚的大小，或重新排列儀表板上的磚，請遵循下列步驟：

1. 選取 ![編輯圖示](./media/azure-portal-dashboards/dashboard-edit-icon.png)**編輯**從頁首。
1. 選取磚右上角的操作功能表。 然後選擇 並排顯示大小。 支援任何規模的磚右下角，可讓您將圖格拖曳至您想要的大小也包含 「 控制代碼 」。

   ![含有圖格大小 功能表開啟儀表板的螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-tile-resize.png)

3. 選取圖格，並將它拖曳到新位置中，在方格中，若要排列儀表板上。

### <a name="additional-tile-configuration"></a>其他的圖格設定

某些磚可能需要更多的設定，以顯示您想要的資訊。 例如，**度量圖表**圖格已設定要顯示與計量**Azure 監視器**。 您也可以自訂要覆寫儀表板的預設時間設定圖格資料。

要設定顯示任何圖格**設定圖格**橫幅直到您自訂的圖格。 選取該橫幅中，然後執行必要的設定。

![需要使用組態資訊的圖格的螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> 將 markdown 圖格可讓您在儀表板上顯示自訂的靜態內容。 這可以是基本指示、 影像、 超連結、 一組，或甚至連絡資訊。 如需使用 markdown 磚的詳細資訊，請參閱 <<c0> [ 使用自訂的 markdown 磚](azure-portal-markdown-tile.md)。
>
>
### <a name="customize-tile-data"></a>自訂圖格資料

儀表板上的資料會自動顯示在過去 24 小時內的活動。 若要顯示此圖格的不同的時間長度，請遵循下列步驟：

1. 選取 ![篩選器圖示](./media/azure-portal-dashboards/dashboard-filter.png)從選取之磚的左上角的篩選圖示**自訂圖格資料**從內容功能表。

   ![並排顯示內容功能表的螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

2. 選取核取方塊**覆寫磚層級的儀表板時間設定**。

   ![對話方塊的螢幕擷取畫面，若要設定圖格的時間設定](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

3. 選擇要顯示此磚的時間範圍。 您可以選擇從過去 30 分鐘到過去 30 天，或定義自訂的範圍。
1. 選擇要顯示的時間粒度。 您可以顯示任何地方從一分鐘的增量一個月。
1. 選取 [套用]  。

## <a name="delete-a-tile"></a>刪除圖格

若要移除儀表板圖格，請遵循下列步驟：

* 選取磚右上角的操作功能表，然後選取**從 儀表板移除**。 或者，
* 選取 ![編輯圖示](./media/azure-portal-dashboards/dashboard-edit-icon.png)**編輯**進入自訂模式。 將游標停在右上角的磚，然後選取![刪除圖示](./media/azure-portal-dashboards/dashboard-delete-icon.png)刪除圖示從儀表板移除圖格。

   ![螢幕擷取畫面顯示如何從儀表板移除磚](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>複製儀表板

若要使用現有的儀表板做為範本，為新的儀表板，請遵循下列步驟：

1. 請確定儀表板檢視會顯示您想要複製的儀表板。
1. 在頁首中，選取![複製圖示](./media/azure-portal-dashboards/dashboard-clone.png)**複製**。
1. 一份儀表板，名為"的複製*儀表板名稱*」 就會在編輯模式。 這篇文章中使用上述步驟，重新命名，並自訂儀表板。

## <a name="publish-and-share-a-dashboard"></a>發佈及共用儀表板

當您建立儀表板時，它是私用的預設值，這表示您是唯一一個人可以看到它。 若要讓儀表板可供他人使用，您可以將它們與其他使用者共用。 首先，您必須發佈儀表板設為 Azure 資源。 若要發佈及共用自訂的儀表板，請遵循下列步驟：

1. 選取 ![共用圖示](./media/azure-portal-dashboards/dashboard-share-icon.png)**共用**從頁首。 **共用 + 存取控制**表單隨即出現。
1. 確認已顯示正確的儀表板名稱。
1. 選取 **訂用帳戶名稱**。 具有訂用帳戶的存取權的使用者可以使用共用的儀表板。 由個別的圖格表示資源的存取權是由 Azure 角色型存取控制決定。
1. 選取將此儀表板發佈到選取的訂用帳戶中的 '儀表板' 的資源群組的核取方塊。 或者，請清除此核取方塊，並選擇改為發行至現有的資源群組。
1. 選擇儀表板資源的位置。 我們建議您找出與其他資源的儀表板。 注意： 如果您選擇從現有的資源群組，儀表板位於自動與該資源群組。
1. 選取 [發行]。 

   ![儀表板發佈對話方塊的螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-publish.png)

### <a name="set-access-control-on-a-shared-dashboard"></a>設定共用的儀表板上的存取控制

發行儀表板之後，管理誰能夠存取的儀表板依照下列步驟：

1. 在 **共用 + 存取控制**窗格中，選取**管理使用者**。

   ![螢幕擷取畫面的儀表板共用及存取控制 對話方塊](./media/azure-portal-dashboards/dashboard-share-access-control.png)

2. **存取控制**頁面隨即開啟。 在此頁面上，您可以檢閱其他人存取層級，或加入新的角色指派。 當您新增的角色指派時，您授與權限的儀表板。

> [!NOTE]
> 圖格是代表性檢視您的組織中的資源。 透過角色型存取控制指派管理資源的存取權和權限繼承自下資源的訂用帳戶。 提供存取權儀表板不會自動指派權限給儀表板上顯示的資源。 如需有關共用的儀表板和資源的角色型存取控制的權限的詳細資訊，請參閱[共用儀表板使用角色型存取控制](azure-portal-dashboard-share-access.md)。

### <a name="open-a-shared-dashboard"></a>開啟共用的儀表板

若要尋找及開啟共用的儀表板，請遵循下列步驟：

1. 選取儀表板名稱旁的下拉式清單。
1. 從顯示的儀表板清單中選取或**瀏覽所有儀表板**如果未列出您想要開啟儀表的板。

   ![儀表板選取功能表的螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-browse.png)

3. 在 **型別**欄位中，選取**共用儀表板**。
1. 選取一或多個訂用帳戶。 您也可以輸入文字以依名稱篩選儀表板。
1. 從共用的儀表板清單中選取儀表板。

## <a name="delete-a-dashboard"></a>刪除儀表板

若要永久刪除私用或共用的儀表板，請遵循下列步驟：

1. 選取您想要從儀表板名稱旁的下拉式清單中刪除儀表的板。
1. 選取 ![刪除圖示](./media/azure-portal-dashboards/dashboard-delete-icon.png)**刪除**從頁首。
1. 為私用儀表板中，選取**確定**在確認對話方塊中，若要移除儀表板。 共用的儀表板，在確認對話方塊中，選取核取方塊，確認 已發佈的儀表板將再也可供其他人檢視。 然後選取 [確定]  。

   ![確認刪除的螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>後續步驟

* [使用角色型存取控制共用儀表板](azure-portal-dashboard-share-access.md)
* [以程式設計方式建立 Azure 儀表板](azure-portal-dashboards-create-programmatically.md)
