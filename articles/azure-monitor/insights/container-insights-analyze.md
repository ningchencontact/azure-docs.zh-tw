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
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: dc55e4999a09c45463ae75b05d610b290f5ff526
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/16/2019
ms.locfileid: "68248325"
---
# <a name="understand-aks-cluster-performance-with-azure-monitor-for-containers"></a>使用適用於容器的 Azure 監視器來了解 AKS 叢集效能 
使用適用於容器的 Azure 監視器，您可以使用效能圖和健全狀態，從下列兩個檢視方塊中監視 Azure Kubernetes Service (AKS) 叢集的工作負載：直接從 AKS 叢集，或者從 Azure 監視器監視訂用帳戶中的所有 AKS 叢集。 當您監視特定的 AKS 叢集時，也可以檢視 Azure 容器執行個體 (ACI)。

本文將協助您了解這兩個檢視方塊之間的體驗，以及如何協助您快速地評估、調查和解決所偵測到的問題。

如需啟用適用於容器的 Azure 監視器相關資訊，請參閱[將適用於容器的 Azure 監視器上線](container-insights-onboard.md)。

Azure 監視器提供多叢集視圖, 顯示在訂用帳戶的所有資源群組中, 執行 Linux 和 Windows Server 2019 的所有受監視 AKS 叢集的健全狀況狀態。  它也會顯示探索到且未受到解決方案監視的 AKS 叢集。 您可以立即了解叢集健康情況，並從這裡向下切入到節點和控制器效能頁面，或者瀏覽以查看叢集的效能圖。  針對探索到且識別為未受監視的 AKS 叢集，您隨時都可針對該叢集啟用監視。  

與 Linux 叢集相較之下, 針對容器的 Azure 監視器監視 Windows Server 叢集的主要差異如下:

- Windows 節點和容器無法使用記憶體 RSS 計量。
- Windows 節點無法使用磁片儲存體容量資訊。
- Windows 容器記錄除外, 提供即時記錄支援。
- 只有 pod 環境會受到監視, 而不是 Docker 環境。
- 在預覽版本中, 最多可支援30個 Windows Server 容器。 此限制不適用於 Linux 容器。  

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站
登入 [Azure 入口網站](https://portal.azure.com)。 

## <a name="multi-cluster-view-from-azure-monitor"></a>Azure 監視器中的多叢集檢視 
若要檢視已部署的所有 AKS 叢集健康狀態，請從 Azure 入口網站的左側窗格選取 [監視]  。  在 [Insights]  區段下方，選取 [容器]  。  

![Azure 監視器多叢集儀表板範例](./media/container-insights-analyze/azmon-containers-multiview.png)

在 [受監視的叢集]  索引標籤上，您可以了解下列內容：

1. 有多少個叢集處於重大或狀況不良狀態，以及有多少個叢集的狀況良好或未報告 (稱為未知狀態)？
2. 我的 [Azure Kubernetes 引擎 (AKS-engine)](https://github.com/Azure/aks-engine) 部署全部都狀況良好嗎？
3. 每個叢集有多少個節點、使用者和系統 pod 部署？
4. 有多少可用的磁碟空間, 還有容量問題嗎？

內含的健全狀態如下： 

* **狀況良好**：針對 VM 並未偵測到任何問題，而其會視需要運作。 
* **重大**：已偵測到一或多個重大問題，您必須解決這類問題，才能如預期般地還原正常的作業狀態。
* **警告**：已偵測到一或多個問題，您必須解決這類問題，或者健康情況可能會變成「重大」。
* **未知**：如果服務無法建立與節點或 Pod 的連線，狀態就會變更為未知狀態。
* **找不到**：已刪除工作區、資源群組或包含適用於此解決方案之工作區的訂用帳戶。
* **未經授權**：使用者沒有讀取工作區中資料的必要權限。
* **錯誤**：嘗試從工作區讀取資料時發生錯誤。
* 設定**錯誤**-未在指定的工作區中正確設定容器的 Azure 監視器。
* **沒有資料**：過去 30 分鐘內並未向工作區回報任何資料。

健全狀況狀態會將整體叢集狀態計算為三個狀態的*最差*, 但有一個例外狀況, 如果三個狀態的其中一個*不明*, 則整體叢集狀態會顯示 [**不明**]。  

下表提供計算明細，其會在多叢集檢視上控制受監視叢集的健康狀態。

| |狀態 |可用性 |  
|-------|-------|-----------------|  
|**使用者 Pod**| | |  
| |Healthy |100% |  
| |警告 |90 - 99% |  
| |重要 |<90% |  
| |不明 |如果未在過去 30 分鐘內報告 |  
|**系統 Pod**| | |  
| |Healthy |100% |
| |警告 |N/A |
| |重要 |<100% |
| |不明 |如果未在過去 30 分鐘內報告 |
|**Node** | | |
| |Healthy |>85% |
| |警告 |60 - 84% |
| |重要 |<60% |
| |不明 |如果未在過去 30 分鐘內報告 |

從叢集清單中，您可按一下叢集名稱以向下切入到 [叢集]  頁面、在該特定叢集的 [節點]  資料行中按一下節點的彙總以向下切入到 [節點]  效能頁面，或者按一下 [使用者 Pod]  或 [系統 Pod]  資料行的彙總以向下切入到 [控制器]  效能頁面。   

## <a name="view-performance-directly-from-an-aks-cluster"></a>直接從 AKS 叢集檢視效能
若要存取適用於容器的 Azure 監視器，可從左側窗格中選取 [Insights]  ，直接從 AKS 叢集存取。 關於 AKS 叢集的檢視資訊，會組織成四個檢視方塊：

- 叢集
- 節點 
- Controllers  
- 容器

當您按一下 [Insights]  時所開啟的預設頁面為 [叢集]  ，其包含四個效能折線圖來顯示您叢集的關鍵效能計量。 

![[叢集] 索引標籤上的範例效能圖表](./media/container-insights-analyze/containers-cluster-perfview.png)

效能圖表會顯示四個效能計量：

- **節點 CPU 使用率&nbsp;%** ：整個叢集中 CPU 使用率的彙總檢視方塊。 您可以從圖表上方的百分位數選取器中，個別或合併選取 [Avg]  、[Min]  、[Max]  、[50th]  、[90th]  和 [95th]  ，來篩選時間範圍的結果。 
- **節點記憶體使用率&nbsp;%** ：整個叢集中記憶體使用率的彙總檢視方塊。 您可以從圖表上方的百分位數選取器中，個別或合併選取 [Avg]  、[Min]  、[Max]  、[50th]  、[90th]  和 [95th]  ，來篩選時間範圍的結果。 
- **節點計數**：Kubernetes 中的節點計數和狀態。 所表示的叢集節點狀態為 [所有]  、[就緒]  和 [尚未就緒]  ，且可以在圖表上方的選取器中進行個別篩選或結合。 
- **活動 Pod 計數**：Kubernetes 中的 Pod 計數和狀態。 所表示的叢集節點狀態為 [所有]  、[擱置]  、[執行中]  和 [未知]  ，且可以在圖表上方的選取器中進行個別篩選或結合。 

您可以使用向左/向右鍵循環顯示圖表上的每個資料點，使用向上/向下鍵來循環顯示百分位數線。 按一下任何一個圖表右上角的釘選圖示, 即可將選取的圖表釘選到上次查看的 Azure 儀表板。 您可以從儀表板調整圖表的大小和位置。 從儀表板選取圖表會將您重新導向至容器的 Azure 監視器, 並載入正確的範圍和視圖。

容器的 Azure 監視器也支援 Azure 監視器[計量瀏覽器](../platform/metrics-getting-started.md), 您可以在其中建立自己的繪圖圖表、相互關聯和調查趨勢, 以及釘選到儀表板。 在計量瀏覽器中, 您也可以使用您已設定的準則, 將您的計量視覺化為以[度量為基礎的警示規則](../platform/alerts-metric.md)的基礎。  

## <a name="view-container-metrics-in-metrics-explorer"></a>在計量瀏覽器中查看容器計量
在計量瀏覽器中, 您可以從容器的 Azure 監視器中, 查看匯總的節點和 pod 使用率計量。 下表摘要說明的詳細資料, 可協助您瞭解如何使用計量圖表來視覺化容器計量。

|命名空間 | 計量 |
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

您可以套用度量的[分割](../platform/metrics-charts.md#apply-splitting-to-a-chart)以依據維度來進行查看, 並以視覺化方式將不同區段彼此比較。 對於節點, 您可以依*主機*維度分割圖表, 也可以從 pod 將其分割為下列維度:

* Controller
* Kubernetes 命名空間
* 節點
* 階段

## <a name="analyze-nodes-controllers-and-container-health"></a>分析節點、控制器和容器健全狀況

當您切換至 [節點]  、[控制站]  和 [容器]  索引標籤時，頁面右邊會自動顯示屬性窗格。 它會顯示已選取專案的屬性, 包括您定義來組織 Kubernetes 物件的標籤。 選取 [Linux] 節點時, 它也會顯示在 [**本機磁片容量**可用磁碟空間] 和 [用於每個磁片] 節點所用的 [百分比] 區段底下。 按一下窗格中的 **>>** 連結，以檢視\隱藏此窗格。 

![範例 Kubernetes 觀點屬性窗格](./media/container-insights-analyze/perspectives-preview-pane-01.png)

當您展開階層中的物件時，屬性窗格會根據所選的物件更新。 在此窗格中，您也可以按一下窗格頂端的 [檢視 Kubernetes 事件記錄]  連結，使用已預先定義的記錄搜尋來檢視 Kubernetes 事件。 如需檢視 Kubernetes 記錄資料的詳細資訊，請參閱[搜尋記錄來分析資料](container-insights-log-search.md)。 當您查看叢集資源時, 您可以即時查看容器記錄和事件。 如需這項功能的詳細資訊, 以及授與和控制存取所需的設定, 請參閱[如何使用適用于容器的 Azure 監視器來即時查看記錄](container-insights-live-logs.md)。 

使用頁面頂端的 [ **+ 新增篩選**器] 選項, 依**服務**、**節點**、**命名空間**或**節點集**區篩選視圖的結果, 然後在選取篩選範圍之後, 選取其中一個顯示在**選取 [值**] 欄位。  設定篩選器之後，就會在檢視 AKS 叢集的任一個檢視方塊時全域套用。  此公式僅支援等號。  您可以在第一個篩選器上方新增其他篩選器，進一步縮小您的結果。  例如，如果您依**節點**指定篩選器，則您的第二個篩選器只允許您選取 [服務]  或 [命名空間]  。  

![使用篩選器來縮小結果的範例](./media/container-insights-analyze/add-filter-option-01.png)

在一個索引標籤中指定篩選器會持續在您選取另一個篩選器時套用，並在您按一下指定篩選器旁邊的 **x** 符號之後加以刪除。   

切換至 [節點]  索引標籤，而資料列階層會依循 Kubernetes 物件模型，從您叢集中的節點開始。 展開節點後，您便可以檢視一或多個在節點上執行的 Pod。 若有超過一個容器被分組至 Pod，它們會以最後一個資料列的形式顯示於階層中。 當主機有處理器或記憶體的壓力時，您也可檢視主機上有多少非 Pod 相關的工作負載正在執行。

![效能檢視中的範例 Kubernetes 節點階層](./media/container-insights-analyze/containers-nodes-view.png)

執行 Windows Server 2019 OS 的 windows Server 容器會顯示在清單中所有以 Linux 為基礎的節點之後。 當您展開 Windows Server 節點時, 您可以查看在節點上執行的一或多個 pod 和容器。 選取節點時, [屬性] 窗格會顯示版本資訊, 但不包括代理程式資訊, 因為 Windows Server 節點並未安裝代理程式。  

![列出 Windows Server 節點的範例節點階層](./media/container-insights-analyze/nodes-view-windows.png) 

執行 Linux OS 的 Azure 容器執行個體虛擬節點會顯示於清單中的最後一個 AKS 叢集節點之後。  當您展開 ACI 虛擬節點時，您可以檢視在節點上執行的一或多個 ACI Pod 和容器。  系統不會針對節點收集並回報計量，僅會針對 Pod 進行。

![列出容器執行個體的範例節點階層](./media/container-insights-analyze/nodes-view-aci.png)

從展開的節點中，您可以從節點上執行的 Pod 或容器向下切入到控制器，以檢視針對該控制器所篩選的效能資料。 按一下特定節點 [控制器]  欄下方的值。   
![效能檢視中從節點到控制器的向下切入範例](./media/container-insights-analyze/drill-down-node-controller.png)

您可以從頁面頂端選取控制器或容器，然後檢閱那些物件的狀態與資源使用率。  如果您想要改為檢閱記憶體使用率，請在 [計量]  下拉式清單中選取 [記憶體 RSS]  或 [記憶體工作集]  。 只有 Kubernetes 1.8 版和更新版本才支援**記憶體 RSS**。 否則，您會以 *NaN&nbsp;%* 的形式檢視 **Min&nbsp;%** 的值，這是代表未定義或無法顯示之值的數值資料類型值。

記憶體工作集會顯示內含的記憶體和虛擬記憶體 (快取), 以及應用程式所使用的總數。 記憶體 RSS 只會顯示主儲存體, 也就是常駐記憶體。 此度量會顯示可用記憶體的實際容量。

![容器節點效能檢視](./media/container-insights-analyze/containers-node-metric-dropdown.png)

根據預設，效能資料是以過去六小時的資料為基礎，但您可以使用左上方的 [時間範圍]  選項來變更時間長度。 您也可以在百分位數選取器中選取 [Avg]  、[Min]  、[Max]  、[50th]  、[90th]  和 [95th]  ，來篩選時間範圍內的結果。 

![資料篩選的百分位數選取範圍](./media/container-insights-analyze/containers-metric-percentile-filter.png)

當您將滑鼠暫留在 [趨勢]  資料行底下的長條圖上方時，每個長條圖都會依據所選取的計量，在 15 分鐘的取樣期間內顯示 CPU 或記憶體使用量。 透過鍵盤選取趨勢圖後，您可以使用 Alt+PageUp 或 Alt+PageDown 鍵來循環顯示每個橫條，並取得和滑鼠指標置於其上時相同的詳細資訊。

![趨勢橫條圖滑鼠停留在範例上](./media/container-insights-analyze/containers-metric-trend-bar-01.png)    

在下列範例中，請注意清單中的第一個節點 *aks-nodepool1-* ，**容器**的值為 9，這是已部署容器總數的彙總。

![每個節點範例的彙總容器數](./media/container-insights-analyze/containers-nodes-containerstotal.png)

這可幫助您快速識別叢集中節點之間的容器是否出現不平衡的情況。 

當您檢視節點時所顯示的資訊已詳述於下表：

| 「資料行」 | 描述 | 
|--------|-------------|
| 名稱 | 主機的名稱。 |
| 狀態 | 節點狀態的 Kubernetes 檢視。 |
| Avg&nbsp;%、Min&nbsp;%、Max&nbsp;%、50th&nbsp;%、90th&nbsp;% | 根據選取期間內百分位數的平均節點百分比。 |
| Avg、Min、Max、50th、90th | 根據所選取時間的期間內百分位數的平均節點實際值。 從為節點設定的 CPU/記憶體限制測量所得的平均值；對於 Pod 與容器而言，則是主機所報告的平均值。 |
| 容器 | 容器的數目。 |
| Uptime | 呈現自節點啟動或重新啟動以來經過的時間。 |
| Controllers | 僅適用於容器與 Pod。 顯示它位在哪個控制器之中。 並非所有 Pod 都位在控制器之中，因此有些可能會顯示 **N/A**。 | 
| Trend Avg&nbsp;%、Min&nbsp;%、Max&nbsp;%、50th&nbsp;%、90th&nbsp;% | 長條圖趨勢代表控制器的平均百分位數計量百分比。 |

在選取器中，選取 [控制器]  。

![選取控制器檢視](./media/container-insights-analyze/containers-controllers-tab.png)

您可以在此處檢視控制器的效能健康情況，以及未連線到控制器的 ACI 虛擬節點控制器或虛擬節點 Pod。

![\<> 控制器的名稱 效能視圖](./media/container-insights-analyze/containers-controllers-view.png)

資料列階層以控制器為首，展開控制器時，您會看到一或多個 Pod。  展開 Pod，最後一個資料列會顯示分組至 Pod 的容器。 從展開的控制器中，您可以向下切入到控制器執行所在的節點，以檢視針對該節點所篩選的效能資料。 未連線到控制器的 ACI Pod 會列為清單中的最後一個。

![列出容器執行個體 Pod 的範例控制器階層](./media/container-insights-analyze/controllers-view-aci.png)

按一下特定控制器 [節點]  欄下方的值。   

![效能檢視中從節點到控制器的向下切入範例](./media/container-insights-analyze/drill-down-controller-node.png)

在您檢視控制器時所顯示的資訊已詳述於下表：

| 「資料行」 | 描述 | 
|--------|-------------|
| 名稱 | 控制器的名稱。|
| 狀態 | 容器的彙總狀態，就是當其完成執行後的狀態，例如「確定」  、「終止」  、「失敗」  、「停止」  或「暫停」  。 如果容器在執行中，但狀態卻未正確呈現或未由代理程式擷取，而且超過 30 分鐘都沒有回應時，則狀態為 [未知]  。 下表中提供狀態圖示的其他詳細資料。|
| Avg&nbsp;%、Min&nbsp;%、Max&nbsp;%、50th&nbsp;%、90th&nbsp;% | 針對所選取計量和百分位數的每個實體匯總平均百分比的平均值。 |
| Avg、Min、Max、50th、90th  | 彙總容器所選百分位數的平均 CPU millicore 或記憶體效能。 平均值是從為 Pod 設定的 CPU/記憶體限制測量所得。 |
| 容器 | 該控制器或 Pod 的容器總數。 |
| Restarts | 積存容器中的重新啟動計數。 |
| Uptime | 代表自容器啟動以來經過的時間。 |
| 節點 | 僅適用於容器與 Pod。 顯示它位在哪個控制器之中。 | 
| Trend Avg&nbsp;%、Min&nbsp;%、Max&nbsp;%、50th&nbsp;%、90th&nbsp;%| 長條圖趨勢代表控制器的平均百分位數計量。 |

[狀態] 欄位中的圖示會指出容器的線上狀態：
 
| 圖示 | 狀態 | 
|--------|-------------|
| ![準備好執行的狀態圖示](./media/container-insights-analyze/containers-ready-icon.png) | 執行中 (就緒)|
| ![等候中或已暫停狀態圖示](./media/container-insights-analyze/containers-waiting-icon.png) | 等候中或已暫停|
| ![上次回報的執行狀態圖示](./media/container-insights-analyze/containers-grey-icon.png) | 上次回報的執行，但尚未回應超過 30 分鐘|
| ![成功狀態圖示](./media/container-insights-analyze/containers-green-icon.png) | 已成功停止或無法停止|

狀態圖示會根據 Pod 所提供的功能顯示計數。 它會顯示最差的兩個狀態，且當您將滑鼠暫留在狀態上方時，會顯示容器中所有 Pod 的彙總狀態。 如果沒有就緒狀態，則狀態值會顯示 **(0)** 。 

在選取器中，選取 [容器]  。

![選取容器檢視](./media/container-insights-analyze/containers-containers-tab.png)

您可以在這裡檢視 Azure Kubernetes 和 Azure 容器執行個體容器的效能健康情況。  

![\<> 控制器的名稱 效能視圖](./media/container-insights-analyze/containers-containers-view.png)

您可以從容器向下切入到 Pod 或節點，以檢視針對該物件所篩選的效能資料。 按一下特定容器的 [Pod]  或 [節點]  欄。   

![效能檢視中從節點到控制器的向下切入範例](./media/container-insights-analyze/drill-down-controller-node.png)

在您檢視容器時所顯示的資訊已詳述於下表：

| 「資料行」 | 描述 | 
|--------|-------------|
| 名稱 | 控制器的名稱。|
| 狀態 | 容器的狀態，若有的話。 下一個表格會提供狀態圖示的其他詳細資料。|
| Avg&nbsp;%、Min&nbsp;%、Max&nbsp;%、50th&nbsp;%、90th&nbsp;% | 針對所選取計量和百分位數之每個實體的平均百分比彙總。 |
| Avg、Min、Max、50th、90th  | 容器針對所選取百分位數的平均 CPU millicore 或記憶體效能彙總。 平均值是從為 Pod 設定的 CPU/記憶體限制測量所得。 |
| Pod | Pod 所在的容器。| 
| 節點 |  容器所在的節點。 | 
| Restarts | 代表自容器啟動以來經過的時間。 |
| Uptime | 代表自容器啟動或重新啟動以來經過的時間。 |
| Trend Avg&nbsp;%、Min&nbsp;%、Max&nbsp;%、50th&nbsp;%、90th&nbsp;% | 長條圖趨勢代表容器的平均百分位數計量百分比。 |

[狀態] 欄位中的圖示會指出 Pod 的線上狀態，如下表所述：
 
| 圖示 | 狀態 |  
|--------|-------------|  
| ![準備好執行的狀態圖示](./media/container-insights-analyze/containers-ready-icon.png) | 執行中 (就緒)|  
| ![等候中或已暫停狀態圖示](./media/container-insights-analyze/containers-waiting-icon.png) | 等候中或已暫停|  
| ![上次回報的執行狀態圖示](./media/container-insights-analyze/containers-grey-icon.png) | 上次回報的執行，但未回應的時間超過 30 分鐘|  
| ![終止的狀態圖示](./media/container-insights-analyze/containers-terminated-icon.png) | 已成功停止或無法停止|  
| ![失敗狀態圖示](./media/container-insights-analyze/containers-failed-icon.png) | 失敗狀態 |  

## <a name="disk-capacity-workbook"></a>磁片容量活頁簿
活頁簿將文字、 [記錄查詢](../log-query/query-language.md)、[計量](../platform/data-platform-metrics.md)和參數結合成豐富的互動式報表。 活頁簿可以由具有相同 Azure 資源存取權的其他小組成員編輯。

適用于容器的 Azure 監視器包含可協助您開始使用的活頁簿, 也就是**磁片容量**。  此活頁簿會根據下列觀點, 呈現呈現給容器內節點之每個磁片的互動式磁片使用量圖表:

- 所有磁片的磁片% 使用量
- 所有磁片的可用磁碟空間
- 顯示每個節點磁片、其% 已使用空間、% 已使用空間的趨勢、可用磁碟空間 (GiB), 以及可用磁碟空間 (GiB) 趨勢的表格。 在資料表中選取資料列時,% 已使用的空間和可用磁碟空間 (GiB) 如下所示 

您可以從 [**查看活頁簿**] 下拉式清單中選取 [**磁片容量**] 來存取這個活頁簿。  

![[查看活頁簿] 下拉式清單](./media/container-insights-analyze/view-workbooks-dropdown-list.png)


## <a name="next-steps"></a>後續步驟
- 請參閱[使用適用于容器的 Azure 監視器建立效能警示](container-insights-alerts.md), 以瞭解如何建立高 CPU 和記憶體使用率的警示, 以支援您的 DevOps 或操作程式和程式。 
- 查看[記錄查詢範例](container-insights-log-search.md#search-logs-to-analyze-data), 以查看預先定義的查詢和範例, 以評估或自訂警示、視覺化或分析您的叢集。
