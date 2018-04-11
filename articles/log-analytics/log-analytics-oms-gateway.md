---
title: 使用 OMS 閘道連線電腦 | Microsoft Docs
description: 將您裝置與受 Operations Manager 監視的電腦與 OMS 閘道連線，以在它們沒有網際網路存取時傳送資料給 Azure 自動化和 Log Analytics 服務。
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: magoedte
ms.openlocfilehash: 66e5444f5346a44cfc8a43cf2b43dbaeacffedc9
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2018
---
# <a name="connect-computers-without-internet-access-by-using-the-oms-gateway"></a>在無法存取網際網路的情況下使用 OMS 閘道連線電腦
本文件說明如何在直接連線的電腦或 Operations Manager 所監視的電腦無法存取網際網路時，設定以 OMS 閘道來與 Azure 自動化和 Log Analytics 通訊。 OMS 閘道是一個 HTTP 正向 Proxy，其使用 HTTP CONNECT 命令支援 HTTP 通道。 其可代表無法存取網際網路的電腦來收集資料，並傳送給 Azure 自動化和 Log Analytics。  

OMS 閘道支援︰

* Azure 自動化混合式 Runbook 背景工作  
* 直接連線到 Log Analytics 工作區、具有 Microsoft Monitoring Agent 的 Windows 電腦
* 直接連線到 Log Analytics 工作區、具有適用於 Linux 的 OMS 代理程式的 Linux 電腦  
* System Center Operations Manager 2012 SP1 (含 UR7)、Operations Manager 2012 R2 (含 UR3)、Operations Manager 2016 和與 Log Analytics 整合的 Operations Manager 1801 版管  

如果您的 IT 安全性原則不允許網路上的某些電腦連線到網際網路 (例如，銷售點裝置或支援 IT 服務的伺服器)，但您需要將這些電腦連線至 Azure 自動化或 Log Analytics 從而對這些電腦進行管理和監視，則可以將它們設定為直接與 OMS 閘道通訊。

 如果您為這些電腦設定了 OMS 代理程式以直接連線到 Log Analytics 工作區，所有電腦會改為與 OMS 閘道通訊。 OMS 閘道會將資料直接從代理程式傳輸到服務。 其不會分析傳輸中的任何資料。

當 Operations Manager 管理群組與 Log Analytics 整合時，您可以將管理伺服器設定為連線至 OMS 閘道，以接收組態資訊和傳送收集到的資料。 Operations Manager 代理程式會將一些資料 (例如 Operations Manager 警示、組態評估、執行個體空間，以及容量資料) 傳送到管理伺服器。 其他大量資料 (例如 IIS 記錄、效能資訊及安全性事件) 會直接傳送到 OMS 閘道。  

如果您使用一或多個部署在 DMZ 或其他隔離網路的 Operations Manager 閘道伺服器來監視未受信任的系統，這些伺服器無法與 OMS 閘道通訊。 Operations Manager 閘道伺服器只能向管理伺服器報告。 

當您將 Operations Manager 管理群組設定為與 OMS 閘道通訊時，Proxy 組態資訊會自動發佈到每個受代理程式管理、並已設定為會收集資料以供 Log Analytics 使用的電腦，即使設定是空的也會如此。    

若要為透過 OMS 閘道與 Log Analytics 通訊的直接連線群組或 Operations Management 群組提供高可用性，您可以使用網路負載平衡將流量重新導向並分散到多個 OMS 閘道伺服器。 如果某個閘道伺服器故障，系統就會將流量重新導向到其他可用節點。  

建議您將 OMS 代理程式安裝在執行 OMS 閘道軟體的電腦上，以對電腦進行監視，並分析效能或事件資料。 此外，該代理程式也可協助 OMS 閘道識別其需要通訊的服務端點。

所有代理程式都必須能夠透過網路連線到其閘道，才能自動將資料傳輸到閘道，以及從閘道傳輸資料。 不建議將閘道安裝在網域控制站上。

