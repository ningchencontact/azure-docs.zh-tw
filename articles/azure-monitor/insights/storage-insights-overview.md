---
title: 使用適用于儲存體的 Azure 監視器監視 Azure 儲存體服務（預覽） |Microsoft Docs
description: 本文說明儲存體功能的 Azure 監視器，可讓儲存體管理員快速瞭解其 Azure 儲存體帳戶的效能和使用問題。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/15/2019
ms.author: magoedte
ms.openlocfilehash: 17135f896140a843dd95c8d9624e9faf1d7194c3
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996253"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage-preview"></a>使用適用于儲存體的 Azure 監視器監視儲存體服務（預覽）

適用于儲存體的 Azure 監視器（預覽）藉由提供 Azure 儲存體服務效能、容量和可用性的統一觀點，全面監視您的 Azure 儲存體帳戶。 您可以透過兩種方式來觀察儲存體容量和效能、直接從儲存體帳戶或從 Azure 監視器查看，查看不同的儲存體帳戶群組。 

這篇文章將協助您瞭解儲存體（預覽） Azure 監視器的體驗，以提供大規模的儲存體帳戶健康情況和效能的可操作知識，並能專注于熱點並診斷延遲、節流、和可用性問題。

## <a name="introduction-to-azure-monitor-for-storage-preview"></a>儲存體 Azure 監視器簡介（預覽）

在深入探討經驗之前，您應該先瞭解它呈現和視覺化資訊的方式。 無論您是直接從儲存體帳戶或從 Azure 監視器選取儲存體功能，Azure 監視器的儲存體都會提供一致的體驗。 

結合的 it 提供：

* 根據儲存體服務或 API 作業的健康情況，**大規模**顯示其可用性的快照集視圖、顯示儲存體服務接收的要求總數，以及顯示平均時間的延遲儲存體服務或 API 作業類型是用來處理要求。 您也可以依 blob、檔案、資料表和佇列來查看容量。

* 向下切入特定儲存體帳戶的**分析**，以協助診斷問題，或依類別（可用性、效能、失敗和容量）執行詳細的分析。 選取其中任何一個選項可提供計量的深入觀點。  

* 可**自訂**，您可以在其中變更您想要查看的計量、修改或設定符合限制的臨界值，以及另存為您自己的活頁簿。 活頁簿中的圖表可以釘選到 Azure 儀表板。  

這項功能不會要求您啟用或設定任何專案，預設會收集儲存體帳戶的儲存體計量。 如果您不熟悉 Azure 儲存體上可用的計量，請參閱[Azure 儲存體計量](../../storage/common/storage-metrics-in-azure-monitor.md)來查看 Azure 儲存體計量中的描述和定義。

