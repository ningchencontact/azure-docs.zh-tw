---
title: 疑難排解 Azure 監視器計量圖表
description: 針對與建立、 自訂或解譯的度量圖表問題進行疑難排解
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 73ef5cc00b5154dbdbc92911d17740c7d13038ec
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341969"
---
# <a name="troubleshooting-metrics-charts"></a>疑難排解的計量圖表

如果您遇到問題，與建立、 自訂或解譯 Azure 計量瀏覽器中的圖表，請使用本文。 如果您不熟悉計量，了解[開始使用計量瀏覽器](metrics-getting-started.md)並[計量瀏覽器的進階功能](metrics-charts.md)。 您也可以查看[範例](metric-chart-samples.md)的已設定的度量圖表。

## <a name="cant-find-your-resource-to-select-it"></a>找不到您的資源，以選取它

在您按一下**選取資源**按鈕，但沒有看到您在資源選擇器對話方塊中的資源。

**解決方案：** 計量瀏覽器會要求您選取的訂用帳戶和資源群組之前列出可用的資源。 如果您沒有看到您的資源：

1. 請確定您已選取正確的訂用帳戶中**訂用帳戶**下拉式清單。 如果未列出您訂用帳戶，請按一下**目錄 + 訂用帳戶設定**並新增您的資源的訂用帳戶。

1. 請確定您已選取正確的資源群組。
    > [!WARNING]
    > 為了達到最佳效能，當您第一次開啟計量瀏覽器**資源群組**下拉式清單中沒有任何預先選取的資源群組。 您可以看到任何資源之前，您必須選取至少一個群組。

## <a name="chart-shows-no-data"></a>圖表會顯示任何資料

有時候圖表可能會顯示選取正確的資源和度量之後的任何資料。 此行為可能被因有幾個原因如下：

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>您的訂用帳戶未註冊 Microsoft.Insights 資源提供者

探索度量需要*Microsoft.Insights*資源提供者註冊您的訂用帳戶中。 在許多情況下，它會自動註冊 （也就是說，在您設定警示規則、 自訂診斷設定的任何資源，或設定自動調整規則）。 如果未註冊 Microsoft.Insights 資源提供者，您必須手動註冊該遵循的步驟中所述[Azure 資源提供者和類型](../../azure-resource-manager/resource-manager-supported-services.md)。

**解決方案：** 開啟**訂用帳戶**，**資源提供者**索引標籤，然後確認*Microsoft.Insights*註冊訂用帳戶。

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>您沒有足夠的存取權，您的資源