下圖顯示使用閘道伺服器從直接代理程式流向 Azure 自動化和 Log Analytics 的資料流程。 代理程式 Proxy 組態所使用的連接埠，必須和 OMS 閘道用來與服務通訊的連接埠相同。  

![代理程式直接與服務通訊的圖表](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

下圖顯示從 Operations Manager 管理群組流向 Log Analytics 的資料流程。   

![Operations Manager 與 Log Analytics 通訊的圖表](./media/log-analytics-oms-gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="prerequisites"></a>先決條件

當您指定要執行 OMS 閘道的電腦時，請確定這台電腦具有下列元件︰

* Windows 10、Windows 8.1、Windows 7
* Windows Server 2016、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 及 Windows Server 2008
* .NET Framework 4.5
* 至少為 4 核心處理器和 8 GB 記憶體 

### <a name="language-availability"></a>提供的語言

OMS 閘道有下列語言版本︰

- 中文 (簡體)
- 中文 (繁體)
- 捷克文
- 荷蘭文
- English
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
OMS 閘道僅支援傳輸層安全性 (TLS) 1.0、1.1 及 1.2。  不支援安全通訊端層 (SSL)。

### <a name="supported-number-of-agent-connections"></a>支援的代理程式連線數目
下表指出支援與閘道伺服器通訊的代理程式數目。 此支援的根據是每 6 秒會上傳約 200 KB 資料的代理程式。 每個代理程式每天所測試的資料量約為 2.7 GB。

|閘道器 |所支援代理程式的大略數目|  
|--------|----------------------------------|  
|- CPU：Intel XEON CPU E5-2660 v3 @ 2.6 GHz 2 核心<br> - 記憶體：4 GB<br> - 網路頻寬：1 Gbps| 600|  
|- CPU：Intel XEON CPU E5-2660 v3 @ 2.6 GHz 4 核心<br> - 記憶體：8 GB<br> - 網路頻寬：1 Gbps| 1000|  

## <a name="download-the-oms-gateway"></a>下載 OMS 閘道

有兩種方法可以取得最新版的 OMS 閘道安裝程式檔案。

1. 從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=54443)進行下載。

2. 從 Azure 入口網站進行下載。 登入 Azure 入口網站之後，執行下列步驟：  

   1. 瀏覽服務清單，然後選取 [Log Analytics]。  
   2. 選取工作區。
   3. 在工作區刀鋒視窗中，選取 [一般] 下方的 [快速入門]。
   4. 在 [選擇要連線至工作區的資料來源] 下方，選取 [電腦]。
   5. 在 [直接代理程式] 面板中，選取 [下載 OMS 閘道]。
   
    ![下載 OMS 閘道](./media/log-analytics-oms-gateway/download-gateway.png)

-- 或 -- 

   1. 在您的工作區刀鋒視窗中，於 [設定] 下選取 [進階設定]。
   2. 選取 [連線的來源] > [Windows 伺服器]。 然後選取 [下載 OMS 閘道]。

## <a name="install-the-oms-gateway"></a>安裝 OMS 閘道

若要安裝閘道，請採取下列步驟。 如果您已安裝舊版 (先前稱為「Log Analytics 轉寄站」)，該舊版會升級為此版本。  

1. 在目的地資料夾中，選取 [OMS Gateway.msi]。
2. 在 [歡迎] 頁面上，選取 [下一步]。

 ![閘道設定精靈](./media/log-analytics-oms-gateway/gateway-wizard01.png)
  
3. 在 [授權合約] 頁面上，選取 [我接受授權合約中的條款]。 然後，選取 [下一步]。

4. 在 [連接埠和 Proxy 位址] 頁面上：

   a. 輸入要用於閘道的 TCP 連接埠號碼。 安裝程式會使用此連接埠號碼在 Windows 防火牆上設定輸入規則。 預設值為 8080。 連接埠號碼的有效範圍是 1 到 65535。 如果輸入的值不在此範圍內，就會顯示錯誤訊息。

   b. 如果閘道安裝所在的伺服器需要透過 Proxy 進行通訊，您也可以選擇輸入閘道需要連線的 Proxy。 例如 `http://myorgname.corp.contoso.com:80`，如下列螢幕擷取畫面所示。 如果讓此欄位空白，閘道會嘗試直接連線到網際網路。  如果您的 Proxy 伺服器需要驗證，請輸入使用者名稱與密碼。
   
    ![閘道精靈 Proxy 組態](./media/log-analytics-oms-gateway/gateway-wizard02.png)

4. 選取 [下一步] 。

5. 如果您未啟用 Microsoft Update，系統會顯示 Microsoft Update 頁面，然後您可選擇加以啟用。 選擇想要的選項，然後選取 [下一步]。 否則，請繼續下一個步驟。

6. 在 [目的地資料夾] 頁面上，讓預設資料夾維持是 **C:\Program Files\OMS Gateway**，或輸入您想要安裝 OMS 閘道的位置。 然後，選取 [下一步]。

7. 在 [準備安裝] 頁面上，選取 [安裝]。 系統可能會顯示 [使用者帳戶控制] 來要求安裝權限。 如果您收到提供權限的要求，請選取 [是]。

8. 設定完成之後，請選取 [完成]。 您可以透過開啟 services.msc 嵌入式管理單元來確認服務正在執行，並確定服務清單中有顯示 [OMS 閘道]，且其狀態為 [執行中]。

    ![服務 – OMS 閘道](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="configure-network-load-balancing"></a>設定網路負載平衡 
您可以透過網路負載平衡 (NLB) 為閘道設定高可用性。 請使用 Microsoft 網路負載平衡或以硬體為基礎的負載平衡器。  負載平衡器可藉由跨其節點將來自 OMS 代理程式或 Operations Manager 管理伺服器的要求連線進行重新導向，來管理流量。 如果閘道伺服器故障，流量就會被重新導向到其他節點。

若要了解如何設計和部署 Windows Server 2016 網路負載平衡叢集，請參閱[網路負載平衡](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing)。  下列步驟說明如何設定 Microsoft 網路負載平衡叢集。  

1.  使用系統管理帳戶登入屬於 NLB 叢集成員的 Windows 伺服器。  

2.  在伺服器管理員中，開啟網路負載平衡管理員。 選取 [工具]，然後選取 [網路負載平衡管理員]。

3. 若要連線已安裝 Microsoft Monitoring Agent 的 OMS 閘道伺服器，請用滑鼠右鍵按一下叢集的 IP 位址，然後選取 [新增主機到叢集]。

 ![網路負載平衡管理員--新增主機到叢集](./media/log-analytics-oms-gateway/nlb02.png)

4. 輸入您要連線之閘道伺服器的 IP 位址。

    ![網路負載平衡管理員--新增主機到叢集：連線](./media/log-analytics-oms-gateway/nlb03.png) 
    
## <a name="configure-the-oms-agent-and-the-operations-manager-management-group"></a>設定 OMS 代理程式和 Operations Manager 管理群組
本文後續幾節所包含的步驟，會說明如何為直接連線的 OMS 代理程式、Operations Manager 管理群組或 Azure 自動化混合式 Runbook 背景工作設定 OMS 閘道以與 Azure 自動化或 Log Analytics 通訊。  

若要了解在直接連線至 Log Analytics 的 Windows 電腦上，其中 OMS 代理程式的安裝需求和步驟，請參閱[使用 Log Analytics 從環境中的電腦收集資料](log-analytics-concept-hybrid.md#prerequisites)。

 若為 Linux 電腦，請參閱[將 Linux 電腦連接到 Log Analytics](log-analytics-quick-collect-linux-computer.md)。 如需自動化混合式 Runbook 背景工作角色 的相關資訊，請參閱[部署混合式 Runbook 背景工作角色](../automation/automation-hybrid-runbook-worker.md)。

### <a name="configure-the-standalone-oms-agent"></a>設定獨立的 OMS 代理程式
如需如何將代理程式設定為使用 Proxy 伺服器 (此案例所使用的是閘道) 的相關資訊，請參閱[使用 Log Analytics 從環境中的電腦收集資料](log-analytics-concept-hybrid.md#prerequisites)。 如果您已在網路負載平衡器後方部署了多個閘道伺服器，OMS 代理程式 Proxy 組態會是 NLB 的虛擬 IP 位址︰

![Microsoft Monitoring Agent 內容 – Proxy 設定](./media/log-analytics-oms-gateway/nlb04.png)

### <a name="configure-operations-manager-all-agents-use-the-same-proxy-server"></a>設定 Operations Manager：所有代理程式使用相同的 Proxy 伺服器
您可以設定 Operations Manager 來新增閘道伺服器。  即使設定是空的，Operations Manager Proxy 組態仍會自動套用到所有向 Operations Manager 報告的代理程式。  

若要使用 OMS 閘道來支援 Operations Manager，請備妥下列元件︰

* 在閘道伺服器上安裝 Microsoft Monitoring Agent (代理程式版本 **8.0.10900.0** 或更新版本)，並針對您想要與之通訊的 Log Analytics 工作區進行設定。

* 具有網際網路連線的閘道，或能連線到具有網際網路連線之 Proxy 伺服器的閘道。

> [!NOTE]
> 如果未指定閘道的值，系統會將空白值推送到所有代理程式。
> 

如果這是 Operations Manager 管理群組第一次向 Log Analytics 工作區進行登錄，在 Operations 主控台中為管理群組指定 Proxy 設定的選項將無法使用。 

必須先向服務成功登錄該管理群組，之後此選項才可供使用。 在執行 Operations 主控台的同一個系統上使用 Netsh 更新系統 Proxy 設定，以及管理群組中的所有管理伺服器。

1. 開啟提升權限的命令提示字元。

    a. 移至 [啟動]。 然後輸入 **cmd**。
    
    b. 以滑鼠右鍵按一下 [命令提示字元]。 然後選取 [以系統管理員身分執行]。
    
2. 輸入下列命令，然後選取 **Enter**：

    `netsh winhttp set proxy <proxy>:<port>`

完成與 Log Analytics 的整合之後，您可以透過執行 `netsh winhttp reset proxy` 來移除變更。 然後使用 Operations 主控台中的 [設定 Proxy 伺服器] 選項來指定 OMS 閘道伺服器。 

1. 開啟 Operations Manager 主控台。 在 [Operations Management Suite] 下選取 [連線]。 然後選取 [設定 Proxy 伺服器]。

    ![Operations Manager--設定 Proxy 伺服器](./media/log-analytics-oms-gateway/scom01.png)

2. 選取 [使用 Proxy 伺服器來存取 Operations Management Suite]。 然後輸入 OMS 閘道伺服器的 IP 位址，或 NLB 的虛擬 IP 位址。 確定您已在開頭使用 `http://` 前置詞。

    ![Operations Manager--Proxy 伺服器位址](./media/log-analytics-oms-gateway/scom02.png)

3. 選取 [完成]。 Operations Manager 管理群組現在已設定為透過閘道伺服器來與 Log Analytics 服務進行通訊。

### <a name="configure-operations-manager-specific-agents-use-proxy-server"></a>設定 Operations Manager：特定代理程式使用 Proxy 伺服器
對於大型或複雜的環境，建議您只讓特定伺服器 (或群組) 使用 OMS 閘道伺服器。 對於這些伺服器，您無法直接更新 Operations Manager 代理程式，因為這個值會遭到管理群組的全域值所覆寫。 相反地，請覆寫用來推送這些值的規則。  

> [!NOTE] 
> 這個相同的設定技巧可用來允許您的環境使用多個 OMS 閘道伺服器。 例如，您可能需要以每個區域為基礎來指定特定的 OMS 閘道伺服器。
>  

1. 開啟 Operations Manager 主控台，然後選取 [撰寫] 工作區。

2. 在 [撰寫] 工作區中選取 [規則]。 然後選取 Operations Manager 工具列上的 [範圍] 按鈕。 如果此按鈕無法使用，請進行檢查以確定您在 [監視] 窗格中選取的是物件，而非資料夾。 [範圍管理組件物件] 對話方塊會顯示一般會作為目標之類別、群組或物件的清單。 

3. 在 [尋找] 欄位中，輸入**健全狀況服務**。 然後，從清單中選取它。 選取 [確定] 。  

4. 搜尋 [Advisor Proxy 設定規則] 規則。 在 Operations 主控台工具列中，選取 [覆寫]。 然後選取 [覆寫特定類別物件的規則: 健全狀況服務]。 

5. 接下來，從清單中選取特定的物件。 

6. (選擇性) 建立自訂群組，且其中要包含您要套用此覆寫的伺服器健全狀況服務物件。 然後，對該群組套用覆寫。

7. 在 [覆寫屬性] 對話方塊中，按一下以勾選 [WebProxyAddress] 參數旁的 [覆寫] 資料行。  在 [覆寫值] 欄位中，輸入 OMS 閘道伺服器的 URL，並確保其開頭有 `http://` 前置詞。  

    >[!NOTE]
    > 您不需要啟用此規則。 系統已自動使用覆寫來管理它，該覆寫包含在 Microsoft System Center Advisor 安全參考覆寫管理組件中，而此組件是以 Microsoft System Center Advisor 監控伺服器群組為目標。
    >   

8. 從 [選取目的地管理組件] 清單選取管理組件，或選取 [新增] 來建立新的未密封管理組件。 

9. 當您變更完成時，選取 [確定]。 

### <a name="configure-the-oms-gateway-for-automation-hybrid-runbook-workers"></a>為自動化混合式 Runbook 背景工作角色設定 OMS 閘道
如果您的環境中有自動化混合式 Runbook 背景工作，下列步驟可提供手動、暫時性解決方式來設定 OMS 閘道以支援它們。

在下列步驟中，您需要知道自動化帳戶所在的 Azure 區域。 若要找到該位置，請採取下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 選取 Azure 自動化服務。
3. 選取適當的 Azure 自動化帳戶。
4. 在 [位置] 下，檢視帳戶的區域。

    ![Azure 入口網站--自動化帳戶位置](./media/log-analytics-oms-gateway/location.png)  

使用下列表格來識別每個位置的 URL：

**作業執行階段資料服務 URL**

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

如果您的電腦是使用更新管理方案自動註冊為混合式 Runbook 背景工作以進行修補，請遵循下列步驟：

1. 將作業執行階段資料服務 URL 新增到 OMS 閘道上的「允許的主機」清單。 例如，輸入下列內容：`Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`

2. 使用以下 PowerShell Cmdlet 重新啟動 OMS 閘道服務：`Restart-Service OMSGatewayService`

如果您使用混合式 Runbook 背景工作角色註冊 Cmdlet 將電腦加入 Azure 自動化，請遵循下列步驟：

1. 將代理程式服務註冊 URL 新增到 OMS 閘道上的「允許的主機」清單。 例如，輸入下列內容：`Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`

2. 將作業執行階段資料服務 URL 新增到 OMS 閘道上的「允許的主機」清單。 例如，輸入下列內容：`Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`

3. 重新啟動 OMS 閘道服務：`Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>PowerShell Cmdlet
Cmdlet 可以協助您完成更新 OMS 閘道組態設定時需執行的工作。 在使用之前，請務必先採取下列步驟：

1. 安裝 OMS 閘道 (MSI)。
2. 開啟 PowerShell 主控台視窗。
3. 輸入下列命令來匯入模組：`Import-Module OMSGateway`。
4. 如果上一個步驟沒有發生錯誤，就表示模組已經成功匯入，且可以使用 Cmdlet。 輸入 `Get-Module OMSGateway`。
5. 在您使用 Cmdlet 進行變更之後，請確定您已重新啟動 OMS 閘道服務。

如果您在步驟 3 發生錯誤，表示模組並未匯入。 當 PowerShell 找不到模組時，就可能會發生錯誤。 您可以在 OMS 閘道的安裝路徑中找到它：C:\Program Files\Microsoft OMS Gateway\PowerShell。

| **Cmdlet** | **參數** | **說明** | **範例** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Key |取得服務的組態 |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |索引鍵 (必要) <br> 值 |變更服務的組態 |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |取得轉送 (上游) Proxy 的位址 |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |位址<br> 使用者名稱<br> 密碼 |設定轉送 (上游) Proxy 的位址 (與認證) |1.設定回覆 Proxy 和認證：<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2.設定不需要驗證的回覆 Proxy：`Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3.清除轉送 Proxy 設定︰<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |取得目前允許的主機 (僅限本機設定的允許的主機，不包括自動下載的允許的主機) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |主機 (必要) |將主機加到允許清單 |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |主機 (必要) |將主機從允許清單移除 |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |主體 (必要) |將用戶端憑證主體加到允許清單 |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |主體 (必要) |將用戶端憑證主體從允許清單移除 |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |取得目前允許的用戶端憑證主體 (僅限本機設定的允許的主體，不包括自動下載的允許的主體) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>疑難排解
若要收集閘道所記錄的事件，您還需要安裝 OMS 代理程式。

![事件檢視器--OMS 閘道記錄](./media/log-analytics-oms-gateway/event-viewer.png)

**OMS 閘道事件識別碼與描述**

以下表格顯示 OMS 閘道記錄事件的事件識別碼與描述：

| **識別碼** | **說明** |
| --- | --- |
| 400 |沒有特定識別碼的任何應用程式錯誤。 |
| 401 |錯誤組態。 例如：listenPort = "text"，而不是整數。 |
| 402 |剖析 TLS 交握訊息時發生例外狀況。 |
| 403 |網路錯誤。 例如：無法連線到目標伺服器。 |
| 100 |一般資訊。 |
| 101 |服務已經啟動。 |
| 102 |服務已經停止。 |
| 103 |已從用戶端接收到 HTTP CONNECT 命令。 |
| 104 |不是 HTTP CONNECT 命令。 |
| 105 |目的地伺服器不在允許的清單中，或目的地連接埠不是安全的連接埠 (443)。 <br> <br> 請確定您閘道伺服器上的 MMA 代理程式和與閘道通訊的代理程式，是連線到相同的 Log Analytics 工作區。 |
| 105 |ERROR TcpConnection – 無效的用戶端憑證: CN=Gateway <br><br> 請確定： <br>    <br> - 您是使用版本號碼為 1.0.395.0 或更新版本的閘道。 <br> - 閘道伺服器上的 MMA 代理程式和與閘道通訊的代理程式，是連線到相同的 Log Analytics 工作區。 |
| 106 |OMS 閘道只支援 TLS 1.0、TLS 1.1 和 1.2。  不支援 SSL。 針對任何不支援的 TLS/SSL 通訊協定版本，OMS 閘道會產生事件識別碼 106。|
| 107 |已經驗證 TLS 工作階段。 |

**要收集的效能計數器**

以下表格顯示可供 OMS 閘道使用的效能計數器。 您可以使用效能監視器新增計數器。

| **名稱** | **描述** |
| --- | --- |
| OMS 閘道/使用中用戶端連線 |使用中用戶端網路 (TCP) 連線數目 |
| OMS 閘道/錯誤計數 |錯誤數目 |
| OMS 閘道/已連線用戶端 |已連線用戶端數目 |
| OMS 閘道/拒絕計數 |因為任何 TLS 驗證錯誤而被拒絕的次數 |

![OMS 閘道效能計數器](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>取得協助
當您已經登入 Azure 入口網站時，您可以建立要求，來要求在 OMS 閘道或任何其他 Azure 服務或服務功能上提供協助。

若要要求協助，請選取入口網站右上角的問號符號。 然後選取 [新增支援要求]。 完成新的支援要求表單。

![新增支援要求](./media/log-analytics-oms-gateway/support.png)

## <a name="next-steps"></a>後續步驟
[新增資料來源](log-analytics-data-sources.md)會從 [連接的來源] 收集資料，並將它儲存在 Log Analytics 工作區中。
