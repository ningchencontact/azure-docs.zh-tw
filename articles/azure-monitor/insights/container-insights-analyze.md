---
title: 使用適用於容器的 Azure 監視器來監視 AKS 叢集效能 | Microsoft Docs
description: 本文說明如何使用適用於容器的 Azure 監視器，來檢視和分析效能與記錄資料。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/22/2019
ms.author: magoedte
ms.openlocfilehash: 154848c33960cb78b10c58e7a39ddec669d4fae0
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69872980"
---
# <a name="understand-aks-cluster-performance-with-azure-monitor-for-containers"></a>使用適用於容器的 Azure 監視器來了解 AKS 叢集效能
使用容器的 Azure 監視器, 您可以使用效能圖表和健全狀況狀態, 從兩個角度監視 Azure Kubernetes Service (AKS) 叢集的工作負載。 您可以直接從 AKS 叢集進行監視, 也可以從 Azure 監視器監視訂用帳戶中的所有 AKS 叢集。 當您監視特定的 AKS 叢集時, 也可以查看 Azure 容器實例。

本文可協助您瞭解這兩個觀點, 以及 Azure 監視器如何協助您快速地評估、調查和解決偵測到的問題。

如需如何啟用容器 Azure 監視器的詳細資訊, 請參閱將[容器上架 Azure 監視器](container-insights-onboard.md)。

Azure 監視器提供多叢集的視圖, 顯示在訂用帳戶的資源群組中, 所有執行 Linux 和 Windows Server 2019 之受監視 AKS 叢集的健全狀況狀態。 它會顯示已探索到未受解決方案監視的 AKS 叢集。 您可以立即瞭解叢集健康情況, 您可以從這裡向下切入到節點和控制器效能頁面, 或流覽以查看叢集的效能圖表。 針對探索到且識別為未受監視的 AKS 叢集, 您可以隨時為其啟用監視。 

與 Linux 叢集相較之下, 針對容器的 Azure 監視器監視 Windows Server 叢集的主要差異如下:

- 記憶體 RSS 計量不適用於 Windows 節點和容器。
- 磁片儲存體容量資訊不適用於 Windows 節點。
- Windows 容器記錄除外, 提供即時記錄支援。
- 只有 pod 環境會受到監視, 而不是 Docker 環境。
- 在預覽版本中, 最多可支援30個 Windows Server 容器。 這項限制不適用於 Linux 容器。 

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com)。 

## <a name="multi-cluster-view-from-azure-monitor"></a>Azure 監視器中的多叢集檢視

若要查看已部署的所有 AKS 叢集的健全狀況狀態, 請從 [Azure 入口網站] 的左窗格中選取 [**監視**]。 在 [Insights] 區段下方，選取 [容器]。 

![Azure 監視器多叢集儀表板範例](./media/container-insights-analyze/azmon-containers-multiview.png)

在 [**受監視**的叢集] 索引標籤上, 您會瞭解下列各項:

