---
title: 使用 Log Analytics 閘道連線電腦 |Microsoft Docs
description: 使用 Log Analytics 閘道來將資料傳送至 Azure 自動化和 Log Analytics 服務，它們沒有網際網路存取時，連接您的裝置和 Operations Manager 監視的電腦。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: magoedte
ms.openlocfilehash: b0b221a9fe6c6482e8759664c297dbd25d0ee776
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60396246"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>Azure 監視器中使用 Log Analytics 閘道沒有網際網路存取的電腦連接

>[!NOTE]
>為 Microsoft Operations Management Suite (OMS) 轉換為 Microsoft Azure 監視器中，會變更的術語。 這篇文章會將 Azure Log Analytics 閘道稱為的 OMS 閘道。 
>

本文說明如何使用 Log Analytics 閘道直接連接，或由 Operations Manager 監視的電腦已無法存取網際網路時，設定與 Azure 自動化和 Azure 監視器的通訊。 

Log Analytics 閘道是使用 HTTP CONNECT 命令支援 HTTP 通道的 HTTP 正向 proxy。 此閘道可以收集資料，並將它傳送至 Azure 自動化和 Log Analytics 工作區在 Azure 監視器中，代表未連線到網際網路的電腦。  

Log Analytics 閘道支援︰

* 報告到相同的四個 Log Analytics 工作區的代理程式，位於且與 Azure 自動化混合式 Runbook 背景工作角色設定。  
* Windows 電腦的 Microsoft Monitoring Agent 直接連線到 Azure 監視器中的 Log Analytics 工作區。
* 在其適用於 Linux 的 Log Analytics 代理程式直接連接到 Log Analytics 工作區，Azure 監視器中的 Linux 電腦。  
* System Center Operations Manager 2012 SP1 UR7、 Operations Manager 2012 R2 UR3，或在 Operations Manager 2016 或更新版本的管理群組與 Log Analytics 整合。  

某些 IT 安全性原則不允許之網路電腦的網際網路連線。 這些未連線的電腦可能是銷售點 (POS) 裝置或支援 IT 服務，例如的伺服器。 若要將這些裝置連線到 Azure 自動化或 Log Analytics 工作區，因此您可以管理和監視，將其設定為直接與 Log Analytics 閘道通訊。 Log Analytics 閘道可以接收組態資訊和其代表的資料轉送。 如果您的電腦設定與 Log Analytics 代理程式直接連接到 Log Analytics 工作區，與 Log Analytics 閘道改為通訊的電腦。  

Log Analytics 閘道將資料從代理程式服務直接傳輸。 它不會分析任何傳輸中的資料。

管理伺服器與 Log Analytics 整合的 Operations Manager 管理群組時，可以設定為連線至 Log Analytics 的閘道，以接收組態資訊和傳送收集的資料，根據您已啟用的解決方案.  Operations Manager 代理程式會將一些資料傳送至管理伺服器。 例如，代理程式可能會傳送 Operations Manager 警示、 組態評估資料、 執行個體的空間資料和容量資料。 其他大量資料，例如 Internet Information Services (IIS) 記錄檔、 效能資料，以及安全性事件，會直接傳送至 Log Analytics 閘道。 

如果監視未受信任的系統，在周邊網路或隔離的網路中部署一或多個 Operations Manager 閘道伺服器，這些伺服器無法與 Log Analytics 閘道通訊。  Operations Manager 閘道伺服器可以只向管理伺服器報告。  當 Operations Manager 管理群組設定為與 Log Analytics 閘道通訊時，proxy 組態資訊會自動發佈到每個代理程式管理的電腦設定為針對 Azure 監視器來收集記錄資料即使設定是空的。    

若要直接提供的高可用性連線或與閘道，透過 Log Analytics 工作區通訊的 Operations Management 群組使用網路負載平衡 (NLB) 以重新導向，以及將流量分散到多部閘道伺服器。 如此一來，如果某個閘道伺服器故障時，將流量重新導向至另一個可用的節點。  

