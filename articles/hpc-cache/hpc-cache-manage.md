---
title: 管理及更新 Azure HPC Cache （預覽）
description: 如何使用 Azure 入口網站來管理及更新 Azure HPC 快取
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: rohogue
ms.openlocfilehash: b5607f1d97e4e240a86ba8e7800af7cb21083217
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952013"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>從 Azure 入口網站管理您的快取

Azure 入口網站中的 快取總覽 頁面會顯示您快取的專案詳細資料、快取狀態和基本統計資料。 它也具有可刪除快取、將資料排清至長期儲存或更新軟體的控制項。

若要開啟 總覽 頁面，請在 Azure 入口網站中選取您的快取資源。 例如，載入 [**所有資源**] 頁面，然後按一下快取名稱。

![Azure HPC 快取實例 [總覽] 頁面的螢幕擷取畫面](media/hpc-cache-overview.png) <!-- placeholder is identical to hpc-cache-new-overview.png; replace with better image (showing graphs, full sidebar) when available -->

頁面頂端的按鈕可協助您管理快取：

* [**Flush**](#flush-cached-data) -將所有快取的資料寫入儲存體目標
* [**升級**](#upgrade-cache-software)-更新快取軟體
* 重新**整理-重載**[總覽] 頁面
* [**刪除**](#delete-the-cache)-永久終結快取

如需這些選項的詳細資訊，請參閱下方。

## <a name="flush-cached-data"></a>清除快取的資料

[總覽] 頁面上的 [**清除**] 按鈕會指示快取立即將儲存在快取中的所有已變更資料寫入後端儲存體目標。 快取會定期將資料儲存到儲存體目標，因此，除非您想要確定後端儲存系統是最新的，否則不需要手動執行此動作。 例如，您可以在建立儲存體快照集之前使用**Flush** ，或檢查資料集大小。

> [!NOTE]
> 在清除程式期間，快取無法服務用戶端要求。 在作業完成之後，快取存取已暫停並繼續。

當您啟動快取排清作業時，快取會停止接受用戶端要求，而且 [總覽] 頁面上的快取狀態會變更為 [排清 **]。**

快取中的資料會儲存至適當的儲存體目標。 此程式可能需要幾分鐘的時間，否則可能需要一個小時以上，視最近寫入快取的資料量而定。

將所有資料儲存至儲存體目標之後，快取會自動開始再次取得用戶端要求。 快取狀態會恢復為**狀況良好**。

## <a name="upgrade-cache-software"></a>升級快取軟體

如果有新的軟體版本可用，[**升級**] 按鈕就會變成作用中狀態。 您也可能會在頁面頂端看到關於更新軟體的訊息。

![已啟用 [升級] 按鈕之按鈕頂端列的螢幕擷取畫面](media/hpc-cache-upgrade-button.png)

在軟體升級期間，用戶端存取不會中斷，但快取效能會變慢。 規劃在非尖峰使用時間或預定的維護期間升級軟體。

軟體更新可能需要數小時的時間。 以較高的輸送量設定的快取所需的升級時間比具有較低尖峰輸送量值的快取記憶體長。

當軟體升級可供使用時，您有幾天的時間可以手動套用。 [結束日期] 會列在 [升級] 訊息中。 如果您在這段時間內未升級，Azure 會自動將更新套用至您的快取。 自動升級的時間無法設定。 如果您擔心會影響快取效能，則應該在期間到期之前自行升級軟體。

按一下 [**升級**] 按鈕以開始軟體更新。 快取狀態會變更為 [**升級**]，直到作業完成為止。

## <a name="delete-the-cache"></a>刪除快取

[**刪除**] 按鈕會損毀快取。 當您刪除快取時，其所有資源都會被終結，而不會再產生帳戶費用。

當您刪除快取時，儲存體目標不會受到影響。 您可以稍後再將它們新增至未來的快取，或分別將它們解除委任。

快取會在其最終關機過程中，自動將任何未儲存的資料排清到儲存體目標。

## <a name="cache-metrics-and-monitoring"></a>快取計量和監視

[總覽] 頁面會顯示一些基本快取統計資料的圖表-快取輸送量、每秒的作業數和延遲。

![三個折線圖的螢幕擷取畫面，其中顯示針對範例快取所述的統計資料](media/hpc-cache-overview-stats.png)

這些圖表是 Azure 內建監視和分析工具的一部分。 您可以從入口網站提要欄位中 [**監視**] 標題底下的頁面取得其他工具和警示。 若要深入瞭解，請[參閱 Azure 監視檔](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal)的入口網站一節。

## <a name="next-steps"></a>後續步驟

<!-- * Learn more about metrics and statistics for hpc cache -->
* 深入瞭解[Azure 計量和統計資料工具](../azure-monitor/index.yml)
* 取得[AZURE HPC 快取的協助](hpc-cache-support-ticket.md)
