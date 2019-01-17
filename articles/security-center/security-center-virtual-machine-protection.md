---
title: 保護 Azure 資訊安全中心內的機器和應用程式 | Microsoft Docs
description: 本文件說明資訊安全中心為了協助您保護虛擬機器和電腦以及 Web 應用程式和 App Service 環境所提供的建議。
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: 3a2ccd04cd7ec36cafdf56830b9ad8249f89eb7e
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2019
ms.locfileid: "54321581"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>保護 Azure 資訊安全中心內的機器和應用程式
「Azure 資訊安全中心」會分析 Azure 資源的安全性狀態。 當資訊安全中心發現潛在的安全性弱點時，它會建立可引導您完成所需控制之設定程序的建議。 這些建議適用於下列 Azure 資源類型︰虛擬機器 (VM) 和電腦、應用程式、網路、SQL、身分識別與存取。

本文說明適用於機器和應用程式的建議。

## <a name="monitoring-security-health"></a>監視安全性健全狀況
您可在 [資訊安全中心 – 概觀] 儀表板上監視資源的安全性狀態。 [資源] 區段提供為每個資源類型識別到的問題數目和安全性狀態。

您可以選取 [建議] 來檢視所有問題的清單。 如需有關如何套用建議的詳細資訊，請參閱[實作 Azure 資訊安全中心的安全性建議](security-center-recommendations.md)。

如需計算和應用程式服務建議的完整清單，請參閱[建議](security-center-virtual-machine-recommendations.md)。

若要繼續，請選取 [資源] 或 [資訊安全中心] 主功能表下方的 [計算和應用程式]。
![資訊安全中心儀表板](./media/security-center-virtual-machine-recommendations/overview.png)

## <a name="monitor-compute-and-app-services"></a>監視計算和應用程式服務
[計算及應用程式] 下有下列索引標籤：

- **概觀**：資訊安全中心所識別的監視和建議。
- **VM 和電腦**：列出您的 VM、電腦和各項目目前的安全性狀態。
- **雲端服務**︰列出資訊安全中心所監視的 Web 和背景工作角色。
- **應用程式服務 (預覽)**：列出您的應用程式服務環境和各項目目前的安全性狀態。
- **容器 (預覽)**：列出在 IaaS Linux 電腦上所裝載的容器，以及其 Docker 設定的安全性評定。
- **計算資源 (預覽)**：列出您計算資源所適用的建議，例如 Service Fabric 叢集和事件中樞。

若要繼續，請選取 [資源安全性檢查] 下的 [計算及應用程式]。

![計算](./media/security-center-virtual-machine-recommendations/compute.png)

在您可有多個區段的每個索引標籤中，以及在每個區段中，您可以選取一個個別的選項，以查看有關解決該特定問題之建議步驟的更多詳細資訊。

### 未受監視的 VM 和電腦<a name="unmonitored-vms-and-computers"></a>
如果機器未執行 Microsoft Monitoring Agent 擴充功能，VM 或電腦就不會由資訊安全中心監視。 機器可能已安裝本機代理程式，例如 OMS 直接代理程式或 SCOM 代理程式。 具有這些代理程式的機器會識別為未受監視，因為資訊安全中心無法完整支援這些代理程式。 若要能完整享有資訊安全中心的所有功能，則需要 Microsoft Monitoring Agent 擴充功能。

除了已安裝的本機代理程式，您還可以在未受監視的 VM 或電腦上安裝擴充功能。 以相同方式設定這兩種代理程式，並連線至相同工作區。 這可讓資訊安全中心與 Microsoft Monitoring Agent 擴充功能互動和收集資料。 請參閱[啟用 VM 擴充公能](../azure-monitor/learn/quick-collect-azurevm.md)，取得如何安裝 Microsoft Monitoring Agent 擴充功能的指示。

