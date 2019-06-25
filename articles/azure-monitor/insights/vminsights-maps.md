---
title: 如何使用適用於 VM 的 Azure 監視器 (預覽) 來檢視應用程式相依性 | Microsoft Docs
description: 地圖是適用於 Vm 的 Azure 監視器的功能。 自動探索 Windows 和 Linux 系統上的應用程式元件，並對應服務之間的通訊。 這篇文章提供有關如何在各種情況中使用 [地圖] 功能的詳細資料。
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
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: f6273e9b6c7ed0c4685479976343497f01201b0b
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206767"
---
# <a name="use-the-map-feature-of-azure-monitor-for-vms-preview-to-understand-application-components"></a>用於 Vm （預覽） 中的 Azure 監視器的 [地圖] 功能，以了解應用程式元件
在 Azure 監視器中的 Vm，您可以檢視探索到的應用程式元件在 Windows 和 Linux 虛擬機器 (Vm)，在 Azure 或您的環境中執行。 您可以觀察兩種方式中的 Vm。 檢視地圖，直接從 VM，或檢視從 Azure 監視器來查看元件的 Vm 群組對應。 本文將協助您了解這兩種檢視方法，以及如何使用 [地圖] 功能。 

如需設定適用於 VM 的 Azure 監視器相關資訊，請參閱[啟用適用於 VM 的 Azure 監視器](vminsights-enable-overview.md)。

## <a name="sign-in-to-azure"></a>登入 Azure
登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="introduction-to-the-map-experience"></a>對應體驗的簡介
前一頭栽進對應體驗，您應該了解方式呈現，並以視覺化方式呈現資訊。 不論是直接從 VM 或 Azure 監視器中，選取 [地圖] 功能，[地圖] 功能提供一致的體驗。 唯一的差別是，從 Azure 監視器 」、 一個 map 會顯示多層式應用程式或叢集的所有成員。

[地圖] 功能會探索執行的處理程序來視覺化 VM 相依性： 

- 伺服器之間的作用中網路連線。
- 輸入和輸出連線的延遲。
- 橫跨任何 TCP 連線架構在指定的時間範圍內的連接埠。  
 
展開以顯示處理序詳細資料和與 VM 通訊這些處理序的 VM。 用戶端群組會顯示連接至 VM 的前端用戶端數目。 伺服器連接埠群組顯示的 VM 會連線到後端伺服器的計數。 展開以查看詳細的清單，透過該連接埠連接的伺服器的伺服器連接埠群組。  

當您選取的 VM**屬性**右邊的窗格會顯示 VM 的屬性。 屬性包括作業系統、 Azure VM 和環圈圖來摘要列出探索到的連線屬性所報告的系統資訊。 

![[屬性] 窗格](./media/vminsights-maps/properties-pane-01.png)

在右側窗格中，選取**記錄事件**顯示一份 VM 傳送至 Azure 監視器的資料。 這項資料可供查詢。  選取任何記錄的類型，來開啟**記錄檔**頁面，您可在其中查看該記錄類型的結果。 您也會看到預先設定的查詢篩選對虛擬機器。  

![[記錄事件] 窗格](./media/vminsights-maps/properties-pane-logs-01.png)

關閉**記錄檔**頁面上，並返回**屬性**窗格。 在該處，選取 **警示**以檢視 VM 健全狀況準則的警示。 [地圖] 功能整合 Azure 警示，以顯示所選的時間範圍內針對所選伺服器的警示。 伺服器會顯示目前的警示圖示並**機器警示**窗格會列出警示。 

![[警示] 窗格](./media/vminsights-maps/properties-pane-alerts-01.png)

若要讓顯示相關警示的 [地圖] 功能，建立適用於特定電腦的警示規則：

- 包含依電腦子句，以將警示分組 (例如**的電腦間隔 1 分鐘**)。
- 基底計量警示。

如需有關 Azure 警示 」 和 「 建立警示規則的詳細資訊，請參閱 <<c0> [ 整合 Azure 監視器中的警示](../../azure-monitor/platform/alerts-overview.md)。

在右上角**圖例**選項描述在地圖上的符號和角色。 進一步了解為您的地圖，並移動，使用右下角的縮放控制項。 您可以設定的縮放層級，以及調整頁面大小的對應。  

## <a name="connection-metrics"></a>連接計量
**連線** 窗格會顯示所選連接從 VM 的標準計量，透過 TCP 連接埠。 計量包括回應時間、每分鐘的要求、流量輸送量及連結。  

