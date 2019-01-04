---
title: 適用於 VM 的 Azure 監視器 (預覽) 常見問題集 | Microsoft Docs
description: 適用於 VM 的 Azure 監視器是 Azure 中的一個解決方案，結合了 Azure VM 作業系統的健康情況和效能監視。 它會自動探索應用程式元件以及與其他資源的相依性，並對應兩者之間的通訊。 本文提供常見問題的解答。
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
ms.date: 11/08/2018
ms.author: magoedte
ms.openlocfilehash: a97a7be0eaa8438a4df27b610106ec6ab9f60d30
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184384"
---
# <a name="azure-monitor-for-vms-preview-faq"></a>適用於 VM 的 Azure 監視器 (預覽) 常見問題集
本文將回答有關適用於 VM 的 Azure 監視器的常見問題。 若您有任何關於解決方案的其他問題，請前往 [Azure 討論論壇](https://feedback.azure.com/forums/34192--general-feedback)並張貼您的問題。 當問到常見問題時，我們會將其新增至本文，以便其他人可以快速輕鬆地找到這些問題。

## <a name="can-i-deploy-vms-to-an-existing-workspace"></a>我可以將 VM 部署到現有的工作區嗎？
如果您的虛擬機器已經連線到 Log Analytics 工作區，則您可以在將虛擬機器部署到適用於 VM 的 Azure 監視器時，繼續使用該工作區。 此工作區必須存在於[部署適用於 VM 的 Azure 監視器 (預覽)](vminsights-onboard.md#prerequisites) 的「必要條件」一節所列的其中一個支援的區域中。

在部署期間，我們會設定工作區的效能計數器。 此動作會使向工作區報告資料的 VM 開始收集資訊，以便在適用於 VM 的 Azure 監視器中顯示並進行分析。 因此，您將會看到來自所有連線到所選工作區之 VM 的效能資料。 健康情況和對應功能只能針對您已指定用於部署的 VM 加以啟用。

如需有關啟用哪些效能計數器的詳細資訊，請參閱[部署適用於 VM 的 Azure 監視器 (預覽)](vminsights-onboard.md)。

## <a name="can-i-deploy-vms-to-a-new-workspace"></a>我可以將 VM 部署至新的工作區嗎？ 
如果您的 VM 目前並未連線到現有的 Log Analytics 工作區，您需要建立新的工作區來儲存資料。 您可以在 Azure 入口網站中，針對適用於 VM 的 Azure 監視器設定單一 VM，以建立一個工作區。

如果您選擇使用指令碼型方法，請參閱[部署適用於 VM 的 Azure 監視器 (預覽)](vminsights-onboard.md)。 

## <a name="what-can-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>如果我的 VM 已經向現有的工作區回報，我該怎麼做？
如果您已經從虛擬機器收集資料，則您可能已經將其設為向現有的 Log Analytics 工作區回報資料。 只要該工作區位於我們支援的其中一個區域，您就可以針對該預先存在的工作區啟用適用於 VM 的 Azure 監視器。 我們正積極努力地支援更多區域。

>[!NOTE]
>我們會針對工作區設定效能計數器，其會影響要向該工作區回報的所有 VM，而不論您是否已經選擇要將它們部署到適用於 VM 的 Azure 監視器。 如需有關如何為工作區設定效能計數器的詳細資訊，請參閱的 [Log Analytics 中的 Windows 和 Linux 效能資料來源](../../azure-monitor/platform/data-sources-performance-counters.md)的「設定效能計數器」一節。 如需針對適用於 VM 的 Azure 監視器所設定的計數器相關資訊，請參閱[部署適用於 VM 的 Azure 監視器 (預覽)](vminsights-onboard.md)。 

## <a name="why-did-my-vm-deployment-fail"></a>為什麼我的 VM 部署會失敗？
從 Azure 入口網站部署 Azure VM 時，會發生下列事件：

* 如果已選取該選項，即會建立預設的 Log Analytics 工作區。
* 已針對所選工作區設定效能計數器。 如果這個步驟失敗，則部分效能圖表和表格不會顯示您所部署之 VM 的資料。 您可以執行[部署適用於 VM 的 Azure 監視器 (預覽)](vminsights-onboard.md#enable-with-powershell) 的「使用 PowerShell 啟用」一節中記載的 PowerShell 指令碼來修正此問題。
* 如果需要，使用 VM 延伸模組在 Azure VM 上安裝 Log Analytics 代理程式。 
* 如果需要，使用使用延伸模組在 Azure VM 上安裝適用於 VM 的 Azure 監視器對應 Dependency Agent。 
* 視需要設定支援健康情況功能的 Azure 監視器元件，並設定 VM 回報健康情況資料。

在部署期間，我們會針對每個上述步驟檢查狀態，並在入口網站中，將通知狀態傳回給您。 設定工作區與代理程式安裝通常需要 5 至 10 分鐘。 在 Azure 入口網站中檢視監視與健康情況資料需要額外的 5 至 10 分鐘。 

如果您已經起始部署，並看到一則訊息指出必須部署 VM，則允許 VM 最多 30 分鐘的時間來完成此程序。 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>我在適用於 VM 的效能圖表中看不到部分或任何資料
如果效能資料未顯示在磁碟表格或效能圖表中，則您可能未在工作區中設定效能計數器。 若要解決這個問題，請執行[部署適用於 VM 的 Azure 監視器 (預覽)](vminsights-onboard.md#enable-with-powershell) 的「使用 PowerShell 啟用」一節中記載的 PowerShell 指令碼。

## <a name="how-is-the-azure-monitor-for-vms-map-feature-different-from-service-map"></a>適用於 VM 的 Azure 監視器對應功能與服務對應有何不同？
適用於 VM 的 Azure 監視器對應功能以服務對應為基礎，但有下列差異：

* 您可以從 VM 窗格，以及從 Azure 監視器下方適用於 VM 的 Azure 監視器存取 [對應] 檢視。
* [對應] 中的連線現在可供點選，並在側邊面板中顯示連線計量資料。
* 新的 API 用於建立對應，以便為更複雜的對應提供更好的支援。
* 受監視的 VM 現在位於用戶端群組節點中，而環圈圖會顯示受監視與未受監視的虛擬機器比例。 您也可以在展開群組時篩選機器清單。
* 受監視的虛擬機器現在位於伺服器連接埠群組節點中，而環圈圖會顯示受監視與未受監視的機器比例。 您也可以在展開群組時篩選機器清單。
* 對應樣式已經過更新，與 Azure Application Insights 的應用程式對應更為一致。
* 側邊面板已經過更新，但是還沒有服務對應支援的完整整合：更新管理、變更追蹤、安全性和服務台。 
* 選擇要對應之群組與機器的選項已經過更新。 它現在支援訂用帳戶、資源群組、Azure 虛擬機器擴展集和雲端服務。
* 您無法在適用於 VM 的 Azure 監視器對應功能中建立新的服務對應機器群組。 

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>為什麼我的效能圖表會顯示虛線？

效能圖表顯示虛線而不是實線的幾個原因：
* 在資料集合中可能會有間隔。 

* 資料取樣的預設設定是每隔 60 秒。 若您針對圖表選擇較窄的時間範圍，而且您的取樣頻率小於圖表中所使用的貯體大小，則您會看到虛線。 假設您選擇的取樣頻率為 10 分鐘，而圖表上的每個貯體是 5 分鐘。 在此情況下，選擇更寬的時間範圍來檢視，應該會讓圖表線條顯示為實線，而非虛線。

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>適用於 VM 的 Azure 監視器支援群組嗎？
是，在您安裝 Dependency Agent 後，我們就會從 VM 收集資訊，以根據訂用帳戶、資源群組、虛擬機器擴展集和雲端服務顯示群組。 如果您先前使用服務對應，並已建立電腦群組，這些群組將會一併顯示。 如果您為您所檢視的工作區建立了電腦群組，這些群組也會出現在群組篩選條件中。 

## <a name="how-can-i-display-the-details-about-whats-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>如何顯示彙總效能圖表中衍生第 95 條百分位數線之項目的詳細資料？
根據預設，系統會為清單排序，以便向您顯示所選計量的第 95 條百分位數線為最高值的 VM。 例外狀況是**可用的記憶體**圖表，此圖表會顯示第 5 條百分位數線為最低值的機器。 選取圖表，以利用已選取的適當計量來開啟 [N 大排行榜] 檢視。

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-various-virtual-networks-and-subnets"></a>對應功能如何跨不同的虛擬網路和子網路處理重複的 IP？
如果您跨子網路和虛擬網路使用 VM 或 Azure 虛擬機器擴展集來複製 IP 範圍，它可能會造成適用於 VM 的 Azure 監視器對應功能顯示不正確的資訊。 我們已經意識到這個問題，而且我們正在研究改善體驗的選項。

## <a name="does-the-map-feature-support-ipv6"></a>對應功能支援 IPv6 嗎？
對應功能目前僅支援 IPv4，而且我們正在研究對於 IPv6 的支援。 我們也支援在 IPv6 內部設定通道的 IPv4。

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-why-is-the-map-difficult-to-view"></a>當我針對資源群組或其他大型群組載入對應時，為什麼難以檢視該對應？
儘管我們已經改進對應功能來處理大型且複雜的設定，但我們理解到對應可能擁有許多節點、連線，以及作為叢集使用的節點。 我們承諾會繼續加強支援以提高延展性。  

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-from-the-network-chart-on-the-azure-vm-overview-page"></a>[效能] 索引標籤上的網路圖表為什麼看起來與 [Azure VM 概觀] 頁面上的網路圖表不同呢？

適用於 Azure VM 的 [概觀] 頁面會根據客體 VM 中主機的活動量值來顯示圖表。 [Azure VM 概觀] 頁面上的網路圖表則只會顯示將收費的網路流量。 此顯示不包含虛擬網路之間的流量。 針對適用於 VM 的 Azure 監視器顯示的資料和圖表是以來自客體 VM 的資料為基礎，而網路圖表則會顯示輸入和輸出到該 VM (包括虛擬網路間) 的所有 TCP/IP 流量。

## <a name="what-are-the-limitations-of-the-log-analytics-free-pricing-plan"></a>採用 Log Analytics 免費定價方案有什麼限制？
如果您使用*免費*定價層設定具有 Log Analytics 工作區的 Azure 監視器，則適用於 VM 的 Azure 監視器對應功能僅支援將五部機器連線到工作區。 

例如，假設您有五部連線到免費工作區的 VM。 如果您中斷連線一部 VM，並在稍後連線新的 VM，則在 [對應] 頁面上不會監視和反映新的 VM。 在此情況下，當您開啟新的 VM 時，您會收到使用 [立即試用] 選項，並從左窗格選取 [Insights (預覽)] 的提示 (即使是在已安裝在 VM 上之後)。 不過，如果 VM 未部署至適用於 VM 的 Azure 監視器，您就不會如同以往般收到選項的相關提示。 

## <a name="next-steps"></a>後續步驟
若要了解啟用虛擬機器監視的需求和方法，請檢閱[部署適用於 VM 的 Azure 監視器 (預覽)](vminsights-onboard.md)。