請參閱[監視代理程式健康情況問題](security-center-troubleshooting-guide.md#mon-agent)，深入了解資訊安全中心無法成功監視為了自動佈建而初始化之 VM 和電腦的原因。

### <a name="recommendations"></a>建議
此區段會分別就資訊安全中心所監視的每個 VM 和電腦、Web 和背景工作角色、Azure App Service Web 應用程式和 Azure App Service 環境提供一組建議。 第一個資料行會列出建議。 第二個資料行則顯示受該項建議影響的資源總數。 第三個資料行顯示問題的嚴重性。

每個建議在經選取後，都會有一組可供執行的動作。 比方說，如果您選取 [遺漏系統更新]，將會出現遺漏修補程式的 VM 和電腦數目，以及遺漏更新的嚴重性。

[套用系統更新] 有圖形格式的重大更新摘要 (一個適用於 Windows，一個適用於 Linux)。 第二部分有包含下列資訊的表格：

- **名稱**：遺漏更新的名稱。
- **VM 及電腦數目**：遺漏此更新的 VM 和電腦總數。
- **更新嚴重性**：描述該特定建議的嚴重性：

    - **重大**：某個有意義的資源 (應用程式、虛擬機器或網路安全性群組) 有弱點存在，並且需要注意。
    - **重要**：需要執行非關鍵性步驟或其他步驟才能完成程序或消除弱點。
    - **中**：應該處理但不需要立即注意的弱點。 (預設不會顯示嚴重性低的建議，但是如果您要檢視它們，則可以篩選嚴重性低的建議)。


- **狀態**：建議的目前狀態：

    - **未處理**：建議尚未處理。
    - **進行中**：正在將建議套用到資源，且您不需要採取任何動作。
    - **已解決**：建議動作已完成。 (若問題已解決，該項目會呈現暗灰色)。

若要檢視建議的詳細資料，請按一下清單中遺漏更新的名稱。


> [!NOTE]
> 此處的安全性建議與 [建議] 圖格下的相同。 如需如何處理建議的詳細資訊，請參閱[實作 Azure 資訊安全中心的安全性建議](security-center-recommendations.md)一文。
>
>

### <a name="vms-and-computers"></a>VM 和電腦
[VM 和電腦] 區段提供所有 VM 和電腦的建議概觀。 每一欄表示一組建議。

![VM 和電腦的建議](./media/security-center-virtual-machine-recommendations/vm-computers.png)

此清單中列出四種類型的圖示：

![非 Azure 電腦](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) 非 Azure 電腦。

![Azure Resource Manager VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Azure Resource Manager VM。

![Azure 傳統 VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Azure 傳統 VM。


![從工作區識別的 VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) 僅只從已檢視訂用帳戶中的工作區識別的 VM。 這包括來自向此訂用帳戶中的工作區回報之其他訂用帳戶的 VM，以及使用 SCOM 直接代理程式安裝的 VM (沒有資源識別碼)。

每個建議底下出現的圖示，可協助您快速識別需要關注的 VM 和電腦，以及建議的類型。 您也可以使用篩選，依**資源類型**及依**嚴重性**來搜尋清單。

若要向下切入到每個 VM 的安全性建議，請按一下該 VM。
您可在此處查看 VM 或電腦的安全性詳細資料。 您可以在底部看到建議的動作和每個問題的嚴重性。
![雲端服務](./media/security-center-virtual-machine-recommendations/recommendation-list.png)

### <a name="cloud-services"></a>雲端服務
針對雲端服務，若作業系統版本已過期，便會建立建議。

![雲端服務](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

在您具有建議的情況下 (這不是前一個範例的案例)，您需要依照建議步驟來更新作業系統版本。 有可用的更新時，您會收到警示 (紅色或橙色 - 取決於問題的嚴重性)。 當您選取 WebRole1 (使用自動部署至 IIS 的 Web 應用程式執行 Windows Server) 或 WorkerRole1 (使用自動部署至 IIS 的 Web 應用程式執行 Windows Server) 資料列中的這個警示，您可看到這項建議的詳細資料。

若要查看有關這項建議的更多規範說明，請按一下 [描述] 資料行之下的 [更新OS 版本]。



![更新作業系統版本](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png)

### <a name="app-services-preview"></a>應用程式服務 (預覽)

> [!NOTE]
> 監視 App Service 是預覽功能，僅適用於資訊安全中心的標準層。 若要深入了解資訊安全中心的定價層，請參閱[價格](security-center-pricing.md)。
>
>

在 [應用程式服務] 下方，您會看到應用程式服務環境的清單，並可根據資訊安全中心執行的評估檢視健康情況摘要。

![應用程式服務](./media/security-center-virtual-machine-recommendations/app-services.png)

此清單中列出三種類型的圖示：

![應用程式服務環境](./media/security-center-virtual-machine-recommendations/ase.png) 應用程式服務環境。

![Web 應用程式](./media/security-center-virtual-machine-recommendations/web-app.png) Web 應用程式。

![函式應用程式](./media/security-center-virtual-machine-recommendations/function-app.png) 函式應用程式。

1. 選取 Web 應用程式。 摘要檢視隨即開啟，並有三個索引標籤：

  - **建議**：根據資訊安全中心所執行的失敗評估。
  - **已通過的評估**：資訊安全中心所執行、已通過的評估清單。
  - **無法使用的評估**：列出因錯誤或建議與特定應用程式服務無關而無法執行的評估

  [建議] 下方會列出為選取的 Web 應用程式提供的建議，和每項建議的嚴重性。

  ![應用程式服務建議](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

2. 選取建議可查看建議的描述，以及狀況不良資源、狀況良好資源和未掃描資源的清單。

 - [已通過的評定] 資料行下有已通過的評定清單。  這些評估的嚴重性永遠是綠色的。

 -  從清單中選取已通過的評估，以取得評估的描述，以及狀況不良和狀況良好的資源清單，和未掃描的資源清單。 狀況不良的資源會有索引標籤，但由於已通過評估，因此該清單一律是空白的。

    ![App Service 修復](./media/security-center-virtual-machine-recommendations/app-service-remediation.png)


## <a name="compute-and-app-recommendations"></a>計算和應用程式建議
|資源類型|安全分數|建議|說明|
|----|----|----|----|
|App Service|20|Web 應用程式應只可經由 HTTPS 存取|僅限透過 HTTPS 存取 Web 應用程式。|
|App Service|20|函式應用程式應只可經由 HTTPS 存取|僅限透過 HTTPS 存取函數應用程式。|
|App Service|5|啟用 App Service 診斷記錄|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。 |
|App Service|10|Web 應用程式的遠端偵錯應關閉|如果您不再需要使用 Web 應用程式的偵錯功能，請將該功能關閉。 遠端偵錯需要在函式應用程式上開啟輸入連接埠。|
|App Service|10|函數應用程式的遠端偵錯應關閉|如果您不再需要使用函數應用程式的偵錯功能，請將該功能關閉。 遠端偵錯需要在函式應用程式上開啟輸入連接埠。|
|App Service|10|設定 Web 應用程式的 IP 限制|定義獲允許存取應用程式的 IP 位址清單。 使用 IP 限制可防止 Web 應用程式遭受常見攻擊。|
|App Service|10|設定函式應用程式的 IP 限制| 定義獲允許存取應用程式的 IP 位址清單。 使用 IP 限制可防止函式應用程式遭受常見攻擊。|
|App Service|10|請勿允許所有 ('*') 資源來存取應用程式| 請勿允許將 WEBSITE_LOAD_CERTIFICATES 參數設定為 ""。 將此參數設定為 ‘’ 意謂著會將所有憑證都載入至 Web 應用程式個人憑證存放區。 這會導致最小權限的原則遭到濫用，原因是網站不可能需要在執行階段存取所有憑證。|
|App Service|5|Web 應用程式應停用 Web 通訊端|請檢閱 Web 應用程式內 Web 通訊端的使用狀況。 Web 通訊端通訊協定易受不同類型的安全性威脅攻擊。|
|App Service|5|函數應用程式應停用 Web 通訊端|請檢閱函數應用程式內 Web 通訊端的使用狀況。 Web 通訊端通訊協定易受不同類型的安全性威脅攻擊。|
|App Service|5|對 Web 應用程式使用自訂網域|使用自訂網域來防止 Web 應用程式遭受常見的攻擊，例如網路釣魚及其他 DNS 相關攻擊。|
|App Service|5|對函式應用程式使用自訂網域|使用自訂網域來防止函數應用程式遭受常見的攻擊，例如網路釣魚及其他 DNS 相關攻擊。|
|App Service|20|CORS 不應允許每項資源存取您的 Web 應用程式|請只允許必要網域與您的 Web 應用程式互動。 跨原始資源共用 (CORS) 不應允許所有網域存取 Web 應用程式。|
|App Service|20|CORS 不應允許每項資源存取函式應用程式| 請只允許必要網域與您的函數應用程式互動。 跨原始資源共用 (CORS) 不應允許所有網域存取函式應用程式。|
|App Service|10|為 Web 應用程式使用支援的最新 .NET Framework|請使用最新的 .NET Framework 版本，以取得最新的安全性類別。 使用較舊的類別和類型會讓應用程式變得容易遭受攻擊。|
|App Service|10|為 Web 應用程式使用支援的最新 JAVA 版本|請使用最新的 JAVA 版本，以取得最新的安全性類別。 使用較舊的類別和類型會讓應用程式變得容易遭受攻擊。|
|App Service|10|為 Web 應用程式使用支援的最新 PHP 版本|請使用最新的 PHP 版本，以取得最新的安全性類別。 使用較舊的類別和類型會讓應用程式變得容易遭受攻擊。|
|App Service|10|為 Web 應用程式使用支援的最新 Node.js 版本|請使用最新的 Node.js 版本，以取得最新的安全性類別。 使用較舊的類別和類型會讓應用程式變得容易遭受攻擊。|
|App Service|10|為 Web 應用程式使用支援的最新 Python 版本|請使用最新的 Python 版本，以取得最新的安全性類別。 使用較舊的類別和類型會讓應用程式變得容易遭受攻擊。|
|計算資源 (Batch)|1|為 Batch 帳戶設定計量警示規則|為 Batch 帳戶設定計量警示規則，並啟用計量的「集區刪除完成事件」與「集區刪除開始事件」|
|計算資源 (Service Fabric)|10|在 Service Fabric 中使用 Azure Active Directory 來執行用戶端驗證|在 Service Fabric 中只透過 Azure Active Directory 來執行用戶端驗證。|
|計算資源 (自動化帳戶)|5| 啟用自動化帳戶加密|儲存敏感性資料時，啟用自動化帳戶變數資產加密。|
|計算資源 (Load Balancer)|5|啟用 Load Balancer 診斷記錄|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。 |
|計算資源 (搜尋)|5|啟用搜尋服務診斷記錄|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。 |
|計算資源 (服務匯流排)|5|啟用服務匯流排診斷記錄|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。 |
|計算資源 (串流分析)|5|啟用 Azure 串流分析診斷記錄|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。 |
|計算資源 (Service Fabric)|5|啟用 Service Fabric 診斷記錄|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。 |
|計算資源 (Batch)|5|為 Batch 帳戶啟用診斷記錄|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。 |
|計算資源 (事件中樞)|5|啟用事件中樞診斷記錄|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。 |
|計算資源 (Logic Apps)|5|啟用 Logic Apps 診斷記錄|啟用記錄並保留最多一年。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索供調查之用。 |
|計算資源 (Service Fabric)|15|在 Service Fabric 中，將 ClusterProtectionLevel 屬性設定為 EncryptAndSign|Service Fabric 使用主要叢集憑證，可為節點對節點的通訊提供三層保護 (None、Sign 及 EncryptAndSign)。  設定保護層級可確保所有節點對節點的訊息皆經過加密及數位簽署。 |
|計算資源 (服務匯流排)|1|從服務匯流排命名空間移除 RootManageSharedAccessKey 以外的所有授權規則 |服務匯流排用戶端不應該使用提供命名空間中所有佇列及主題存取權的命名空間層級存取原則。 若要與最低權限資訊安全模型達成一致，您應在佇列和主題的實體層級建立存取原則，以提供僅限特定實體的存取權。|
|計算資源 (事件中樞)|1|從事件中樞命名空間移除 RootManageSharedAccessKey 以外的所有授權規則 |事件中樞用戶端不應該使用提供命名空間中所有佇列及主題存取權的命名空間層級存取原則。 若要與最低權限資訊安全模型達成一致，您應在佇列和主題的實體層級建立存取原則，以提供僅限特定實體的存取權。|
|計算資源 (事件中樞)|5|為事件中樞實體定義授權規則|稽核事件中樞實體上的授權規則，以只授與最低權限的存取權。|
|機器|50|請在您的機器上安裝 Monitoring Agent|安裝 Monitoring Agent，以在每部機器上啟用資料收集、更新掃描、基準掃描及端點保護。|
|機器|50|為訂用帳戶啟用自動佈建和資料收集 |為您訂用帳戶中的機器啟用自動佈建和資料收集，以在新增至您訂用帳戶的每部機器上啟用資料收集、更新掃描、基準掃描及端點保護。|
|機器|40|請解決機器上的 Monitoring Agent 健康情況問題|為了獲得完整的「資訊安全中心」保護，請依照《疑難排解指南》中的指示，解決機器上的監視代理程式問題| 
|機器|40|請解決機器上的 Endpoint Protection 健康情況問題|為了獲得完整的「資訊安全中心」保護，請依照《疑難排解指南》中的指示，解決機器上的監視代理程式問題。|
|機器|40|請對機器上缺少的掃描資料進行疑難排解|針對虛擬機器和電腦上缺少的掃描資料進行疑難排解。 機器上缺少掃描資料會導致缺少安全性評定，例如更新掃描、基準掃描及缺少端點保護解決方案掃描。|
|機器|40|請在您的機器上安裝系統更新|安裝缺少的系統安全性與重大更新，以保護您的 Windows 及 Linux 虛擬機器與電腦
|機器|15|新增 Web 應用程式防火牆| 新增 Web 應用程式防火牆 (WAF) 解決方案來保護 Web 應用程式。 |
|機器|40|請更新雲端服務角色的 OS 版本|將您雲端服務角色的作業系統 (OS) 版本更新至您 OS 系列可用的最新版本。|
|機器|35|請修復機器上安全性設定中的弱點|修復機器上安全性設定中的弱點，以避免這些機器遭受攻擊。 |
|機器|35|請修復容器上安全性設定中的弱點|修複已安裝 Docker 之機器上安全性設定中的弱點，以避免這些機器遭受攻擊。|
|機器|25|啟用自適性應用程式控制|啟用應用程式控制，以控制哪些應用程式可在您位於 Azure 中的 VM 上執行。 這可協助強化您 VM 抵禦惡意程式碼的能力。 「資訊安全中心」會利用機器學習服務來分析在每個 VM 上執行的應用程式，並協助您利用此情報來套用允許規則。 此功能可簡化設定及維護應用程式允許規則的程序。|
|機器|20|請在您的機器上安裝 Endpoint Protection 解決方案|在虛擬機器上安裝 Endpoint Protection 解決方案，以避免虛擬機器遭受威脅及弱點損害。|
|機器|20|請重新啟動您的機器，以套用系統更新|重新啟動機器，以套用系統更新及避免機器受到弱點損害。|
|機器|15|請在您的虛擬機器上套用磁碟加密|使用「Azure 磁碟加密」為 Windows 和 Linux 虛擬機器磁碟加密虛擬機器磁碟。 「Azure 磁碟加密」(ADE) 利用 Windows 的業界標準 BitLocker 功能及 Linux 的 DM-Crypt 功能來提供 OS 和資料磁碟加密，以協助您在客戶 Azure Key Vault 中保護資料並滿足組織的安全性與合規性承諾。 當您的合規性與安全性需求要求您使用加密金鑰來進行端對端資料加密 (包括加密暫時磁碟 (本機連結的臨時磁碟)) 時，請使用「Azure 磁碟加密」。 或者，系統預設會使用「Azure 儲存體服務加密」將待用的「受控磁碟」加密，其中加密金鑰為 Azure 中的 Microsoft 受控金鑰。 如果這符合您的合規性與安全性需求，您便可以利用預設的受控磁碟加密來滿足您的需求。|
|機器|30|在您的虛擬機器上安裝弱點評定解決方案|在您的虛擬機器上安裝弱點評定解決方案|
|機器|15|新增 Web 應用程式防火牆| 新增 Web 應用程式防火牆 (WAF) 解決方案來保護 Web 應用程式。 |
|機器|30|利用弱點評定解決方案修復弱點|針對已部署弱點評定第三方解決方案的虛擬機器，持續評定是否有應用程式和 OS 弱點。 每當找到這類弱點時，都會隨著建議一併提供這些弱點的相關詳細資訊。|
|機器|30|在您的虛擬機器上安裝弱點評定解決方案|在您的虛擬機器上安裝弱點評定解決方案|
|機器|1|將虛擬機器移轉到新的 Azure Resource Manager 資源|針對您的虛擬機器使用 Azure Resource Manager 以加強安全性，除了增強存取控制 (RBAC)、改善稽核、提供以 Resource Manager 為基礎的部署及治理、受控身分識別的存取、金鑰保存庫的祕密存取、以 Azure AD 為基礎的驗證，並支援標籤和資源群組，讓安全性管理更加輕鬆。 |
|機器|30|利用弱點評定解決方案修復弱點|針對已部署弱點評定第三方解決方案的虛擬機器，持續評定是否有應用程式和 OS 弱點。 每當找到這類弱點時，都會隨著建議一併提供這些弱點的相關詳細資訊。|

 





## <a name="next-steps"></a>後續步驟
若要深入了解適用於其他 Azure 資源類型的建議，請參閱下列文章︰


* [了解 Azure 資訊安全中心對虛擬機器的建議](security-center-virtual-machine-recommendations.md)
* [在 Azure 資訊安全中心監視身分識別和存取](security-center-identity-access.md)
* [保護 Azure 資訊安全中心內的網路](security-center-network-recommendations.md)
* [保護 Azure 資訊安全中心內的 Azure SQL 服務](security-center-sql-service-recommendations.md)

如要深入了解資訊安全中心，請參閱下列主題：

* [在 Azure 資訊安全中心設定安全性原則](tutorial-security-policy.md) -- 了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
* [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) -- 了解如何管理與回應安全性警示。
* [Azure 資訊安全中心常見問題集](security-center-faq.md) -- 尋找有關使用服務的常見問題。