在 Azure 中，計量的存取權會受到[角色型存取控制 (RBAC)](../../role-based-access-control/overview.md)。 您必須是隸屬[監視讀取器](../../role-based-access-control/built-in-roles.md#monitoring-reader)，[監視參與者](../../role-based-access-control/built-in-roles.md#monitoring-contributor)，或[參與者](../../role-based-access-control/built-in-roles.md#contributor)探索任何資源的計量。

**解決方案：** 請確定您有足夠的權限探索計量的資源。

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>您的資源不在所選的時間範圍期間發出計量

某些資源不持續發出其度量。 例如，Azure 不會收集已停止的虛擬機器的計量。 其他資源可能會在某些條件發生時，才發出其計量。 例如，顯示交易的處理時間的度量需要至少一個交易。 如果沒有任何交易在所選的時間範圍中，圖表會自然地為空白。 此外，雖然大部分在 Azure 中的度量會收集每隔一分鐘，有一些收集的頻率較低。 請參閱計量的說明文件，以取得更多詳細您嘗試瀏覽的計量。

**解決方案：** 更多變更圖表的時間。 您可以啟動 「 過去 30 天內"使用較大的時間資料粒度 （或信賴憑證者的 「 自動時間資料粒度 」 選項）。

### <a name="you-picked-a-time-range-greater-than-30-days"></a>您選取超過 30 天的時間範圍

[在 Azure 中大部分的計量會儲存 93 天](data-platform-metrics.md#retention-of-metrics)。 不過，您可以只查詢不能超過 30 天的任何單一圖表上的資料。 這項限制不適用於[記錄檔型計量](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics)。

**解決方案：** 如果您看到空白的圖表，或您的圖表只顯示計量資料的一部分，請確認之間開始-和結束的日期時間選擇器中的差異不超過 30 天的間隔。

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>已鎖定 y 軸範圍之外的所有計量的值

藉由[鎖定圖表 y 軸的界限](metrics-charts.md#lock-boundaries-of-chart-y-axis)，您可以不小心讓不會顯示圖表線條的圖表顯示區域。 比方說，如果介於 0%和 50%，已被鎖定 y 軸，而且計量都有常數的值為 100%，該行是永遠會轉譯外部可見的區域中，使得圖表會顯示空白。

**解決方案：** 確認圖表的 y 軸的界限不鎖定計量值的範圍之外。 如果被鎖定 y 軸的界限，您可能想要暫時重設密碼，確認值未落在圖表的範圍之外的計量。 鎖定 y 軸範圍不建議使用自動資料粒度的圖表具有**總和**， **min**，並**max**彙總因為其值會變更藉由調整瀏覽器視窗的大小，或從一個螢幕解析度移至另一個資料粒度。 切換資料粒度可能會留下您圖表的空白的顯示區域。

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>您正在查看的客體 OS 計量，但未啟用 Azure 診斷擴充功能

集合**客體 OS**計量需要設定 Azure 診斷擴充功能，或讓它使用**診斷設定**您資源的面板。

**解決方案：** 如果已啟用 Azure 診斷擴充功能，但您還是沒看見您的計量，請依照下列步驟中所述[Azure 診斷擴充功能疑難排解指南](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal)。 另請參閱疑難排解步驟[無法挑選客體 OS 的命名空間和計量](metrics-troubleshoot.md#cannot-pick-guest-os-namespace-and-metrics)

## <a name="error-retrieving-data-message-on-dashboard"></a>在 儀表板上的 「 擷取資料時發生錯誤 」 訊息

儀表板建立與更新版本已被取代並從 Azure 中移除的度量時，可能會發生這個問題。 若要確認它是大小寫，請開啟**計量** 索引標籤，您的資源，並檢查計量選擇器中的可用計量。 如果計量未顯示，已從 Azure 移除度量。 通常，當計量即將淘汰，是更好新的計量，可提供類似的觀點來看，資源健康狀態。

**解決方案：** 挑選的替代度量圖表在儀表板上，以更新 [失敗] 圖格。 您可以[檢閱的 Azure 服務的可用計量清單](metrics-supported.md)。

## <a name="chart-shows-dashed-line"></a>圖表會顯示虛線

Azure 的計量圖表會使用虛線的線條樣式，表示有遺漏值 （也稱為 「 null 值 」） 之間兩個已知的時間粒紋為資料點。 例如，如果時間選取器中挑選 「 1 分鐘 」 時間資料粒度，但是 07:07:27，26 回報度量了 07:29 和 07:30 （請注意第二個和第三個資料點之間的分鐘間距），然後 07:27 和 07:29，虛線會連線並將連接一條實線，其他所有資料點。 降至零計量值使用時，虛線會卸除**計數**並**總和**彙總。 針對**avg**， **min**或是**最大**彙總，虛線會連接兩個最接近的已知的資料點。 此外，當圖表的最右側或最左邊端上遺漏的資料，虛線會展開以遺漏的資料點的方向。
  ![計量影像](./media/metrics-troubleshoot/missing-data-point-line-chart.png)

**解決方案：** 這是設計的行為。 它可用於識別遺漏的資料點。 折線圖是視覺化高密度計量的趨勢的絕佳選擇，但可能難以解譯疏鬆的值，計量，尤其是當 corelating 具有時間粒紋值很重要。 虛線輕鬆讀取這些圖表，但如果仍然不清楚您的圖表，請考慮使用不同的圖表類型中檢視您的計量。 比方說，在同一個度量零散的盒狀圖清楚顯示的每個時間粒紋僅的值時，視覺化點，並跳過資料點完全遺漏值時：![計量的映像](./media/metrics-troubleshoot/missing-data-point-scatter-chart.png)

   > [!NOTE]
   > 如果您仍然會偏好，而您的度量的折線圖，移動滑鼠移至圖表可能有助於透過反白顯示的滑鼠指標位置的資料點評估的時間粒度。

## <a name="chart-shows-unexpected-drop-in-values"></a>圖表會顯示未預期的拖放值

在許多情況下，認知下拉式清單中的度量值會是在圖表上顯示資料的誤解。 您可以藉由加總下降中給誤導或計算圖表時顯示的最新的分鐘數，因為最後一個的度量資料點尚未接收到或尚未處理的 Azure。 根據服務，延遲處理標準有可能在幾分鐘的時間範圍內。 顯示最近的時間範圍，1 或 5 分鐘細微度的圖表，過去幾分鐘內的值則卸除會變得更明顯：![計量的映像](./media/metrics-troubleshoot/drop-in-values.png)

**解決方案：** 這是設計的行為。 我們相信顯示資料，因為我們收到它是有幫助的資料時，即使*部分*或是*完整*。 這樣做可讓您進行更快很重要的結論，並且開始立即調查。 例如，計量會顯示失敗的數目，看到部分的值 X 會告訴您沒有至少 X 上的指定分鐘的失敗。 您可以開始調查問題，而不必等待查看此分鐘，這可能不是那麼重要發生的失敗的確切的計數。 此圖表會更新之後我們會收到整組資料，但是當時它可能也會顯示新的不完整的資料點，從較新的分鐘。

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>無法挑選客體 OS 的命名空間和計量

虛擬機器和虛擬機器擴展集有兩種計量：**虛擬機器主機**會由 Azure 裝載環境中，所收集的計量並**客體 OS**所收集的計量[監視代理程式](agents-overview.md)虛擬機器上執行。 您可以安裝監視代理程式藉由啟用[Azure 診斷擴充功能](diagnostics-extension-overview.md)。

根據預設，客體 OS 度量資訊會儲存在 Azure 儲存體帳戶，您從挑選**診斷設定** 索引標籤，您的資源。 如果不收集客體 OS 計量或計量瀏覽器無法存取它們，您只會看到**虛擬機器主機**度量命名空間：

![計量影像](./media/metrics-troubleshoot/cannot-pick-guest-os-namespace.png)

**解決方案：** 如果您沒有看到**客體 OS**命名空間和計量瀏覽器中的計量：

1. 確認[Azure 診斷擴充功能](diagnostics-extension-overview.md)已啟用並設定用來收集計量。
    > [!WARNING]
    > 您無法使用[Log Analytics 代理程式](agents-overview.md#log-analytics-agent)（也稱為 Microsoft Monitoring Agent 或 「 MMA 」） 傳送**客體 OS**到儲存體帳戶。

1. 確認防火牆不保護儲存體帳戶。

1. 使用[Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)驗證度量資訊會傳輸到儲存體帳戶。 如果不收集度量，請遵循[Azure 診斷擴充功能疑難排解指南](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal)。

## <a name="next-steps"></a>後續步驟

* [了解如何開始使用計量瀏覽器](metrics-getting-started.md)
* [深入了解計量瀏覽器進階功能](metrics-charts.md)
* [請參閱 Azure 服務的可用計量清單](metrics-supported.md)
* [請參閱設定圖表的範例](metric-chart-samples.md)