>[!NOTE]
>存取這項功能不需要付費，您只需支付所設定或啟用的 Azure 監視器基本功能的費用，如[Azure 監視器定價詳細資料](https://azure.microsoft.com/pricing/details/monitor/)頁面所述。

>[!NOTE]
>儲存體的 Azure 監視器不支援[一般用途 v1 帳戶](../../storage/common/storage-account-overview.md#general-purpose-v1-accounts)。
>

## <a name="view-from-azure-monitor"></a>從 Azure 監視器查看

從 Azure 監視器，您可以從訂用帳戶中的多個儲存體帳戶查看交易、延遲和容量詳細資料，並協助識別效能、容量問題和失敗。

若要查看您所有訂用帳戶的儲存體帳戶使用率和可用性，請執行下列步驟。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 從 Azure 入口網站的左側窗格中選取 [**監視**]，然後在 [**深入**解析] 區段下，選取 [**儲存體帳戶（預覽）** ]。

    ![多個儲存體帳戶的觀點](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>總覽活頁簿

在所選訂用帳戶的**總覽**活頁簿中，資料表會顯示在訂用帳戶內分組的最多10個儲存體帳戶的互動式儲存體計量和服務可用性狀態。 您可以根據您從下列下拉式清單中選取的選項來篩選結果：

* 訂用帳戶-只會列出具有儲存體帳戶**的訂閱。**  

* **儲存體帳戶**-預設會預先選取10個儲存體帳戶。 如果您在 [範圍] 選取器中選取 [所有或多個儲存體帳戶]，則會傳回最多200個儲存體帳戶。 例如，如果您所選取的三個訂用帳戶共有573個儲存體帳戶，則只會顯示200個帳戶。 

* **時間範圍**：根據預設，會依據對應的選取專案顯示最後4小時的資訊。

下拉式清單底下的 [計數器] 磚會匯總訂用帳戶中的儲存體帳戶總數，並反映選取的總計數。 活頁簿中的資料行有條件式色彩編碼或熱度圖，可報告交易計量或錯誤。 最深的色彩具有最高的值，而較淡的色彩則是根據最低值。 針對以錯誤為基礎的資料行，此值為紅色，而針對以度量為基礎的資料行，其值為藍色。

在 [資料行**可用性**]、[ **E2E 延遲**]、[**伺服器延遲**] 和 [**交易錯誤類型/錯誤**] 中選取一個值，會將您導向符合針對該所選取之資料行的特定儲存體計量類型而量身打造的報表。儲存體帳戶。 如需每個類別之活頁簿的詳細資訊，請參閱下面的[詳細儲存活頁簿](#detailed-storage-workbooks)一節。 

>[!NOTE]
>如需報告中可顯示哪些錯誤的詳細資訊，請參閱[回應類型架構](../../storage/common/storage-metrics-in-azure-monitor.md#metrics-dimensions)和尋找回應類型，例如**ServerOtherError**、 **ClientOtherError**、 **ClientThrottlingError**。 視選取的儲存體帳戶而定，如果報告的錯誤類型超過三個，所有其他錯誤都會在 [**其他**] 類別下呈現。

預設**可用性**閾值為：

* 警告-99%
* 重大-90%

若要根據觀察或需求的結果來設定可用性閾值，請參閱[修改可用性閾值](#modify-the-availability-threshold)。 

### <a name="capacity-workbook"></a>容量活頁簿

選取頁面頂端的 [**容量**]，[**容量**] 活頁簿隨即開啟。 它會顯示帳戶中所使用的總儲存體數量，以及帳戶中每個資料服務所使用的容量，以協助識別使用中的儲存空間。

![多個儲存體帳戶容量活頁簿](./media/storage-insights-overview/storage-account-capacity-02.png) 

活頁簿中的資料行有條件式色彩編碼或熱度圖，會以藍色值來報告容量計量。 最深的色彩具有最高的值，而較淡的色彩則是根據最低值。

當您在活頁簿中的任何一個資料行底下選取一個值時，您會向下切入至儲存體帳戶的**容量**活頁簿。 有關向下切入報表的進一步詳細資料，請參閱下面的[詳細儲存體活頁簿](#detailed-storage-workbooks)一節。 

## <a name="view-from-a-storage-account"></a>從儲存體帳戶查看

若要直接從儲存體帳戶存取適用於 VM 的 Azure 監視器：

1. 在 Azure 入口網站中，選取 儲存體帳戶。

2. 從清單中選擇儲存體帳戶。 在 [監視] 區段中，選擇 [Insights （預覽）]。

    ![選取的儲存體帳戶總覽頁面](./media/storage-insights-overview/storage-account-direct-overview-01.png)

在儲存體帳戶的**總覽**活頁簿中，它會顯示數個儲存體效能計量，可協助您快速評估：

* 儲存體服務的健康情況，以立即查看控制項外部的問題是否會影響其部署目的地區域中的儲存體服務，這會在 [**摘要**] 資料行底下指出。

* 互動式效能圖表，顯示與儲存體容量、可用性、交易和延遲相關的最基本詳細資料。  

* 顯示服務可用性、儲存體服務的交易總數、E2E 延遲和伺服器延遲的計量和狀態磚。

針對 [**失敗**]、[**效能**]、[**可用性**] 和 [**容量**] 選取任何一個按鈕，會開啟個別的活頁簿。 

![選取的儲存體帳戶總覽頁面](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>詳細的儲存活頁簿

您是否從多個儲存體帳戶**總覽**活頁簿中選取資料行**可用性**、 **E2E 延遲**、**伺服器延遲**和**交易錯誤類型/錯誤**中的值，或選取任何一個按鈕來自特定儲存體帳戶之**總覽**活頁簿的**失敗**、**效能**、**可用性**和**容量**，各自提供一組專為該類別量身打造的互動式儲存體相關資訊。  

* **可用性**會開啟**可用性**活頁簿。 它會顯示目前的 Azure 儲存體服務健全狀況狀態，這是一個資料表，其中顯示由儲存體帳戶中定義的資料服務所分類之每個物件的可用健全狀況狀態，並具有代表所選時間範圍的趨勢線，以及的可用性趨勢圖表帳戶中的每個資料服務。  

    ![可用性報告範例](./media/storage-insights-overview/storage-account-availability-01.png)

* **E2E 延遲**和**伺服器延遲**會開啟 [**效能**] 活頁簿。 其中包含 [匯總狀態] 磚，其中顯示 E2E 延遲和伺服器延遲、E2E 與伺服器延遲的效能圖表，以及根據儲存體帳戶中定義的資料服務所分類的 API，細分成功呼叫的延遲的資料表。

    ![效能報告範例](./media/storage-insights-overview/storage-account-performance-01.png)

* 選取方格中列出的任何錯誤類別，即會開啟 [**失敗**] 活頁簿。 此報表會顯示所有其他用戶端錯誤的計量磚，但描述了其中一個和一個成功的要求、用戶端節流錯誤、ClientOtherError 屬性特定之交易**回應類型**維度計量的效能圖表。和兩個數據表-依**API 名稱**和交易的交易（**依回應類型**）。

   ![失敗報告範例](./media/storage-insights-overview/storage-account-failures-01.png)

* **容量**會開啟**容量**活頁簿。 它會顯示磚和圖表中帳戶內每個儲存體資料物件所使用的儲存體總量，以及帳戶中儲存多少個數據物件。  

    ![選取的儲存體帳戶容量頁面](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>釘選和匯出

您可以選取區段右上角的圖釘圖示，將任何一個度量區段釘選到 Azure 儀表板。

![度量區段釘選至儀表板範例](./media/storage-insights-overview/workbook-pin-example.png)

多訂用帳戶和儲存體帳戶的**總覽**或**容量**活頁簿支援以 Excel 格式匯出結果，方法是選取圖釘圖示右邊的向下箭號圖示。

![匯出活頁簿方格結果範例](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage-preview"></a>自訂儲存體的 Azure 監視器（預覽）

本節重點說明編輯活頁簿以自訂以支援資料分析需求的常見案例：

* 將活頁簿的範圍設為一律選取特定訂用帳戶或儲存體帳戶
* 變更方格中的計量
* 變更可用性閾值
* 變更色彩呈現

這些自訂專案會儲存至自訂活頁簿，以避免覆寫已發行之活頁簿中的預設設定。 活頁簿會儲存在資源群組中，不論是在您私人的**我的報表**區段中，或是在可存取資源群組的每個人都可存取的 [**共用報表**] 區段中。 儲存自訂活頁簿之後，您必須移至活頁簿庫來啟動它。

![從命令列啟動活頁簿圖庫](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>指定訂用帳戶或儲存體帳戶

您可以在每次執行時，將多個訂用帳戶和儲存體帳戶**總覽**或**容量**活頁簿設定為特定訂用帳戶或儲存體帳戶的範圍，然後執行下列步驟。

1. 從入口網站選取 [**監視**]，然後從左側窗格中選取 [**儲存體帳戶（預覽）** ]。

2. 在 [**總覽**] 活頁簿上，從命令列選取 [**編輯**]。

3. 從 [訂用帳戶] 下拉式**清單中選取**您想要它預設為的一個或多個訂用帳戶。 請記住，活頁簿最多支援選取10個訂用帳戶。  

4. 從 [**儲存體帳戶**] 下拉式清單中選取您想要其預設的一或多個帳戶。 請記住，活頁簿支援總共選取200個儲存體帳戶。 

5. 從命令列選取 [**另存**新檔]，以使用您的自訂儲存活頁簿的複本，然後按一下 [**完成編輯**] 回到閱讀模式。  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>修改活頁簿中的計量和色彩

預先建立的活頁簿包含計量資料，而且您可以修改或移除任何一個視覺效果，並自訂您小組的特定需求。 

在我們的範例中，我們會使用多訂用帳戶和儲存體帳戶容量活頁簿，以示範如何：

* 移除度量
* 變更色彩呈現

您可以對任何一個預先建立的**失敗**、**效能**、**可用性**和**容量**活頁簿執行相同的變更。

1. 從入口網站選取 [**監視**]，然後從左側窗格中選取 [**儲存體帳戶（預覽）** ]。

2. 選取 [**容量**] 以切換至 [容量] 活頁簿，並從命令列中選取命令列中的 [**編輯**]。

    ![選取 [編輯] 以修改活頁簿](./media/storage-insights-overview/workbook-edit-workbook.png)

3. 在 [計量] 區段旁，選取 [**編輯**]。 

    ![選取 [編輯] 以修改容量活頁簿計量](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. 我們即將移除 [使用的**帳戶容量時間軸**] 資料行，因此請選取 [計量] 方格中的 [資料**行設定**]。

    ![編輯資料行設定](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. 在 [**編輯資料行設定**] 窗格中，選取 [microsoft] 資料**行**區段底下的 [ **Storageaccounts-容量-UsedCapacity 時間軸 $ |]。帳戶已使用容量時間軸 $** ，並在下拉式清單資料**行**轉譯器底下選取 [**隱藏**]。 

6. 選取 [**儲存並關閉**] 以認可變更。

現在，讓我們將報表中容量計量的色彩主題變更為使用綠色，而不是藍色。

1. 選取 [計量] 方格中的 [資料**行設定**]。

2. 在 [**編輯資料行設定**] 窗格中，選取 [ **microsoft. storage/storageaccounts-容量-UsedCapacity $ | microsoft. storage/storageaccounts/blobservices-容量-BlobCapacity $ | microsoft. storage/] 資料行區段底下的storageaccounts/fileservices-容量-FileCapacity $ | microsoft. storage/storageaccounts/queueservices-容量-QueueCapacity $ | microsoft. storage/storageaccounts/tableservices-容量-TableCapacity $** 。 在下拉式清單的 [**色板**] 底下，選取 [**綠色**]。

3. 選取 [**儲存並關閉**] 以認可變更。

4. 從命令列選取 [**另存**新檔]，以使用您的自訂儲存活頁簿的複本，然後按一下 [**完成編輯**] 回到閱讀模式。  

### <a name="modify-the-availability-threshold"></a>修改可用性閾值

在此範例中，我們會使用儲存體帳戶容量活頁簿，並示範如何修改可用性閾值。 根據預設，磚和方格報告百分比的可用性設定為最低閾值為90，閾值上限為99。 我們將 [**可用性（依據 API 名稱**）] 方格的 [**可用性%** ] 的最小臨界值變更為 85%，這表示當閾值低於 85% 時，健全狀況狀態會變更為 [重大]。 

1. 從入口網站中選取 [**儲存體帳戶**]，然後從清單中選取儲存體帳戶。

2. 從左側窗格中選取 [ **Insights （預覽）** ]。

3. 在活頁簿中，選取 [**可用性**] 以切換至可用性活頁簿，然後從命令列選取 [**編輯**]。 

4. 向下流覽至頁面底部，然後在左側 [**可用性依據 API** ] 方格旁，選取 [**編輯**]。

    ![依 API 名稱方格設定編輯可用性](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. 選取 資料**行設定**，然後在 **編輯資料行設定** 窗格的 資料**行** 區段下選取 **可用性（%）（閾值 + 已格式化）** 。

6. 將 [**重大**健全狀況狀態] 的值從**90**變更為**85** ，然後按一下 [**儲存並關閉**]。

    ![修改重大狀態的可用性臨界值](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. 從命令列選取 [**另存**新檔]，以使用您的自訂儲存活頁簿的複本，然後按一下 [**完成編輯**] 回到閱讀模式。

## <a name="troubleshooting"></a>疑難排解

本節將協助您進行診斷，並疑難排解使用 Azure 監視器儲存（預覽）時可能會遇到的一些常見問題。 請使用下列清單，找到與您的特定問題相關的資訊。

### <a name="resolving-performance-capacity-or-availability-issues"></a>解決效能、容量或可用性問題

若要協助針對您使用 Azure 監視器 for Storage （預覽）識別的任何儲存體相關問題進行疑難排解，請參閱 Azure 儲存體[疑難排解指引](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)。  

## <a name="next-steps"></a>後續步驟

* 設定計量[警示](../platform/alerts-metric.md)和[服務健康情況通知](../../service-health/alerts-activity-log-service-notifications.md)，以設定自動化警示以協助偵測問題。

* 瞭解活頁簿設計來支援的案例、如何撰寫新的和自訂現有的報表，以及如何[使用 Azure 監視器活頁簿建立互動式報表](../app/usage-workbooks.md)。

* 如需使用儲存體分析和其他工具來識別、診斷及疑難排解 Azure 儲存體相關問題的深入指南，請參閱 [監視、診斷及疑難排解 Microsoft Azure 儲存體](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)。