執行 Log Analytics 閘道的電腦需要 Log Analytics Windows 代理程式，以找出閘道必須與通訊的服務端點。 代理程式也必須直接向相同的工作區回報的閘道，代理程式或 Operations Manager 閘道的管理群組設定。 此組態允許閘道和代理程式使用其指派的工作區通訊。

閘道可以是多重主目錄，最多四個工作區。 這是 Windows 代理程式支援的工作區的總數。  

每個代理程式必須有網路連線到閘道，以便代理程式可以將閘道的資料會自動轉換。 避免網域控制站上安裝閘道。

下圖顯示從直接代理程式，透過閘道，與 Azure 自動化和 Log Analytics 中的資料流程。 代理程式 proxy 組態必須符合 Log Analytics 閘道已設定的連接埠。  

![直接代理程式與服務通訊的圖表](./media/gateway/oms-omsgateway-agentdirectconnect.png)

下圖顯示從 Operations Manager 管理群組流向 Log Analytics 的資料流程。   

![使用 Log Analytics 的 Operations Manager 通訊的圖表](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>設定您的系統

指定要執行 Log Analytics 閘道的電腦必須具有下列設定：

* Windows 10、windows，Windows 8.1 或 Windows 7
* Windows Server 2016、 Windows Server 2012 R2、 Windows Server 2012、 Windows Server 2008 R2 或 Windows Server 2008
* Microsoft .NET Framework 4.5
* 至少 4 核心處理器和 8 GB 的記憶體 
* A [Log Analytics 代理程式的 Windows](agent-windows.md)已設定為透過閘道進行通訊的代理程式相同的工作區的報表

### <a name="language-availability"></a>提供的語言

Log Analytics 閘道有下列語言：

- 中文 (簡體)
- 中文 (繁體)
- 捷克文
- 荷蘭文
- 英文
- 法文
- 德文
- 匈牙利文
- 義大利文
- 日文
- 韓文
- 波蘭文
- 葡萄牙文 (巴西)
- 葡萄牙文 (葡萄牙)
- 俄文
- 西班牙文 (國際)

### <a name="supported-encryption-protocols"></a>支援的加密通訊協定
Log Analytics 閘道支援只傳輸層安全性 (TLS) 1.0、 1.1 及 1.2。  它不支援安全通訊端層 (SSL)。  若要確保資料傳輸到 Log Analytics 中的安全性，將閘道設定為使用至少 TLS 1.2。 舊版的 TLS 或 SSL 是易受攻擊。 雖然它們目前允許回溯相容性，請避免使用它們。  

如需其它資訊，請檢閱[使用 TLS 1.2 安全地傳送](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)。 

### <a name="supported-number-of-agent-connections"></a>支援的代理程式連線數目
下表會顯示大約多少的代理程式可以與閘道伺服器通訊。 支援根據每 6 秒上傳約 200 KB 資料的代理程式。 測試每一個代理程式，資料量是每天大約 2.7 GB。

|閘道器 |代理程式支援 （大約）|  
|--------|----------------------------------|  
|CPU：Intel Xeon 處理器 E5 2660 v3 \@ 2.6 GHz 2 核心<br> 記憶體：4 GB<br> 網路頻寬：1 Gbps| 600|  
|CPU：Intel Xeon 處理器 E5 2660 v3 \@ 2.6 GHz 4 核心<br> 記憶體：8 GB<br> 網路頻寬：1 Gbps| 1000|  

## <a name="download-the-log-analytics-gateway"></a>下載 Log Analytics 閘道

取得最新版本的 Log Analytics 閘道安裝程式檔案從[Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=54443)或 Azure 入口網站。

若要從 Azure 入口網站中取得 Log Analytics 閘道，請遵循下列步驟：

1. 瀏覽服務清單，然後選取 [Log Analytics]  。 
1. 選取工作區。
1. 在工作區刀鋒視窗中，選取 [一般]  下方的 [快速入門]  。 
1. 在 [選擇要連線至工作區的資料來源]  下方，選取 [電腦]  。
1. 在 **代理程式直接**刀鋒視窗中，選取**下載 Log Analytics 閘道**。
 
   ![螢幕擷取畫面的步驟以下載 Log Analytics 閘道](./media/gateway/download-gateway.png)

或 

1. 在您的工作區刀鋒視窗中，於 [設定]  下選取 [進階設定]  。
1. 移至**連接的來源** > **Windows 伺服器**，然後選取**下載 Log Analytics 閘道**。

## <a name="install-log-analytics-gateway-using-setup-wizard"></a>安裝使用安裝精靈的 Log Analytics 閘道

若要安裝閘道，使用安裝精靈，請遵循下列步驟。 

1. 從目的地資料夾中，按兩下 **Log Analytics gateway.msi**。
1. 在 [歡迎]  頁面上，選取 [下一步]  。

   ![在閘道安裝精靈的螢幕擷取畫面的 歡迎使用頁面](./media/gateway/gateway-wizard01.png)

1. 在 [**授權合約**頁面上，選取**我接受授權合約中的條款**以同意 Microsoft 軟體授權條款，然後選取**下一步]** 。
1. 在 [連接埠和 Proxy 位址]  頁面上：

   a. 輸入要用於閘道的 TCP 連接埠號碼。 安裝程式會使用此連接埠號碼，若要設定 Windows 防火牆上的輸入的規則。  預設值為 8080。
      連接埠號碼的有效範圍是 1 到 65535 之間。 如果輸入的值不在此範圍內，就會顯示錯誤訊息。

   b. 如果閘道安裝所在的伺服器需要透過 proxy 進行通訊時，輸入閘道需要連線的 proxy 位址。 例如，輸入 `http://myorgname.corp.contoso.com:80`。  如果您將此欄位保留空白，閘道將會嘗試直接連線到網際網路。  如果您的 Proxy 伺服器需要驗證，請輸入使用者名稱與密碼。

   c. 選取 [下一步]  。

   ![閘道的 proxy 組態的螢幕擷取畫面](./media/gateway/gateway-wizard02.png)

1. 如果您沒有啟用 Microsoft Update、 Microsoft Update 頁面隨即出現，和您可以選擇啟用它。 進行選取，然後選取**下一步**。 否則，請繼續下一個步驟。
1. 在 [**目的地資料夾**] 頁面上，保留預設資料夾 C:\Program Files\OMS Gateway 或輸入您要安裝閘道的位置。 然後，選取 [下一步]  。
1. 在 [準備安裝]  頁面上，選取 [安裝]  。 如果使用者帳戶控制會要求安裝權限，請選取**是**。
1. 安裝程式完成之後，請選取**完成**。 若要確認服務正在執行，請開啟 services.msc 嵌入式管理單元，並確認**OMS 閘道**會出現在服務清單和其狀態是**執行**。

   ![顯示螢幕擷取畫面的本機服務，OMS 閘道正在執行](./media/gateway/gateway-service.png)

## <a name="install-the-log-analytics-gateway-using-the-command-line"></a>使用命令列將 Log Analytics 閘道安裝
為閘道下載的檔案是支援從命令列或其他自動化的方法的無訊息安裝的 Windows Installer 套件。 如果您不熟悉 Windows 安裝程式的標準命令列選項，請參閱[命令列選項](https://docs.microsoft.com/windows/desktop/Msi/command-line-options)。   

下表會反白顯示安裝程式所支援的參數。

|參數| 注意|
|----------|------| 
|連接埠號碼 | 閘道上接聽的 TCP 連接埠號碼 |
|PROXY | Proxy 伺服器的 IP 位址 |
|INSTALLDIR | 指定的閘道軟體檔案的安裝目錄的完整的路徑 |
|USERNAME | 若要使用 proxy 伺服器進行驗證的使用者識別碼 |
|密碼 | 使用者識別碼來向 proxy 的密碼 |
|LicenseAccepted | 指定的值為**1**以確認您接受授權合約 |
|HASAUTH | 指定的值為**1**時指定使用者名稱/密碼參數 |
|HASPROXY | 指定的值為**1**指定的 IP 位址時**PROXY**參數 |

若要以無訊息方式安裝閘道，並設定特定的 proxy 位址、 連接埠號碼，輸入下列命令：

```dos
Msiexec.exe /I “oms gateway.msi” /qn PORTNUMBER=8080 PROXY=”10.80.2.200” HASPROXY=1 LicenseAccepted=1 
```

使用 /qn 命令列選項會隱藏安裝程式、 /qb 無訊息安裝期間顯示的設定。  

如果您需要提供認證來向 proxy，輸入下列命令：

```dos
Msiexec.exe /I “oms gateway.msi” /qn PORTNUMBER=8080 PROXY=”10.80.2.200” HASPROXY=1 HASAUTH=1 USERNAME=”<username>” PASSWORD=”<password>” LicenseAccepted=1 
```

安裝完成後，您可以確認設定是否接受 （exlcuding 使用者名稱和密碼） 使用下列 PowerShell cmdlet:

- **取得 OMSGatewayConfig** – 傳回閘道器設定為接聽 TCP 連接埠。
- **取得 OMSGatewayRelayProxy** – 傳回您將其設定為與通訊的 proxy 伺服器的 IP 位址。

## <a name="configure-network-load-balancing"></a>設定網路負載平衡 
您可以使用的網路負載平衡 (NLB) 使用 Microsoft 為高可用性閘道設定[網路負載平衡 (NLB)](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing)， [Azure Load Balancer](../../load-balancer/load-balancer-overview.md)，或以硬體為基礎的負載平衡器。 負載平衡器可藉由跨其節點將來自 Log Analytics 代理程式或 Operations Manager 管理伺服器的要求連線進行重新導向，來管理流量。 如果閘道伺服器故障，流量就會被重新導向到其他節點。

### <a name="microsoft-network-load-balancing"></a>Microsoft 網路負載平衡
若要了解如何設計和部署 Windows Server 2016 網路負載平衡叢集，請參閱[網路負載平衡](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing)。 下列步驟說明如何設定 Microsoft 網路負載平衡叢集。  

1. 使用系統管理帳戶登入屬於 NLB 叢集成員的 Windows 伺服器。  
2. 在 [伺服器管理員] 中開啟網路負載平衡管理員，然後依序按一下 [工具]  和 [網路負載平衡管理員]  。
3. 若要連線已安裝 Microsoft Monitoring Agent 的 Log Analytics 閘道伺服器，請用滑鼠右鍵按一下叢集的 IP 位址，然後按一下 [新增主機到叢集]  。 

    ![網路負載平衡管理員 – 新增主機到叢集](./media/gateway/nlb02.png)
 
4. 輸入您要連線之閘道伺服器的 IP 位址。 

    ![網路負載平衡管理員 – 新增主機到叢集：連線](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure Load Balancer
若要了解如何設計和部署 Azure Load Balancer，請參閱[什麼是 Azure Load Balancer？](../../load-balancer/load-balancer-overview.md)。 若要部署的基本負載平衡器，請遵循在此所述的步驟[快速入門](../../load-balancer/quickstart-create-basic-load-balancer-portal.md)排除一節所述的步驟**建立後端伺服器**。   

> [!NOTE]
> 設定 Azure Load Balancer 使用**基本 SKU**，需要 Azure 虛擬機器屬於可用性設定組。 若要深入了解可用性設定組，請參閱[管理 Azure 中 Windows 虛擬機器的可用性](../../virtual-machines/windows/manage-availability.md)。 若要將現有的虛擬機器新增至可用性設定組中，請參閱[設定 Azure Resource Manager VM 可用性設定組](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4)。
> 

建立負載平衡器之後後, 端集區需要建立，這將流量散發至一或多個閘道伺服器。 請依照下列快速入門一節中所述的步驟[建立負載平衡器資源](../../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-resources-for-the-load-balancer)。  

>[!NOTE]
>設定健康情況探查時，它應該設定為使用閘道伺服器的 TCP 連接埠。 健康情況探查以動態方式加入，或根據其健康情況檢查回應負載平衡器循環中移除閘道伺服器。 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>設定 Log Analytics 代理程式和 Operations Manager 管理群組
在本節中，您會看到如何設定 Log Analytics 閘道以與 Azure 自動化或 Log Analytics 通訊的直接連線 Log Analytics 代理程式、 Operations Manager 管理群組或 Azure 自動化混合式 Runbook 背景工作角色。  

### <a name="configure-a-standalone-log-analytics-agent"></a>設定獨立的 Log Analytics 代理程式
設定 Log Analytics 代理程式，請將 proxy 伺服器的值取代 Log Analytics 閘道伺服器和其通訊埠編號的 IP 位址。 如果您已部署負載平衡器後方的多部閘道伺服器，則 Log Analytics 代理程式 proxy 組態會是負載平衡器的虛擬 IP 位址。  

>[!NOTE]
>若要安裝閘道及直接連線到 Log Analytics 的 Windows 電腦的 Log Analytics 代理程式，請參閱[連線的 Windows 電腦連線到 Azure 中的 Log Analytics 服務](agent-windows.md)。 若要將 Linux 電腦的連線，請參閱[混合式環境中設定 Linux 電腦的 Log Analytics 代理程式](../../azure-monitor/learn/quick-collect-linux-computer.md)。 
>

閘道伺服器上安裝代理程式之後，將它設定為向工作區與閘道通訊的代理程式的工作區報告。 如果在閘道上未安裝 Log Analytics Windows 代理程式，會將事件 300 寫入 OMS 閘道事件記錄檔，指出必須安裝代理程式。 如果代理程式已安裝但未設定為透過它進行通訊的代理程式相同的工作區的報表，事件 105 寫入相同的記錄檔，表示在閘道上的代理程式必須設定為相同的工作區，作為代理程式回報該 communicate 與閘道。

完成組態之後，重新啟動 OMS 閘道服務，才能套用變更。 否則，閘道將會拒絕嘗試與 Log Analytics 通訊，且會報告事件 105 OMS 閘道事件記錄檔中的代理程式。 這將會發生當您新增或移除閘道伺服器上的代理程式設定的工作區。   

自動化混合式 Runbook 背景工作角色的相關資訊，請參閱[自動化您的資料中心或雲端中的資源使用混合式 Runbook 背景工作角色](../../automation/automation-hybrid-runbook-worker.md)。

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>設定 Operations Manager，其中所有的代理程式會使用相同的 proxy 伺服器
即使設定是空的，Operations Manager Proxy 組態仍會自動套用到所有向 Operations Manager 報告的代理程式。  

若要使用 OMS 閘道來支援 Operations Manager，您必須：

* Microsoft Monitoring Agent (8.0.10900.0 版本或更新版本) OMS 閘道伺服器上安裝並設定您的管理群組設定為向報告相同的 Log Analytics 工作區。
* 網際網路連線。 或者，OMS 閘道必須連線至連線到網際網路的 proxy 伺服器。

> [!NOTE]
> 如果您不指定閘道的任何值時，就會將空白值推送到所有代理程式。
>

如果您的 Operations Manager 管理群組向 Log Analytics 工作區中，第一次，您就不會看到 Operations 主控台中指定的管理群組的 proxy 組態的選項。 管理群組已向此服務時，才使用此選項。  

若要設定整合，請在執行 Operations 主控台的系統上，並在管理群組中的所有管理伺服器上使用 Netsh 更新系統 proxy 設定。 請遵循下列步驟：

1. 開啟提升權限的命令提示字元：

   a. 選取 **開始**，然後輸入**cmd**。  

   b. 以滑鼠右鍵按一下**命令提示字元**，然後選取**系統管理員身分執行**。  

1. 輸入下列命令：

   `netsh winhttp set proxy <proxy>:<port>`

完成之後與 Log Analytics 整合，請執行，變更移除`netsh winhttp reset proxy`。 接著，在 Operations 主控台中，使用**設定 proxy 伺服器**選項來指定 Log Analytics 閘道伺服器。 

1. 在 Operations Manager 主控台中，在**Operations Management Suite**，選取**連線**，然後選取**設定 Proxy 伺服器**。

   ![螢幕擷取畫面的 Operations Manager 中，顯示選取項目設定 Proxy 伺服器](./media/gateway/scom01.png)

1. 選取 **使用 proxy 伺服器來存取 Operations Management Suite** ，然後輸入 Log Analytics 閘道伺服器的 IP 位址或負載平衡器虛擬 IP 位址。 請小心前置詞開頭`http://`。

   ![螢幕擷取畫面的 Operations Manager 中，顯示 proxy 伺服器位址](./media/gateway/scom02.png)

1. 選取 [完成]  。 Operations Manager 管理群組現在已設定為透過閘道伺服器來與 Log Analytics 服務進行通訊。

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>設定 Operations Manager，其中特定代理程式使用 proxy 伺服器
對於大型或複雜的環境，建議您只有特定的伺服器 （或群組） 使用 Log Analytics 閘道伺服器。  對於這些伺服器，您無法更新 Operations Manager 代理程式直接因為這個值會覆寫管理群組的全域值。  相反地，覆寫用來推送這些值的規則。  

> [!NOTE] 
> 如果您想要允許您的環境中的多個 Log Analytics 閘道伺服器，請使用此設定的技巧。  例如，您可以要求特定的 Log Analytics 閘道伺服器，必須指定以區域為基礎。
>

若要設定特定伺服器或群組，以使用 Log Analytics 閘道伺服器： 

1. 開啟 Operations Manager 主控台，然後選取 [撰寫]  工作區。  
1. 在 [撰寫] 工作區中選取 [規則]  。 
1. 在 Operations Manager 工具列上選取**範圍** 按鈕。 如果此按鈕無法使用，請確定您已選取的物件，而不是資料夾，在**監視**窗格。 [範圍管理組件物件]  對話方塊會顯示一般會作為目標之類別、群組或物件的清單。 
1. 在 **尋求**欄位中，輸入**健全狀況服務**並從清單中選取。 選取 [確定]  。  
1. 搜尋**Advisor Proxy 設定規則**。 
1. 在 Operations Manager 工具列上選取**會覆寫**，然後指向**覆寫特定物件類別的規則：健全狀況服務**及從清單中選取的物件。  或建立自訂群組，其中包含您想要套用此覆寫的伺服器的健全狀況服務物件。 您的自訂群組，然後套用覆寫。
1. 在 **覆寫內容**對話方塊方塊中，加入核取記號**覆寫**旁邊的資料行**WebProxyAddress**參數。  在 **覆寫值**欄位中，輸入 Log Analytics 閘道伺服器的 URL。 請小心前置詞開頭`http://`。  

    >[!NOTE]
    > 您不需要啟用此規則。 它有已自動使用管理的 Microsoft System Center Advisor 安全參考覆寫管理組件中的覆寫目標的 Microsoft System Center Advisor 監控伺服器群組。
    > 

1. 選取管理組件從**選取目的地管理組件**清單，或選取建立新的未密封的管理組件**新增**。 
1. 完成時，請選取 [確定]  。 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>設定自動化混合式 Runbook 背景工作角色
如果您的環境中有自動化混合式 Runbook 背景工作角色，請遵循下列步驟為手動、 暫時性解決方式，來設定 OMS 閘道以支援背景工作角色。

若要遵循的步驟，在本節中，您需要知道自動化帳戶所在的 Azure 區域。 若要尋找該位置：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 選取 Azure 自動化服務。
1. 選取適當的 Azure 自動化帳戶。
1. 在 [位置]  底下檢視其區域。

   ![在 Azure 入口網站中的自動化帳戶位置的螢幕擷取畫面](./media/gateway/location.png)

您可以使用下表來識別每個位置的 URL。

**作業執行階段資料服務 Url**

| **位置** | **URL** |
| --- | --- |
| 美國中北部 |ncus-jobruntimedata-prod-su1.azure-automation.net |
| 西歐 |we-jobruntimedata-prod-su1.azure-automation.net |
| 美國中南部 |scus-jobruntimedata-prod-su1.azure-automation.net |
| 美國東部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| 加拿大中部 |cc-jobruntimedata-prod-su1.azure-automation.net |
| 北歐 |ne-jobruntimedata-prod-su1.azure-automation.net |
| 東南亞 |sea-jobruntimedata-prod-su1.azure-automation.net |
| 印度中部 |cid-jobruntimedata-prod-su1.azure-automation.net |
| 日本 |jpe-jobruntimedata-prod-su1.azure-automation.net |
| 澳大利亞 |ase-jobruntimedata-prod-su1.azure-automation.net |

**代理程式服務 URL**

| **位置** | **URL** |
| --- | --- |
| 美國中北部 |ncus-agentservice-prod-1.azure-automation.net |
| 西歐 |we-agentservice-prod-1.azure-automation.net |
| 美國中南部 |scus-agentservice-prod-1.azure-automation.net |
| 美國東部 2 |eus2-agentservice-prod-1.azure-automation.net |
| 加拿大中部 |cc-agentservice-prod-1.azure-automation.net |
| 北歐 |ne-agentservice-prod-1.azure-automation.net |
| 東南亞 |sea-agentservice-prod-1.azure-automation.net |
| 印度中部 |cid-agentservice-prod-1.azure-automation.net |
| 日本 |jpe-agentservice-prod-1.azure-automation.net |
| 澳大利亞 |ase-agentservice-prod-1.azure-automation.net |

如果您的電腦自動註冊為 Hybrid Runbook Worker，使用更新管理解決方案來管理修補程式。 請遵循下列步驟：

1. 將作業執行階段資料服務 URL 新增到 Log Analytics 閘道上的「允許的主機」清單。 例如：`Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. 使用以下 PowerShell Cmdlet 重新啟動 Log Analytics 閘道服務：`Restart-Service OMSGatewayService`

如果您的電腦已使用 Hybrid Runbook Worker 註冊 cmdlet 加入 Azure 自動化中，請遵循下列步驟：

1. 將代理程式服務註冊 URL 新增到 Log Analytics 閘道上的「允許的主機」清單。 例如：`Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. 將作業執行階段資料服務 URL 新增到 Log Analytics 閘道上的「允許的主機」清單。 例如：`Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. 重新啟動 Log Analytics 閘道服務。
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>PowerShell Cmdlet
您可以使用 cmdlet 來完成工作，以更新 Log Analytics 閘道的組態設定。 使用 cmdlet 之前，務必先：

1. 安裝 Log Analytics 閘道 （Microsoft Windows 安裝程式）。
1. 開啟 PowerShell 主控台視窗。
1. 輸入下列命令以匯入模組： `Import-Module OMSGateway`
1. 如果上一個步驟沒有發生錯誤，就表示模組已經成功匯入，且可以使用 Cmdlet。 輸入 `Get-Module OMSGateway`
1. 您可以使用 cmdlet 進行變更之後，重新啟動 OMS 閘道服務。

步驟 3 中的錯誤，表示模組並未匯入。 當 PowerShell 找不到模組時，可能會發生錯誤。 您可以在 OMS 閘道安裝路徑中尋找模組：*C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*。

| **Cmdlet** | **參數** | **說明** | **範例** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Key |取得服務的組態 |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |索引鍵 (必要) <br> 值 |變更服務的組態 |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |取得轉送 (上游) Proxy 的位址 |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |位址<br> 使用者名稱<br> 密碼 |設定轉送 (上游) Proxy 的位址 (與認證) |1.設定回覆 Proxy 和認證：<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2.設定不需要驗證的回覆 Proxy：`Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3.清除轉送 Proxy 設定︰<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |取得目前允許的主機 （僅限本機設定允許的主機，不會自動下載的允許主機） |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |主機 (必要) |將主機加到允許清單 |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |主機 (必要) |將主機從允許清單移除 |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |主體 (必要) |將用戶端憑證主體加到允許清單 |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |主體 (必要) |將用戶端憑證主體從允許清單移除 |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |取得目前允許的用戶端憑證主體 （僅限本機設定允許的主體，不會自動下載的允許的主體） |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>疑難排解
若要收集閘道所記錄的事件，您應該已安裝 Log Analytics 代理程式。

![Log Analytics 的閘道記錄檔中的 事件檢視器清單的螢幕擷取畫面](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>Log Analytics 閘道事件識別碼與描述

下表顯示的事件識別碼與 Log Analytics 閘道記錄事件的描述。

| **識別碼** | **說明** |
| --- | --- |
| 400 |沒有特定的 id。 任何應用程式錯誤 |
| 401 |錯誤組態。 比方說，listenPort ="text"，而不是整數。 |
| 402 |剖析 TLS 交握訊息時發生例外狀況。 |
| 403 |網路錯誤。 例如，無法連線到目標伺服器。 |
| 100 |一般資訊。 |
| 101 |服務已經啟動。 |
| 102 |服務已經停止。 |
| 103 |從用戶端收到 HTTP CONNECT 命令。 |
| 104 |不是 HTTP CONNECT 命令。 |
| 105 |目的地伺服器不在允許清單中，或目的地連接埠不是安全的 (443)。 <br> <br> 確定您的 OMS 閘道伺服器上的 MMA 代理程式和與 OMS 閘道通訊的代理程式已連線到相同的 Log Analytics 工作區。 |
| 105 |ERROR TcpConnection – 無效的用戶端憑證:CN = Gateway。 <br><br> 請確定您使用 OMS 閘道版本 1.0.395.0 或更新版本。 也請確定您的 OMS 閘道伺服器上的 MMA 代理程式和與 OMS 閘道通訊的代理程式連接到相同的 Log Analytics 工作區。 |
| 106 |不支援的 TLS/SSL 通訊協定版本。<br><br> Log Analytics 閘道支援 TLS 1.0、 1.1 和 1.2。 不支援 SSL。|
| 107 |已經驗證 TLS 工作階段。 |

### <a name="performance-counters-to-collect"></a>若要收集的效能計數器

以下表格顯示可供 Log Analytics 閘道使用的效能計數器。 使用效能監視器新增計數器。

| **名稱** | **說明** |
| --- | --- |
| Log Analytics 閘道/使用中用戶端連線 |使用中用戶端網路 (TCP) 連線數目 |
| Log Analytics 閘道/錯誤計數 |錯誤數目 |
| Log Analytics 閘道/已連線用戶端 |已連線用戶端數目 |
| Log Analytics 閘道/拒絕次數 |因為任何 TLS 驗證錯誤而被拒絕的次數 |

![Log Analytics 的螢幕擷取畫面閘道介面，顯示效能計數器](./media/gateway/counters.png)

## <a name="assistance"></a>協助
當您在 Azure 入口網站登入時，您可以取得 Log Analytics 閘道或任何其他 Azure 服務或功能的協助。
若要取得說明，請選取問號圖示入口網站，並選取右上角**新增支援要求**。 然後完成新的支援要求表單。

![新的支援要求的螢幕擷取畫面](./media/gateway/support.png)

## <a name="next-steps"></a>後續步驟
[新增資料來源](../../azure-monitor/platform/agent-data-sources.md)從連接的來源收集資料，並將資料儲存在您的 Log Analytics 工作區。