---
title: 了解 Azure 虛擬機器的健康情況 |Microsoft Docs
description: 本文說明如何了解 Vm 的虛擬機器和基礎作業系統使用 Azure 監視器的健全狀況。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/22/2019
ms.author: magoedte
ms.openlocfilehash: 2bf891f8cfecbb9e78e511dcee7ed1c61c170016
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340135"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>了解 Azure 虛擬機器的健全狀況

Azure 在監視空間中，包含特定角色或工作的服務，但它並不提供深入的健全狀況的作業系統 (Os) 裝載於 Azure 虛擬機器 (Vm) 上的檢視方塊。 雖然您可以使用 Azure 監視器，針對不同的條件，它具有不適用於建立模型，而代表的核心元件健全狀況或 Vm 的整體健全狀況。

藉由使用 Azure 監視器適用於 Vm 健康情況，您可以主動監視的可用性和效能的 Windows 或 Linux 客體 OS。 健康狀態功能會使用以模型代表重要元件和其關聯性，並提供準則，指定如何測量元件健全狀況，並偵測到狀況不良狀況時，會傳送警示。

檢視 Azure VM 和基礎作業系統的整體健全狀況狀態可以觀察到的兩個層面： 直接從 VM，或從 Azure 監視器資源群組中的所有 vm。

本文說明如何快速地評估、 調查和解決 Vm 健康狀態功能的 Azure 監視所偵測到的健全狀況問題。

如需設定適用於 VM 的 Azure 監視器相關資訊，請參閱[啟用適用於 VM 的 Azure 監視器](vminsights-enable-overview.md)。

## <a name="monitoring-configuration-details"></a>監視設定詳細資料

本節將概述預設健全狀況準則，來監視 Azure Windows 和 Linux Vm。 所有的健全狀況準則就會預先設定為它們偵測到狀況不良狀況時傳送警示。

### <a name="windows-vms"></a>Windows VM

- 記憶體的可用 MB
- 每次寫入 (邏輯磁碟) 的平均磁碟秒數
- 每次寫入 (磁碟) 的平均磁碟秒數
- 每次讀取的平均邏輯磁碟秒數
- 每次傳輸的平均邏輯磁碟秒數
- 每次讀取的平均磁碟秒數
- 每次傳輸的平均磁碟秒數
- 目前磁碟佇列長度 (邏輯磁碟)
- 目前磁碟佇列長度 (磁碟)
- 磁碟閒置時間百分比
- 檔案系統錯誤或損毀
- 邏輯磁碟可用空間 (%) 低
- 邏輯磁碟可用空間 (MB) 低
- 邏輯磁碟閒置時間百分比
- 每秒記憶體分頁數
- 已用來讀取的頻寬百分比
- 已使用的頻寬百分比總計
- 已用來寫入的頻寬百分比
- 使用中認可的記憶體百分比
- 磁碟閒置時間百分比
- DHCP 用戶端服務健康狀態
- DNS 用戶端服務健康狀態
- RPC 服務健康狀態
- 伺服器服務健康狀態
- CPU 使用率百分比總計
- Windows 事件記錄服務健康狀態
- Windows 防火牆服務健康狀態
- Windows 遠端管理服務健康狀態

### <a name="linux-vms"></a>Linux VM

- 磁碟平均Disk sec/Transfer
- 磁碟平均Disk sec/Read
- 磁碟平均Disk sec/Write
- 磁碟健康情況
- 邏輯磁碟可用空間
- 邏輯磁碟 % 可用空間
- 邏輯磁碟 % 可用閒置空間
- 網路介面卡健康情況
- 處理器時間百分比總計
- 作業系統記憶體的可用 MB

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