![在 [連線] 窗格上的網路連線能力圖表](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>失敗的連線
對應會顯示處理程序和電腦的連線失敗。 紅色虛線指示用戶端系統無法連線到處理序或連接埠。 對於使用相依性代理程式的系統，代理程式會報告失敗的連線嘗試。 [地圖] 功能會觀察無法建立連線的 TCP 通訊端，監視處理程序。 失敗的原因可能是防火牆、 用戶端或伺服器，或無法使用的遠端服務設定不正確。

![在地圖上的連線失敗](./media/vminsights-maps/map-group-failed-connection-01.png)

了解失敗的連線可以幫助您疑難排解、 驗證移轉、 分析的安全性，以及了解服務的整體架構。 失敗的連線有時無害，但通常問題點。 連線可能會失敗，比方說，當容錯移轉環境突然變成無法連線或兩個應用程式層級不能在雲端移轉之後彼此通訊。

### <a name="client-groups"></a>用戶端群組
在地圖上，用戶端群組代表用戶端電腦連線到對應的機器。 單一用戶端群組代表個別的處理序或電腦的用戶端。

![在地圖上的用戶端群組](./media/vminsights-maps/map-group-client-groups-01.png)

若要查看受監視的用戶端和用戶端群組中系統的 IP 位址，請選取群組。 群組的內容會出現下方。  

![在地圖上的 IP 位址的用戶端群組的清單](./media/vminsights-maps/map-group-client-group-iplist-01.png)

如果群組包含受監視和未受監視的用戶端，您可以選取群組的環圈圖，來篩選用戶端的適當章節。

### <a name="server-port-groups"></a>伺服器連接埠群組
伺服器連接埠群組代表具有伺服器的連接埠輸入從對應的機器的連線。 群組包含伺服器連接埠 」 和 「 已連線到該連接埠的伺服器數目。 選取要查看個別伺服器和連線群組。 

![在地圖上的伺服器連接埠群組](./media/vminsights-maps/map-group-server-port-groups-01.png)  

如果群組包含受監視和未受監視的伺服器，您可以選取群組的環圈圖，來篩選伺服器的適當章節。

## <a name="view-a-map-from-a-vm"></a>檢視從 VM 對應 

若要直接從 VM 的 vm 存取 Azure 監視器：

1. 在 Azure 入口網站中，選取 [虛擬機器]  。 
2. 從清單中，選擇 [VM]。 在 **監視**區段中，選擇**Insights （預覽）** 。  
3. 選取 [對應]  索引標籤。

地圖會將虛擬機器的相依性視覺化探索執行處理程序群組，並在指定的時間範圍內具有作用中網路連線的處理序。  

根據預設，對應會顯示過去 30 分鐘。 如果您想要查看過去的相依性的過往情形，您可以查詢過往時間範圍的最多一小時。 若要執行查詢時，使用**TimeRange**左上角的選取器。 例如，在發生事件，或查看狀態變更之前，您可以執行查詢。  

![直接 VM 對應概觀](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>檢視地圖，以從虛擬機器擴展集

若要存取 vm 的 Azure 監視器，直接從虛擬機器擴展集：

1. 在 Azure 入口網站中，選取**虛擬機器擴展集**。
2. 從清單中，選擇 [VM]。 然後在**監視**區段中，選擇**Insights （預覽）** 。  
3. 選取 [對應]  索引標籤。

地圖會視覺化在擴展集以及群組的相依性群組 節點中的所有執行個體。 展開的節點會列出擴展集中的執行個體。 您可以捲動 10 這些執行個體，一次。 

若要載入特定的執行個體的對應，請先選取該執行個體，在地圖上。 然後選取**省略符號**右邊的按鈕 （...），然後選擇**載入伺服器對應**。 在對應中出現，您會看到處理程序群組，並在指定的時間範圍內具有作用中網路連線的處理序。 

根據預設，對應會顯示過去 30 分鐘。 如果您想要查看過去的相依性的過往情形，您可以查詢過往時間範圍的最多一小時。 若要執行查詢時，使用**TimeRange**選取器。 例如，在發生事件，或查看狀態變更之前，您可以執行查詢。

![直接 VM 對應概觀](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>您也可以存取特定的執行個體，從地圖**執行個體**虛擬機器擴展集的檢視。 在 **設定**區段中，移至**執行個體** > **Insights （預覽）** 。

## <a name="view-a-map-from-azure-monitor"></a>檢視地圖，以從 Azure 監視器
在 Azure 監視器中，[地圖] 功能會提供您的 Vm 和其相依性的全域檢視。 若要存取 Azure 監視器中的 [地圖] 功能：

1. 在 Azure 入口網站中，選取 [監視]  。 
2. 在  **Insights**區段中，選擇**虛擬機器 （預覽）** 。
3. 選取 [對應]  索引標籤。

   ![多個 Vm 的 azure 監視器概觀圖](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

藉由選擇工作區**工作區**在頁面頂端的選取器。 如果您有多個 Log Analytics 工作區，請選擇工作區所啟用的解決方案，並可向其報告的 Vm。 

**群組**選取器會傳回訂用帳戶、 資源群組[電腦群組](../../azure-monitor/platform/computer-groups.md)，和虛擬機器擴展集的所選工作區相關的電腦。 您的選取項目僅適用於 [地圖] 功能，而且不會延續到效能或健全狀況。

根據預設，對應會顯示過去 30 分鐘。 如果您想要查看過去的相依性的過往情形，您可以查詢過往時間範圍的最多一小時。 若要執行查詢時，使用**TimeRange**選取器。 例如，在發生事件，或查看狀態變更之前，您可以執行查詢。  

## <a name="next-steps"></a>後續步驟
- 若要了解如何使用健全狀況的功能，請參閱[檢視 Azure VM 的健康狀態](vminsights-health.md)。 
- 識別瓶頸，請檢查效能，並了解您的虛擬機器的整體使用量，請參閱[檢視 Vm 的 Azure 監視器的效能狀態](vminsights-performance.md)。 
