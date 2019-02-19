---
title: 教學課程 - 建立及管理 Azure 成本管理的匯出資料 |Microsoft Docs
description: 本文說明如何建立及管理匯出的 Azure 成本管理資料來將其用於外部系統中。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/05/2019
ms.topic: tutorial
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: a7c503fba534b72323472fa58b14188bc412003c
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100681"
---
# <a name="tutorial-create-and-manage-exported-data"></a>教學課程：建立和管理匯出的資料

若您已閱讀過成本分析教學課程，您便已對手動下載成本管理資料相當熟悉。 但是，您可以建立週期性工作，每天、每週或每個月自動將您的成本管理資料匯出至 Azure 儲存體。 匯出的資料格式為 CSV，其中包含所有由成本管理收集的資訊。 您接著可以在 Azure 儲存體中，搭配外部系統使用匯出的資料，並與您擁有的自訂資料合併。 您也可以在外部系統 (例如儀表板或其他財務系統) 中使用您的匯出資料。

本教學課程中的範例會引導您匯出成本管理資料，並驗證該資料已成功匯出。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 建立每日匯出
> * 驗證已收集資料

## <a name="prerequisites"></a>必要條件
資料匯出適用於各種不同的 Azure 帳戶類型，包括 [Enterprise 合約 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) 客戶。 若要檢視所支援帳戶類型的完整清單，請參閱[了解成本管理資料](understand-cost-mgt-data.md)。 使用者及群組資料匯出的每個訂用帳戶皆支援下列 Azure 權限：

- 擁有者 - 可以建立、修改或刪除已排程的訂用帳戶匯出。
- 參與者 - 可以建立、修改或刪除他們自己的排程匯出。 可以修改其他人所建立的排程匯出名稱。
- 讀者 - 可以排程擁有其權限的匯出。

針對 Azure 儲存體帳戶：
- 需要寫入權限，才能變更設定的儲存體帳戶，無論匯出的權限為何。
- 您的 Azure 儲存體帳戶必須針對 Blob 或檔案儲存體進行設定。

## <a name="sign-in-to-azure"></a>登入 Azure
在 [https://portal.azure.com](https://portal.azure.com/) 登入 Azure 入口網站。

## <a name="create-a-daily-export"></a>建立每日匯出

[成本管理 + 帳單] &gt; [成本管理] &gt; 選取訂用帳戶或訂用帳戶中的資源群組 &gt; [匯出] &gt; [新增]。

輸入匯出的名稱，然後選取 [每日匯出月初至今的成本] 選項。 按 [下一步] 。

![顯示匯出類型的新匯出範例](./media/tutorial-export-acm-data/basics_exports.png)

為您的 Azure 儲存體帳戶名稱指定訂用帳戶，然後選取您的儲存體帳戶。  指定您希望匯出檔案移至的儲存體容器和目錄路徑。  按 [下一步] 。

![顯示儲存體帳戶詳細資料的新匯出範例](./media/tutorial-export-acm-data/storage_exports.png)

檢閱匯出詳細資料，然後按一下 [建立]。

您新增的匯出會出現在匯出清單中。 根據預設會啟用新的匯出。 若您想要停用或刪除已排程的匯出，請按一下清單中的任何項目，然後按一下 [停用] 或 [刪除]。

剛開始可能會需要一到兩個小時，匯出才會開始執行。 但是，資料可能需要最多四個小時，才會出現在匯出檔案中。

### <a name="export-schedule"></a>匯出排程

排程的匯出會受您最初建立匯出時的時間和星期幾所影響。 當您建立排程的匯出時，匯出會針對後續的每次匯出在一天的相同時間執行。 例如，您會在下午 1:00 建立每日匯出。 下一次匯出會在隔天下午 1:00 執行。 目前時間會以相同方式影響所有其他匯出類型 — 一律會在您最初建立匯出時的一天相同時間執行。 在不同範例中，您可在星期一下午 4:00 建立每週匯出。 下一份報告會在下個星期一下午 4:00 執行。 *匯出的資料可在執行階段的四小時內取得。*

每次匯出都會建立一個新檔案，因此不會覆寫較舊的匯出。

匯出類型有三種：

**每日匯出當月成本**– 初始匯出會立即執行。 後續匯出會在隔天與初始匯出相同的時間執行。 系統會從先前的每日匯出彙總最新資料。

**過去 7 天費用的每週匯出** – 初始匯出會立即執行。 後續匯出會在與初始匯出相同的時間和星期幾執行。 成本屬於過去七天。

**自訂** – 可讓您使用當週和當月選項來排程每週和每月匯出。 *初始匯出會立即執行。*

![[新匯出 - 基本資料] 索引標籤，其中顯示自訂每週的當週選取項目](./media/tutorial-export-acm-data/tutorial-export-schedule-weekly-week-to-date.png)

## <a name="verify-that-data-is-collected"></a>驗證已收集資料

您可以輕鬆驗證您的成本管理資料已收集完成，並使用 Azure 儲存體總管檢視匯出的 CSV 檔案。

在匯出清單中，按一下儲存體帳戶名稱。 在儲存體帳戶頁面上，按一下 [Open in Explorer] \(在總管中開啟\)。 若您看見確認方塊，請按一下 [是] 以在 Azure 儲存體總管中開啟檔案。

![顯示範例資訊和 [在總管中開啟] 連結的儲存體帳戶頁面](./media/tutorial-export-acm-data/storage-account-page.png)

在 [儲存體總管] 中，巡覽至您想要開啟的容器，然後選取對應到目前月份的資料夾。 隨即顯示 CSV 檔案清單。 選取其中一個，然後按一下 [開啟]。

![[儲存體總管] 中顯示的範例訊息](./media/tutorial-export-acm-data/storage-explorer.png)

檔案隨即便會使用設為開啟 CSV 檔案副檔名的程式或應用程式開啟。 以下是 Excel 中的範例。

![Excel 中顯示的範例匯出 CSV 資料](./media/tutorial-export-acm-data/example-export-data.png)

## <a name="access-exported-data-from-other-systems"></a>從其他系統存取匯出資料

匯出成本管理資料的其中一個目的是從外部系統存取資料。 您可能會使用儀表板系統或其他財務系統。 這類系統之間的差異相當大，因此顯示範例並不實用。  但是，您可以從 [Azure 儲存體簡介](../storage/common/storage-introduction.md)中應用程式存取您的資料開始。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 建立每日匯出
> * 驗證已收集資料

前進到下一個教學課程，透過識別閒置及使用量過低的資源來最佳化和改善效率。

> [!div class="nextstepaction"]
> [檢閱並針對建議採取動作](tutorial-acm-opt-recommendations.md)