- 有多少叢集處於「重大」或「狀況不良」狀態, 以及有多少叢集狀況良好或未回報 (稱為「不明」狀態)。
- 所有[Azure Kubernetes Engine (AKS-engine)](https://github.com/Azure/aks-engine)部署是否狀況良好。
- 每個叢集部署的節點和使用者和系統 pod 數目。
- 有多少可用的磁碟空間, 以及是否有容量問題。

內含的健全狀態如下： 

* **狀況良好**:未偵測到 VM 的任何問題, 而且它會視需要運作。 
* **重大**：偵測到一或多個重大問題, 必須加以解決才能如預期般還原正常運作狀態。
* **警告**：偵測到一個或多個問題必須解決, 或健康情況可能變得很重要。
* **未知**：如果服務無法建立與節點或 pod 的連線, 狀態就會變更為未知狀態。
* **找不到**:已刪除包含此解決方案之工作區的工作區、資源群組或訂用帳戶。
* **未經授權**:使用者沒有讀取工作區中資料的必要許可權。
* **錯誤**：嘗試從工作區讀取資料時發生錯誤。
* 設定**錯誤**:未在指定的工作區中正確設定容器的 Azure 監視器。
* **沒有資料**：過去30分鐘內未向工作區回報資料。

健全狀況狀態會將整體叢集狀態計算為三個狀態的*最差*, 但有一個例外。 如果有三種狀態不明, 整體叢集狀態會顯示 [**不明**]。 

下表提供計算的細目, 可控制多叢集視圖上受監視叢集的健全狀況狀態。

| |狀態 |可用性 |  
|-------|-------|-----------------|  
|**使用者 pod**| | |  
| |狀況良好 |100% |  
| |警告 |90 - 99% |  
| |重大 |<90% |  
| |未知 |如果未在過去 30 分鐘內報告 |  
|**系統 pod**| | |  
| |狀況良好 |100% |
| |警告 |N/A |
| |重大 |<100% |
| |未知 |如果未在過去 30 分鐘內報告 |
|**Node** | | |
| |狀況良好 |>85% |
| |警告 |60 - 84% |
| |重大 |<60% |
| |未知 |如果未在過去 30 分鐘內報告 |

從叢集清單中, 您可以選取叢集的名稱來向下切入到 [叢集] 頁面。 然後, 在該特定叢集的 [**節點**] 資料行中選取節點的匯總, 以移至 [**節點**] 效能頁面。 或者, 您可以選取 [**使用者**pod] 或 [**系統**pod] 資料行的匯總, 以向下切入到 [**控制器**] 效能頁面。  

## <a name="view-performance-directly-from-an-aks-cluster"></a>直接從 AKS 叢集檢視效能

從左窗格中選取 [**見解**], 即可直接從 AKS 叢集存取容器的 Azure 監視器。 AKS 叢集的相關資訊會組織成四個觀點:

- 叢集
- 節點 
- 控制器 
- 容器

當您選取 [ **Insights**  > 叢集] 時, 預設頁面就會開啟。 四條線效能圖表會顯示您叢集的關鍵效能計量。 

![[叢集] 索引標籤上的範例效能圖表](./media/container-insights-analyze/containers-cluster-perfview.png)

效能圖表會顯示四個效能計量:

- **節點 CPU 使用率&nbsp; %** :整個叢集中 CPU 使用率的彙總檢視方塊。 若要篩選時間範圍的結果, 請在圖表上方的百分位數選取器中選取 [ **Avg**]、[ **Min**]、[第**50**個]、[ **95**] 或 [**最大值**]。 您可以個別或合併使用篩選。 
- **節點記憶體使用率&nbsp;%** ：整個叢集中記憶體使用率的彙總檢視方塊。 若要篩選時間範圍的結果, 請在圖表上方的百分位數選取器中選取 [ **Avg**]、[ **Min**]、[第**50**個]、[ **95**] 或 [**最大值**]。 您可以個別或合併使用篩選。 
- **節點計數**：Kubernetes 中的節點計數和狀態。 代表的叢集節點狀態包括 [總計]、[就緒] 和 [未就緒]。 可以在圖表上方的選取器中個別篩選或結合。 
- 使用中的**pod 計數**:Kubernetes 中的 Pod 計數和狀態。 所代表 pod 的狀態包括總計、暫止、執行中、不明、成功或失敗。 可以在圖表上方的選取器中個別篩選或結合。 

使用向左鍵和向右鍵可迴圈顯示圖表上的每個資料點。 使用向上箭號和向下鍵來迴圈顯示百分位數線。 選取任何一個圖表右上角的釘選圖示, 將選取的圖表固定至您所查看的最後一個 Azure 儀表板。 您可以從儀表板調整圖表的大小和位置。 從儀表板選取圖表會將您重新導向至容器的 Azure 監視器, 並載入正確的範圍和視圖。

容器的 Azure 監視器也支援 Azure 監視器[計量瀏覽器](../platform/metrics-getting-started.md), 您可以在其中建立自己的繪圖圖表、相互關聯和調查趨勢, 以及釘選到儀表板。 在計量瀏覽器中, 您也可以使用您所設定的準則, 將您的計量視覺化為以[度量為基礎的警示規則](../platform/alerts-metric.md)的基礎。 

## <a name="view-container-metrics-in-metrics-explorer"></a>在計量瀏覽器中查看容器計量

在計量瀏覽器中, 您可以從容器的 Azure 監視器中, 查看匯總的節點和 pod 使用率計量。 下表摘要說明的詳細資料, 可協助您瞭解如何使用計量圖表來視覺化容器計量。

|命名空間 | 度量 |
|----------|--------|
| insights.container/nodes | |
| | cpuUsageMillicores |
| | cpuUsagePercentage |
| | memoryRssBytes |
| | memoryRssPercentage |
| | memoryWorkingSetBytes |
| | memoryWorkingSetPercentage |
| | nodesCount |
| insights.container/pods | |
| | PodCount |

您可以依維度[分割](../platform/metrics-charts.md#apply-splitting-to-a-chart)計量, 並以視覺化方式呈現其不同區段彼此之間的比較。 若是節點, 您可以依*主機*維度分割圖表。 您可以從 pod 將它分割成下列維度:

* 控制器
* Kubernetes 命名空間
* 節點
* 階段

## <a name="analyze-nodes-controllers-and-container-health"></a>分析節點、控制器和容器健全狀況

當您切換至 [**節點**]、[**控制器**] 和 [**容器**] 索引標籤時, [屬性] 窗格會自動顯示在頁面的右側。 它會顯示所選取專案的屬性, 其中包含您定義來組織 Kubernetes 物件的標籤。 選取 [Linux] 節點時, [**本機磁片容量**] 區段也會顯示可用的磁碟空間, 以及每個呈現給節點的磁片所使用的百分比。 選取窗格 **>>** 中的連結, 即可查看或隱藏窗格。

![範例 Kubernetes 透視圖屬性窗格](./media/container-insights-analyze/perspectives-preview-pane-01.png)

當您展開階層中的物件時，屬性窗格會根據所選的物件更新。 在窗格中, 您也可以選取窗格頂端的 [ **View Kubernetes 事件記錄**檔] 連結, 以使用預先定義的記錄搜尋來查看 Kubernetes 事件。 如需如何查看 Kubernetes 記錄資料的詳細資訊, 請參閱[搜尋記錄來分析資料](container-insights-log-search.md)。 當您查看叢集資源時, 您可以即時看到容器記錄和事件。 如需這項功能的詳細資訊, 以及授與和控制存取權所需的設定, 請參閱[使用適用于容器的 Azure 監視器即時查看記錄](container-insights-live-logs.md)。 

使用頁面頂端的 [ **+ 新增篩選**] 選項, 依**服務**、**節點**、**命名空間**或**節點集**區篩選視圖的結果。 選取篩選範圍之後, 請選取 [**選取值**] 欄位中所顯示的其中一個值。 設定篩選之後, 它會全域套用, 同時觀察 AKS 叢集的任何觀點。 此公式僅支援等號。 您可以在第一個篩選器上方新增其他篩選器，進一步縮小您的結果。 例如, 如果您依**節點**指定篩選器, 則只能選取第二個篩選器的**服務**或**命名空間**。

![使用篩選器來縮小結果的範例](./media/container-insights-analyze/add-filter-option-01.png)

當您選取另一個索引標籤時, 會繼續套用其中的篩選。 當您選取指定篩選器旁的**x**符號之後, 就會刪除它。 

切換至 [**節點**] 索引標籤, 資料列階層會遵循 Kubernetes 物件模型, 其開頭為叢集中的節點。 展開節點以查看節點上執行的一或多個 pod。 如果有一個以上的容器分組至 pod, 它們會顯示為階層中的最後一個資料列。 如果主機有處理器或記憶體壓力, 您也可以在主機上查看有多少非 pod 相關的工作負載正在執行。

![效能視圖中 Kubernetes 節點階層的範例](./media/container-insights-analyze/containers-nodes-view.png)

執行 Windows Server 2019 OS 的 windows Server 容器會顯示在清單中所有以 Linux 為基礎的節點之後。 當您展開 Windows Server 節點時, 可以查看在節點上執行的一或多個 pod 和容器。 選取節點之後, [屬性] 窗格會顯示版本資訊。 因為 Windows Server 節點未安裝代理程式, 所以排除代理程式資訊。 

![列出 Windows Server 節點的範例節點階層](./media/container-insights-analyze/nodes-view-windows.png) 

執行 Linux OS 的 Azure 容器實例虛擬節點會顯示在清單中的最後一個 AKS 叢集節點之後。 當您展開容器實例虛擬節點時, 您可以查看一個或多個容器實例 pod, 以及在節點上執行的容器。 不會為節點收集和報告計量, 僅適用于 pod。

![列出容器執行個體的範例節點階層](./media/container-insights-analyze/nodes-view-aci.png)

從展開的節點, 您可以向下切入節點上執行的 pod 或容器, 以查看針對該控制器篩選的效能資料。 選取特定節點的 [**控制器**] 資料行底下的值。
 
![效能檢視中從節點到控制器的向下切入範例](./media/container-insights-analyze/drill-down-node-controller.png)

選取頁面頂端的 [控制器] 或 [容器], 以查看這些物件的狀態和資源使用率。 若要查看記憶體使用率, 請在 [**度量**] 下拉式清單中選取 [**記憶體 RSS** ] 或 [**記憶體工作集**]。 只有 Kubernetes 1.8 版和更新版本才支援**記憶體 RSS**。 否則，您會以 *NaN&nbsp;%* 的形式檢視 **Min&nbsp;%** 的值，這是代表未定義或無法顯示之值的數值資料類型值。

**記憶體工作集**會顯示內含的記憶體和虛擬記憶體 (快取), 以及應用程式所使用的總數。 **記憶體 RSS**只會顯示主儲存體, 也就是常駐記憶體。 此標準會顯示可用記憶體的實際容量。

![容器節點效能檢視](./media/container-insights-analyze/containers-node-metric-dropdown.png)

根據預設, 效能資料是以過去六個小時為基礎, 但是您可以使用左上方的 [ **TimeRange** ] 選項來變更視窗。 您也可以在 [百分位數] 選取器中選取 [**最小值**]、[**平均**]、[第**50**個]、[第**95**] 和 [**最大值**], 以篩選 

![資料篩選的百分位數選取範圍](./media/container-insights-analyze/containers-metric-percentile-filter.png)

當您將滑鼠停留在 [**趨勢**] 資料行底下的橫條圖上時, 每個橫條都會顯示 CPU 或記憶體使用量, 視選取的度量而定, 在15分鐘的範例期間內。 當您透過鍵盤選取趨勢圖表之後, 請使用 Alt + Page up 鍵或 Alt + Page down 鍵來個別迴圈處理每個橫條。 如果您將滑鼠停留在列上, 您會得到相同的詳細資料。

![趨勢橫條圖滑鼠暫留-over 範例](./media/container-insights-analyze/containers-metric-trend-bar-01.png) 

在下一個範例中, 針對清單中的第一個節點*aks-nodepool1-* ,**容器**的值為9。 此值是已部署容器總數的匯總套件。

![每個節點的容器匯總套件範例](./media/container-insights-analyze/containers-nodes-containerstotal.png)

這項資訊可協助您快速識別叢集中節點之間是否有適當的容器平衡。 

下表說明當您看到 [**節點**] 索引標籤時所顯示的資訊。

| 「資料行」 | 描述 | 
|--------|-------------|
| Name | 主機的名稱。 |
| 狀態 | 節點狀態的 Kubernetes 檢視。 |
| 最&nbsp;小%、&nbsp;平均%、&nbsp;第 50%&nbsp;、第 90&nbsp;%、第 95%、最大值&nbsp;%  | 根據選取期間內百分位數的平均節點百分比。 |
| Min、Avg、第50、90、95、Max | 根據所選期間內百分位數的平均節點實際值。 平均值是從為節點設定的 CPU/記憶體限制來測量。 針對 pod 和容器, 這是主機所報告的平均值。 |
| 容器 | 容器的數目。 |
| 執行時間 | 呈現自節點啟動或重新啟動以來經過的時間。 |
| 控制器 | 僅適用於容器與 Pod。 它會顯示其所在的控制器。 並非所有 Pod 都位在控制器之中，因此有些可能會顯示 **N/A**。 | 
| 趨勢最&nbsp;小值%&nbsp;、平均%&nbsp;、第 50&nbsp;%、第&nbsp;90%、第 95%、最大值&nbsp;% | 長條圖趨勢代表控制器的平均百分位數計量百分比。 |

在選取器中，選取 [控制器]。

![選取控制器視圖](./media/container-insights-analyze/containers-controllers-tab.png)

在這裡, 您可以查看控制器的效能健全狀況, 以及未連線到控制器的容器實例虛擬節點控制器或虛擬節點 pod。

![\<> 控制器的名稱 效能視圖](./media/container-insights-analyze/containers-controllers-view.png)

資料列階層會以控制器開始。 當您展開控制器時, 您會看到一或多個 pod。 展開 Pod，最後一個資料夾會顯示分組至 Pod 的容器。 從展開的控制器中, 您可以向下切入到它正在執行的節點, 以查看針對該節點篩選的效能資料。 未連線到控制器的容器實例 pod 會列在清單中的最後一個。

![列出容器執行個體 Pod 的範例控制器階層](./media/container-insights-analyze/controllers-view-aci.png)

在特定控制器的 [**節點**] 資料行底下, 選取 [值]。

![效能檢視中從節點到控制器的向下切入範例](./media/container-insights-analyze/drill-down-controller-node.png)

下表說明您在查看控制器時所顯示的資訊。

| 「資料行」 | 描述 | 
|--------|-------------|
| Name | 控制器的名稱。|
| 狀態 | 容器在完成執行後的匯總狀態, 其狀態如 *[確定]、[* 已*終止*]、[*失敗*]、[*已停止*] 或 [已*暫停*]。 如果容器正在執行, 但狀態未正確顯示或未由代理程式挑選, 而且尚未回應30分鐘以上, 則狀態為*不明*。 下表提供狀態圖示的其他詳細資料。|
| 最&nbsp;小%、&nbsp;平均%、&nbsp;第 50%&nbsp;、第 90&nbsp;%、第 95%、最大值&nbsp;%| 針對所選取計量和百分位數之每個實體的平均百分比彙總平均值。 |
| Min、Avg、第50、90、95、Max  | 容器針對所選取百分位數的平均 CPU millicore 或記憶體效能彙總。 平均值是從為 Pod 設定的 CPU/記憶體限制測量所得。 |
| 容器 | 該控制器或 Pod 的容器總數。 |
| 重新啟動 | 容器重新啟動計數的彙總。 |
| 執行時間 | 代表自容器啟動以來經過的時間。 |
| 節點 | 僅適用於容器與 Pod。 它會顯示其所在的控制器。 | 
| 趨勢最&nbsp;小值%&nbsp;、平均%&nbsp;、第 50&nbsp;%、第&nbsp;90%、第 95%、最大值&nbsp;% | 長條圖趨勢代表控制器的平均百分位數計量。 |

[狀態] 欄位中的圖示會指出容器的線上狀態。
 
| 圖示 | 狀態 | 
|--------|-------------|
| ![準備好執行的狀態圖示](./media/container-insights-analyze/containers-ready-icon.png) | 執行中 (就緒)|
| ![等待或暫停狀態圖示](./media/container-insights-analyze/containers-waiting-icon.png) | 等候中或已暫停|
| ![上次回報的執行狀態圖示](./media/container-insights-analyze/containers-grey-icon.png) | 上次回報執行中, 但未回應30分鐘以上|
| ![成功狀態圖示](./media/container-insights-analyze/containers-green-icon.png) | 已成功停止或無法停止|

狀態圖示會根據 Pod 所提供的功能顯示計數。 它會顯示最差的兩個狀態，且當您將滑鼠暫留在狀態上方時，會顯示容器中所有 Pod 的彙總狀態。 如果沒有就緒狀態，則狀態值會顯示 **(0)** 。

在選取器中，選取 [容器]。

![選取容器視圖](./media/container-insights-analyze/containers-containers-tab.png)

您可以在這裡檢視 Azure Kubernetes 和 Azure 容器執行個體容器的效能健康情況。 

![\<> 容器的名稱效能視圖](./media/container-insights-analyze/containers-containers-view.png)

您可以從容器向下切入到 Pod 或節點，以檢視針對該物件所篩選的效能資料。 選取特定容器的 [ **Pod** ] 或 [**節點**] 資料行底下的值。

![效能視圖中從節點到容器的向下切入範例](./media/container-insights-analyze/drill-down-controller-node.png)

下表說明您在查看容器時所顯示的資訊。

| 「資料行」 | 描述 | 
|--------|-------------|
| Name | 控制器的名稱。|
| 狀態 | 容器的狀態，若有的話。 下一個表格會提供狀態圖示的其他詳細資料。|
| 最&nbsp;小%、&nbsp;平均%、&nbsp;第 50%&nbsp;、第 90&nbsp;%、第 95%、最大值&nbsp;% | 針對所選取計量和百分位數之每個實體的平均百分比彙總。 |
| Min、Avg、第50、90、95、Max | 容器針對所選取百分位數的平均 CPU millicore 或記憶體效能彙總。 平均值是從為 Pod 設定的 CPU/記憶體限制測量所得。 |
| Pod | Pod 所在的容器。| 
| 節點 |  容器所在的節點。 | 
| 重新啟動 | 代表自容器啟動以來經過的時間。 |
| 執行時間 | 代表自容器啟動或重新啟動以來經過的時間。 |
| 趨勢最&nbsp;小值%&nbsp;、平均%&nbsp;、第 50&nbsp;%、第&nbsp;90%、第 95%、最大值&nbsp;% | 長條圖趨勢代表容器的平均百分位數計量百分比。 |

[狀態] 欄位中的圖示會指出 pod 的線上狀態, 如下表所述。
 
| 圖示 | 狀態 |  
|--------|-------------|  
| ![準備好執行的狀態圖示](./media/container-insights-analyze/containers-ready-icon.png) | 執行中 (就緒)|  
| ![等待或暫停狀態圖示](./media/container-insights-analyze/containers-waiting-icon.png) | 等候中或已暫停|  
| ![上次回報的執行狀態圖示](./media/container-insights-analyze/containers-grey-icon.png) | 上次回報的執行，但未回應的時間超過 30 分鐘|  
| ![終止的狀態圖示](./media/container-insights-analyze/containers-terminated-icon.png) | 已成功停止或無法停止|  
| ![失敗狀態圖示](./media/container-insights-analyze/containers-failed-icon.png) | 失敗狀態 |  

## <a name="workbooks"></a>活頁簿

活頁簿將文字、 [記錄查詢](../log-query/query-language.md)、[計量](../platform/data-platform-metrics.md)和參數結合成豐富的互動式報表。 活頁簿可以由具有相同 Azure 資源存取權的其他小組成員編輯。

適用于容器的 Azure 監視器包含四個可協助您開始使用的活頁簿:

- **磁片容量**:顯示每個磁片的互動式磁片使用方式圖表, 其由下列觀點呈現給容器內的節點:

    - 所有磁片的磁片使用量百分比。
    - 所有磁片的可用磁碟空間。
    - 此方格會顯示每個節點的磁片、其已使用空間的百分比、已使用空間的百分比、可用磁碟空間 (GiB), 以及可用磁碟空間 (GiB) 的趨勢。 在資料表中選取資料列時, 資料列底下會顯示已使用的空間和可用磁碟空間百分比 (GiB)。 

- **磁片 IO**:針對根據下列各方面呈現給容器內節點的每個磁片, 提供互動式磁片使用圖表:

    - 所有磁片上的磁片 i/o 摘要, 依讀取位元組/秒、寫入位元組/秒, 以及讀取和寫入位元組/秒趨勢。
    - 八個效能圖表會顯示關鍵效能指標, 以協助測量和識別磁片 i/o 瓶頸。

- **Kubelet**:包含兩個格線, 其中顯示主要節點作業統計資料:

    - 依節點方格的總覽摘要列出每個節點的作業總計、錯誤總數, 以及每個節點的百分比和趨勢。
    - 依作業類型的總覽摘要列出每個作業的總計、錯誤總數, 以及依百分比和趨勢的成功操作。

- **網路**：提供每個節點網路介面卡的互動式網路使用圖表, 而方格則呈現關鍵效能指標, 以協助測量網路介面卡的效能。

您可以從 [**查看活頁簿**] 下拉式清單中選取每個活頁簿來存取這些活頁簿。

![[查看活頁簿] 下拉式清單](./media/container-insights-analyze/view-workbooks-dropdown-list.png)

## <a name="next-steps"></a>後續步驟

- 請參閱[使用容器的 Azure 監視器建立效能警示](container-insights-alerts.md), 以瞭解如何建立高 CPU 和記憶體使用率的警示, 以支援您的 DevOps 或操作程式和程式。
- 查看[記錄查詢範例](container-insights-log-search.md#search-logs-to-analyze-data), 以查看預先定義的查詢和評估或自訂的範例, 以警示、視覺化或分析您的叢集。