若要登入，請前往[Azure 入口網站](https://portal.azure.com)。

## <a name="introduction-to-azure-monitor-for-vms-health"></a>Azure 監視器的 Vm 健康狀態簡介

您使用單一 VM 或 Vm 群組的健全狀況功能之前，請務必了解資訊的呈現方式，以及視覺效果的表示。

### <a name="view-health-directly-from-a-vm"></a>直接從 VM 檢視健康情況

若要檢視 Azure VM 的健全狀況，請選取**Insights （預覽）** VM 的左窗格中。 在 VM insights 頁面中，**健全狀況** 索引標籤預設會開啟並顯示 VM 的健全狀況檢視。

![所選取 Azure 虛擬機器之適用於 VM 的 Azure 監視器健康情況概觀](./media/vminsights-health/vminsights-directvm-health.png)

在 **健全狀況**索引標籤之下**客體 VM 健康情況**下, 表顯示 VM 的健全狀況狀態，以及 VM 健康情況警示的總數引發狀況不良的元件。

如需詳細資訊，請參閱[警示](#alerts)。

為 VM 定義的健全狀態如下表所述：

|圖示 |健康情況狀態 |意義 |
|-----|-------------|---------------|
| |Healthy |VM 是在定義的健全狀況。 此狀態指出所偵測到任何問題時，VM 運作正常。 以父彙總套件監視，健全狀況彙總，並反映子系的最佳或最差狀況的狀態。|
| |重要 |不在定義的健康情況，表示其中的狀態，或偵測到較嚴重的問題。 若要恢復正常的功能，就必須解決這些問題。 以父彙總套件監視，健全狀況狀態彙總，並反映子系的最佳或最差狀況的狀態。|
| |警告 |狀態是已定義的健康狀態條件，其中一個表示警告狀態，另指出 「 重大 」 狀態 （可以設定三種健全狀況狀態臨界值），或當非關鍵的問題可能會導致嚴重的問題如果兩個閾值之間無法解析。 以父彙總套件監視，如果一個或多個子系處於警告狀態，父代會反映警告狀態。 如果有一個子系會處於重大狀態並處於警告狀態的另一個子系，父彙總套件會顯示為重大的健全狀況狀態。|
| |不明 |有幾個原因，無法計算狀態。 下一節會提供其他詳細資料和可能的解決方案。 |

未知的健康情況狀態可以是下列問題所造成：

- 已重新設定代理程式和工作區的報表不會再指定 Vm 的 Azure 監視器啟用時。 若要設定代理程式工作區，請參閱以報告[新增或移除工作區](../platform/agent-manage.md#adding-or-removing-a-workspace)。
- 已刪除 VM。
- Azure 監視器與 Vm 相關聯的工作區已刪除。 如果您有頂級支援權益，您可以復原的工作區。 移至[頂級](https://premier.microsoft.com/)開立支援要求。
- 已刪除的方案相依性。 若要重新啟用 ServiceMap 和 InfrastructureInsights 解決方案在您的 Log Analytics 工作區中，使用重新安裝這些解決方案[Azure Resource Manager 範本](vminsights-enable-at-scale-powershell.md#install-the-servicemap-and-infrastructureinsights-solutions)。 或者，您也可以使用 開始 索引標籤中找到的設定工作區 選項。
- 已關閉 VM。
- Azure VM 服務無法使用，或正在執行維護。
- 工作區[每日資料或保留限制](../platform/manage-cost-storage.md)已符合。

選取 **檢視健康情況診斷**開啟畫面，顯示為虛擬機器的所有元件相關聯的健全狀況準則、 狀態變更和其他偵測到的監視元件與 VM 相關的問題。

如需詳細資訊，請參閱[健康情況診斷](#health-diagnostics)。

在 [**健全狀況**] 區段中，資料表會顯示監視的健全狀況準則的效能元件的健全狀況彙總套件。 這些元件包括**CPU**，**記憶體**，**磁碟**，以及**網路**。 選取元件時，會開啟頁面，其中列出所有監視的條件及該元件的健全狀況狀態。

當您從執行 Windows 的 Azure VM 存取健康情況時前五個, 核心 Windows 服務的健全狀況狀態會顯示在下方**核心服務健全狀況**。 選取任何服務，就會開啟頁面，其中列出該元件，以及其健全狀況狀態監視的健全狀況準則。

選取 健全狀況準則的名稱會開啟 屬性 窗格。 在此窗格中，您可以檢閱組態的詳細資料，包括健全狀況準則有對應的 Azure 監視器警示。

如需詳細資訊，請參閱 <<c0> [ 健康情況診斷和使用健全狀況準則](#health-diagnostics)。

### <a name="aggregate-vm-perspective"></a>彙總的 VM 檢視方塊

若要檢視您的所有 Vm 的健全狀況集合的資源群組中，選取**Azure 監視器**從入口網站，然後選取瀏覽清單**虛擬機器 （預覽）** 。

![Azure 監視器中的 VM Insights 監視檢視](./media/vminsights-health/vminsights-aggregate-health.png)

在 **訂用帳戶**並**資源群組**下拉式清單選取適當的資源群組，其中包含 Vm 與群組相關，若要檢視報告的健康情況狀態。 您的選擇只適用於健全狀況的功能，並不會延續到**效能**或是**地圖**索引標籤。

**健全狀況** 索引標籤提供下列資訊：

* 多少個 Vm 都處於重大或狀況不良的狀態，與多少均狀況良好，或未提交資料 （稱為未知狀態）。
* 哪些和多少 Vm 的作業系統所回報的狀況不良狀態。
* 多少個 Vm 的狀況不良的因為偵測到的處理器、 磁碟、 記憶體或網路介面卡，依健全狀況狀態分類的問題。
* 多少個 Vm 的狀況不良的因為偵測到與核心 OS 服務，依健全狀況狀態分類的問題。

在 **健全狀況**索引標籤上，您可以識別所監視的 VM，並檢閱警示詳細資料和相關聯的知識文章的健全狀況準則偵測到重大問題。 這些文章可協助診斷並修復問題。 選取任何嚴重性以開啟依照該嚴重性篩選的 [所有警示](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) 頁面。

[依作業系統的 VM 分佈]  清單會顯示依 Windows 版本或 Linux 發行版本列出的 VM 及其版本。 在每個作業系統 類別中，Vm 會進一步細分為基礎的 VM 健康情況。

![VM Insights 虛擬機器分佈檢視方塊](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

選取任何資料行，包括**VM 計數**，**重大**，**警告**，**狀況良好**，或**未知**。 檢視中的篩選結果清單**虛擬機器**符合選取的資料行的頁面。

例如，若要檢閱執行 Red Hat Enterprise Linux 版本 7.5 的所有 Vm，請選取**VM 計數**該作業系統，而且它的值將會列出符合該篩選條件和其目前的健全狀況狀態的 Vm。

![Red Hat Linux VM 的範例彙總](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)

在 **虛擬機器**頁面上，如果您選取的資料行底下的 VM 名稱**VM 名稱**，您將您導向至**VM 執行個體**頁面。 此頁面提供警示和健康狀態準則問題影響選取之的 VM 的更多詳細資料。 選取篩選的健全狀況狀態詳細資料**健全狀況狀態**頁面，以查看哪些元件的狀況不良的左上角的圖示。 您也可以檢視產生的狀況不良的元件，依警示嚴重性分類的 VM 健康狀態警示。

從**VM list**檢視中，選取以開啟 VM 的名稱**健全狀況**頁面上的 vm，同樣地，如果您選取**Insights （預覽）** 從直接的 VM。

![所選取 Azure 虛擬機器的 VM Insights](./media/vminsights-health/vminsights-directvm-health.png)

**Insights （預覽）** 頁面會顯示彙總健全狀況狀態的 VM 和警示。 此健全狀況狀態是依嚴重性，表示 VM 健康情況警示引發時的健全狀況狀態從狀況良好變更為狀況不良，根據準則來分類。 選取**關鍵條件中的 Vm**開啟頁面的一或多個 Vm 處於重大的健全狀況狀態的清單。

選取 健全狀況狀態，其中一個 Vm 節目**健康情況診斷**VM 的檢視。 在此檢視中，您可以判斷哪一個健全狀況準則反映的健全狀況狀態問題。 當**健康情況診斷**頁面開啟時，它會顯示 VM 的所有元件和其相關聯的健全狀況準則與目前的健全狀況狀態。

如需詳細資訊，請參閱[健康情況診斷](#health-diagnostics)。

選取 [檢視所有健康情況準則]  ，即會開啟一個頁面，顯示適用於此功能的所有健康情況準則清單。 您可以根據下列選項進一步篩選資訊：

* **型別**。 有三種類型的健全狀況準則，以評估條件，並彙總受監視的 VM 的整體健全狀況狀態：
    - **單位**。 測量在某種程度的 VM。 執行指令碼來執行綜合交易，或是監看表示發生錯誤的事件，此健全狀況準則類型可能會檢查效能計數器以判斷元件的效能。 根據預設，篩選是設單位。
    - **相依性**。 提供不同的實體之間的健全狀況彙總套件。 此健全狀況準則可讓實體相依於另一種類型，它相依於成功的作業之實體的健全狀況的健全狀況。
    - **彙總**。 提供類似的健全狀況準則的合併健全狀況狀態。 單位和相依性的健康狀態準則通常被設定在彙總的健康狀態準則。 除了可針對目標設為實體的許多不同健康情況準則提供更好的一般組織方式，彙總健康情況準則還能針對不同的實體分類提供唯一的健康狀態。

* **類別目錄**。 用來分組類似的準則，針對報告用途的健全狀況準則類型。 這些類別都是**可用性**並**效能**。

若要查看哪些執行個體的狀況不良，選取 下的一個值**狀況不良的元件**資料行。 在此頁面中，資料表會列出處於重大的健全狀況狀態的元件。

## <a name="health-diagnostics"></a>健康情況診斷

**健康情況診斷**頁面可讓您以視覺化方式檢視 VM 的健全狀況模型。 此頁面會列出 VM 的所有元件、 相關聯的健全狀況準則、 狀態變更及其他重大問題所監視的元件與 VM 相關識別。

![適用於 VM 的健康情況診斷頁面範例](./media/vminsights-health/health-diagnostics-page-01.png)

使用下列方法，以啟動健康情況診斷：

* 藉由從彙總 VM 觀點來看，Azure 監視器中的所有 Vm 的彙總套件健全狀況狀態：

    1. 在 [**健全狀況**頁面上，選取的圖示**重大**，**警告**，**狀況良好**，或**未知**健全狀況狀態] 區段下的**客體 VM 健康情況**。
    2. 請移至頁面，其中列出符合該篩選的類別的所有 Vm。
    3. 選取中的值**健全狀況狀態**開啟範圍設定為該 VM 的健康情況診斷的資料行。

* 從彙總 VM 觀點來看，Azure 監視器中的 OS。 在 [VM 分佈]  下方，選取任一個資料行值將會開啟 [虛擬機器]  頁面，並在表格中傳回符合已篩選分類的清單。 選取下方的值**健全狀況狀態**資料行選取的 VM 開啟健康情況診斷。
 
* 從客體 VM 上 Vm 的 Azure 監視器**健全狀況**索引標籤上，選取**檢視健康情況診斷**。

健康情況診斷會組織成兩個類別目錄的健康情況資訊： 可用性和效能。
 
不含篩選條件的兩個分類，您可以檢視所有元件，例如邏輯磁碟、 CPU，並依此類推，所定義的健康狀態準則。 這些檢視可全面檢視的準則，或透過其中一個類別目錄的篩選結果，當您選取**可用性**或是**效能**。

此外旁, 看到的條件類別目錄**健全狀況準則**資料行。 如果條件不符合選取的類別中，訊息，指出**沒有可供所選類別的健全狀況準則**會出現在**健全狀況準則**資料行。

健全狀況準則的狀態是由四種類型的其中一個定義：**關鍵**，**警告**，**狀況良好**，和**未知**。 前三個是可設定的這表示您可以修改直接在監視的閾值**健全狀況準則**組態窗格中。 這也可使用 Azure 監視器 REST API[更新監視作業](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)。 **未知**不可設定，而針對特定案例保留。

**健康情況診斷**頁面有三個主要區段：

* 元件模型
* 健康情況準則
* 狀態變更

![[健康情況診斷] 頁面的區段](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>元件模型

中的最左邊資料行**健康情況診斷**頁**元件模型**。 所有的元件，也就是與 VM 相關聯，會顯示在其目前的健全狀況狀態以及此資料行。

在下列範例中，會探索到的元件**磁碟**， **Logical Disk**，**處理器**，**記憶體**，和**作業系統**。 將在此資料行中探索及顯示這些元件的多個執行個體。

比方說下, 圖顯示 VM 具有兩個邏輯磁碟執行個體**c:** 並**d:** ，它會處於狀況良好的狀態：

![[健康情況診斷] 中所呈現的範例元件模型](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>健康情況準則

Center 中的資料行 [健康情況診斷] 頁面會**健全狀況準則**。 針對 VM 定義的健康情況模型會以階層樹狀結構來顯示。 VM 的健康情況模型會由單位和彙總健全準則所組成。

![[健康情況診斷] 中所呈現的範例健康情況準則](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

健康狀態準則測量受監視的執行個體，這可能是臨界值，狀態的實體，並依此類推的健全狀況。 健康狀態準則會有兩個或三個可設定的健全狀況狀態臨界值，如先前所述。 在任何時間點，只有其中一個可能的狀態可以是健康狀態準則。

健全狀況模型會定義用來決定的整體目標健全狀況和元件的目標準則。 準則的階層架構所示**健全狀況準則**區段**健康情況診斷**頁面。

健全狀況彙總原則屬於彙總健全狀況準則的設定 (預設設定為**最差的**)。 您可以找到一組預設的執行過程中這項功能的健全狀況準則[監視組態詳細資料](#monitoring-configuration-details)一節。

您也可以使用 Azure 監視器 REST API[資源的監視執行個體清單](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource)取得一份所有健全狀況準則。 此準則包括 Azure VM 資源上執行的組態詳細資料。

**單位**健全狀況準則類型可以有選取右邊的省略符號連結來修改其組態。 選取 **顯示詳細資料**以開啟 設定 窗格。

![設定健康情況準則範例](./media/vminsights-health/health-diagnostics-vm-example-02.png)

在 [組態] 窗格所選的健全狀況準則，如果您使用範例**平均磁碟秒 Per Write**，臨界值可以設定具有不同的數值。 雙狀態監視器，這表示它可以變更只能從很**狀況良好**要**警告**。

其他的健全狀況準則有時會使用三種狀態，您可以在其中設定警告和重大的健全狀況狀態閾值的值。 您也可以使用 Azure 監視器 REST API 來修改閾值[監視器組態](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)。

>[!NOTE]
>將健全狀況準則組態變更套用至一個執行個體適用於所有受監視的執行個體。 比方說，如果您選取 **-1 d： 的磁碟**，然後修改**平均磁碟秒 Per Write**臨界值，套用至所有執行個體，探索並監視 VM 上的變更。


![設定單位監視器範例的健康情況準則](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

如果您想要深入了解健全狀況準則，我們包含了可協助您識別問題、 原因和解決方案的知識文章。 選取 **檢視資訊**在頁面上，若要查看相關的知識文章。

若要檢閱使用 Azure 監視器包含 Vm 健康狀態的所有知識文章，請參閱[Azure 監視器的健全狀況知識文件](https://docs.microsoft.com/azure/monitoring/infrastructure-health/)。

### <a name="state-changes"></a>狀態變更

最右側資料行**健康情況診斷**頁**狀態變更**。 此資料行清單中選取之健全狀況條件相關聯的所有狀態變更**健全狀況準則**區段中或如果 VM 從 選取 VM 的狀態變更**元件模型**或**健全狀況準則**資料表資料行。

![[健康情況診斷] 中所呈現的範例狀態變更](./media/vminsights-health/health-diagnostics-page-statechanges.png)

下一節顯示健全狀況準則狀態及相關聯的時間。 這項資訊會顯示頂端的資料行的最新狀態。

### <a name="association-of-component-model-health-criteria-and-state-changes-columns"></a>元件模型、 健全狀況準則和狀態變更的資料行的關聯

這三個資料行彼此交互連結。 當您選取中的執行個體**元件模型**資料行**健全狀況準則**資料行的篩選至該元件檢視。 同樣地，**狀態變更**會根據所選的健全狀況準則 」 來更新 「 資料行。

![選取受監視的執行個體和結果範例](./media/vminsights-health/health-diagnostics-vm-example-01.png)

比方說，如果您選取*磁碟-1 的 d:* 下方的清單**元件模型**，**健全狀況準則**篩選至*磁碟-1 D:* ，以及**狀態變更**顯示狀態變更為基礎的可用性*磁碟-1 的 d:* 。

若要查看更新的健康狀態，您可以重新整理健康情況診斷頁面選取**重新整理**連結。 如果要基於預先定義的輪詢間隔來更新健康情況準則的健康狀態，此工作可讓您避免等候，並反映最新的健康狀態。 **健全狀況準則狀態**是可讓您根據所選的健全狀況狀態的結果的範圍篩選器：狀況良好、 警告、 重大、 未知，和 All。 **上次更新**右上角的時間表示上次更新健康情況診斷頁面的時間。

## <a name="alerts"></a>警示

與整合的 azure Vm 的健全狀況監視器[Azure 警示](../../azure-monitor/platform/alerts-overview.md)。 它會產生警示時預先定義的準則，當偵測到，將變更從狀況良好的狀態為狀況不良狀態。 警示是依嚴重性，嚴重性 0 到嚴重性 4 與嚴重性 0 為最高的層級的分類。

警示不相關聯動作群組已觸發警示時通知您。 訂用帳戶擁有者必須遵循的步驟來設定通知[設定警示](#configure-alerts)一節。

依嚴重性分類的 VM 健康情況警示的總數位於**健全狀況**下方的儀表板**警示**一節。 當您選取警示總數或對應到嚴重性層級的數字時，[警示]  頁面隨即開啟並列出所有符合您選取項目的警示。

例如，如果您選取對應至的資料列**嚴重性層級 1**，您會看到下列檢視：

![所有嚴重性層級 1 的警示範例](./media/vminsights-health/vminsights-sev1-alerts-01.png)

**所有警示**頁面不限定為顯示比對您選取的警示。 它也依篩選**資源類型**顯示只引發由 VM 資源健康狀態警示。 此格式會反映在警示清單中，資料行底下**目標資源**，其中它會顯示 Azure VM 警示引發時符合狀況不良的條件。

從其他的資源類型或服務的警示不適合包含在此檢視中。 這些警示包含記錄檔查詢或計量的警示，您通常會從 Azure 監視器的預設檢視為基礎的記錄警示[所有警示](../../azure-monitor/platform/alerts-overview.md#all-alerts-page)頁面。

您可以篩選此檢視在頁面頂端的下拉式清單功能表中選取值。

|欄 |描述 |
|-------|------------|
|訂用帳戶 |選取 Azure 訂用帳戶。 檢視僅會包含所選訂用帳戶中出現的警示。 |
|資源群組 |選取單一資源群組。 檢視僅會包含所選資源群組中具有目標的警示。 |
|資源類型 |選取一個或多個資源類型。 依預設只會選取目標**虛擬機器**的警示，並將其包含在此檢視中。 指定資源群組之後，才可使用此欄。 |
|資源 |選取資源。 只有以該資源作為目標的警示才會包含在檢視中。 只有在已指定資源類型之後，才使用這個資料行。 |
|Severity |選取警示嚴重性，或選取 [所有]  以包含所有嚴重性的警示。 |
|監視條件 |如果它們已引發或如果條件為不再使用中，系統已解決，請選取監視條件來篩選警示。 或者，選取**所有**来包含的所有條件的警示。 |
|警示狀態 |選取警示的狀態，**的新**，**通知**， **Closed**，或**所有**来包含的所有狀態的警示。 |
|監視器服務 |選取服務，或選取 [所有]  以包含所有服務。 這項功能支援只能從 VM Insights 警示。|
|時間範圍| 只有在所選時間範圍內引發的警示才會包含在檢視中。 支援的值為過去 1 小時、過去 24 小時、過去 7 天和過去 30 天。 |

當您選取警示時**警示詳細資料**頁面隨即顯示。 此頁面提供警示的詳細資料，並可讓您變更其狀態。

若要深入了解如何管理警示，請參閱[使用 Azure 監視器來建立、檢視及管理警示](../../azure-monitor/platform/alerts-metric.md)。

>[!NOTE]
>建立健全狀況準則或修改現有健全狀況的警示規則在 Azure 監視器中從入口網站為基礎的新警示目前不支援。


![所選取警示的 [警示詳細資料] 窗格](./media/vminsights-health/alert-details-pane-01.png)

您可以變更一或多個警示的警示狀態，方法是選取它們，然後選取**變更狀態**從**所有警示**左上角的頁面。 選取其中一個狀態上**變更警示狀態** 窗格中，新增變更的描述**註解**欄位，然後再選取**Ok**來認可變更。 當驗證資訊，並套用變更時，底下追蹤進度**通知**功能表中。

### <a name="configure-alerts"></a>設定警示
您無法從 Azure 入口網站來管理特定的警示管理工作。 必須使用執行這些工作[Azure 監視器 REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)。 具體而言：

- 啟用或停用健全狀況準則的警示
- 設定健全狀況準則的警示通知

每個範例會使用[ARMClient](https://github.com/projectkudu/armclient) Windows 電腦上。 如果您不熟悉此方法，請參閱[使用 ARMClient](../platform/rest-api-walkthrough.md#use-armclient)。

#### <a name="enable-or-disable-an-alert-rule"></a>啟用或停用警示規則

啟用或停用特定的健全狀況準則，此屬性的警示**alertGeneration**必須使用的值修改**停用**或是**已啟用**。

若要找出*monitorId*特定的健全狀況準則，如下列範例示範如何查詢該準則的值**LogicalDisk\Avg Disk 秒 Per Transfer**:

1. 在終端機視窗中，輸入 armclient.exe login  。 如此一來，會提示您登入 Azure。

2. 輸入下列命令來擷取特定 VM 上使用中所有健康狀態準則，並識別的值*monitorId*屬性：

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview”
    ```

    下列範例顯示的輸出*armclient GET*命令。 請記下的值*MonitorId*。 這是必要值下一個步驟，就必須在此指定健康條件的識別碼，並修改其屬性，來建立警示。

    ```
    "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/monitors/ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "name": "ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "type": "Microsoft.WorkloadMonitor/virtualMachines/monitors"
    },
    {
      "properties": {
        "description": "Monitor the performance counter LogicalDisk\\Avg Disk Sec Per Transfer",
        "monitorId": "Microsoft_LogicalDisk_AvgDiskSecPerTransfer",
        "monitorName": "Microsoft.LogicalDisk.AvgDiskSecPerTransfer",
        "monitorDisplayName": "Average Logical Disk Seconds Per Transfer",
        "parentMonitorName": null,
        "parentMonitorDisplayName": null,
        "monitorType": "Unit",
        "monitorCategory": "PerformanceHealth",
        "componentTypeId": "LogicalDisk",
        "componentTypeName": "LogicalDisk",
        "componentTypeDisplayName": "Logical Disk",
        "monitorState": "Enabled",
        "criteria": [
          {
            "healthState": "Warning",
            "comparisonOperator": "GreaterThan",
            "threshold": 0.1
          }
        ],
        "alertGeneration": "Enabled",
        "frequency": 1,
        "lookbackDuration": 17,
        "documentationURL": "https://aka.ms/Ahcs1r",
        "configurable": true,
        "signalType": "Metrics",
        "signalName": "VMHealth_Avg. Logical Disk sec/Transfer"
      },
      "etag": null,
    ```

3. 輸入下列命令來修改*alertGeneration*屬性：

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. 輸入在步驟 2 中用於驗證的屬性值設為 GET 命令**已停用**。

#### <a name="associate-an-action-group-with-health-criteria"></a>健全狀況準則相關聯的動作群組

Azure Vm 的健全狀況監視器的狀況不赤滌準則產生警示時，支援 SMS 和電子郵件通知。 若要設定通知，請注意傳送簡訊或電子郵件通知設定的動作群組的名稱。

>[!NOTE]
>針對每個受監視的 VM，您想要接收的通知，就必須執行此動作。 它不會套用至資源群組中的所有 Vm。

1. 在終端機視窗中，輸入*armclient.exe login*。 如此一來，會提示您登入 Azure。

2. 輸入下列命令來建立動作群組關聯的警示規則：
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}"
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. 若要確認屬性的值**actionGroupResourceIds**已成功更新，請輸入下列命令：

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    輸出看起來應該像下列準則：
    
    ```
    {
      "value": [
        {
          "properties": {
            "actionGroupResourceIds": [
              "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourceGroups/Lab/providers/microsoft.insights/actionGroups/Lab-IT%20Ops%20Notify"
            ]
          },
          "etag": null,
          "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/notificationSettings/default",
          "name": "notificationSettings/default",
          "type": "Microsoft.WorkloadMonitor/virtualMachines/notificationSettings"
        }
      ],
      "nextLink": null
    }
    ```

## <a name="next-steps"></a>後續步驟

- 若要識別限制和整體的 VM 效能，請參閱[檢視 Azure 虛擬機器效能](vminsights-performance.md)。
- 若要深入了解已探索到的應用程式相依性，請參閱[檢視 Vm 對應的 Azure 監視器](vminsights-maps.md)。
