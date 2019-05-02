---
title: Azure 容器執行個體的安全性考量
description: Azure Container Instances 及適用於任何容器平台的一般安全性考量的安全映像和祕密的建議
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/29/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: dc516277d79e37500e73e1aee6b88c908acb9b1c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943992"
---
# <a name="security-considerations-for-azure-container-instances"></a>Azure 容器執行個體的安全性考量

這篇文章介紹使用 Azure Container Instances 來執行容器應用程式的安全性考量。 主題包括：

> [!div class="checklist"]
> * **安全性建議**來管理 Azure 容器執行個體的映像和祕密
> * **容器生態系統的考量**整個容器的生命週期，任何容器平台

## <a name="security-recommendations-for-azure-container-instances"></a>Azure 容器執行個體的安全性建議

### <a name="use-a-private-registry"></a>使用私人登錄

容器是透過一或多個存放庫中所儲存的映像來建立的。 這些存放庫可屬於公用登錄，像是[Docker Hub](https://hub.docker.com)，或至私人登錄。 私人登錄的範例是 [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/2.0/)，它可以安裝在內部部署環境或虛擬私人雲端中。 您也可以使用雲端的私人容器登錄服務，包括[Azure Container Registry](../container-registry/container-registry-intro.md)。 

公開可用的容器映像並不保證安全性。 容器映像包含多個軟體層，而且每個軟體層可能有弱點。 若要協助減少攻擊的威脅，您應該儲存，並擷取從私人登錄，例如 Azure Container Registry 或 Docker Trusted Registry 的映像。 除了提供受控私人登錄，支援 Azure Container Registry[服務主體為基礎的驗證](../container-registry/container-registry-authentication.md)透過 Azure Active Directory 的基本驗證流程。 此驗證包括唯讀狀態 （提取）、 寫入 （推送） 和擁有者權限的角色型存取。

### <a name="monitor-and-scan-container-images"></a>監視和掃描的容器映像

安全性監視和掃描解決方案，例如[Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview)並[Aqua Security](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview)都是透過 Azure Marketplace。 您可以使用它們來掃描私人登錄中的容器映像，並識別潛在的弱點。 請務必了解的掃描不同的解決方案，提供深度。 

### <a name="protect-credentials"></a>保護認證

容器可以分散到數個叢集和 Azure 區域。 因此，您必須保護所需的登入或 API 的存取權，例如密碼或權杖的認證。 請確定只有特殊權限的使用者可以存取這些傳輸中和待用的容器。 清查所有認證祕密，並要求開發人員使用新興的祕密管理工具，其設計對容器平台。  請確定您的方案包含加密的資料庫，祕密中的資料傳輸和最低權限的 TLS 加密[角色型存取控制](../role-based-access-control/overview.md)。 [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md)容器化應用程式是一項雲端服務，保護加密金鑰和祕密 （例如憑證、 連接字串和密碼）。 由於這項資料是敏感性 」 和 「 商務關鍵性，安全地存取您的金鑰保存庫，讓只有經過授權的應用程式和使用者可以存取它們。

## <a name="considerations-for-the-container-ecosystem"></a>容器生態系統的考量

下列安全性措施也實作及有效地管理可協助您保護您的容器生態系統。 這些量值適用於整個容器的生命週期，從開發到生產環境部署，以及各種容器協調器、 主機及平台。 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>弱點管理做為您的容器開發生命週期的一部分 

藉由使用有效的弱點可能會在容器開發週期的管理，您可以改善機率您識別及解決安全性考量，才能在問題更嚴重的問題。 

### <a name="scan-for-vulnerabilities"></a>掃描弱點 

新的弱點會探索所有的時間，因此掃描，以及用來識別弱點是連續的程序。 加入弱點掃描整個容器的生命週期：

* 開發管線中的最後一個檢查，您應該在容器上執行弱點掃描之前先將映像推送到公用或私人登錄。 
* 繼續掃描來識別以某種方式在開發期間遺漏的任何瑕疵並解決任何新探索到的弱點可能存在於使用在容器映像的程式碼的登錄中的容器映像。  

### <a name="map-image-vulnerabilities-to-running-containers"></a>對應至執行中的容器映像弱點 

您需要有執行中的容器，因此可以減輕或解決安全性問題的容器映像中所識別的對應弱點的方法。  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>確保只有已核准的映像可用於您的環境 

沒有足夠的變更並不允許無法辨識的容器以及容器生態系統中的變動性。 允許只有已核准的容器映像。 有工具和程序來監視並防止未核准的容器映像使用。 

減少受攻擊面，並防止開發人員犯錯重大安全性的有效方式，就是控制流程的容器映像到您的開發環境。 例如，您可能會批准單一的 Linux 散發套件為基礎的映像，最好是精實 (Alpine 或 CoreOS 而不是 Ubuntu)，以盡量降低潛在攻擊面。 

簽章或指紋識別的映像可以提供可讓您確認容器的完整性的保管鏈結。 例如，Azure Container Registry 支援 Docker[內容信任](https://docs.docker.com/engine/security/trust/content_trust)模型，這可讓登入映像推送至登錄，映像發行者和提取映像的取用者只簽署的映像。

### <a name="permit-only-approved-registries"></a>僅允許經過核准的登錄 

確保您的環境中使用已核准的映像的延伸模組是允許只使用核准的容器登錄庫。 需要使用已核准的容器登錄庫，可減少您暴露於風險限制了未知的漏洞或安全性問題的可能性。 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>確保在整個生命週期的映像的完整性 

管理安全性，整個容器的生命週期的一部分是確保在登錄中的容器映像的完整性，以及它們會改變，或部署至生產環境。 

* 具有弱點，即使是小幅度的映像，則不應該允許在生產環境中執行。 在理想情況下，應該能夠選取一些私用登錄中儲存部署在生產環境中的所有映像。 保留小，才能確保，它們能夠有效管理的生產環境映像數目。

* 由於很難找出軟體從公開可用的容器映像的來源，建置映像，以確保您知道的圖層的原始來源。 當自行建置的容器映像出現弱點，客戶可以很快地找到解決途徑。 使用公用映像，客戶就必須找到公用映像來修正此問題，或從 「 發行者 」 端取得另一個安全的映像的根目錄。 

* 部署在生產環境中徹底掃描映像不保證應用程式的存留期內保持最新狀態。 針對先前未知的映像層或在部署生產環境後才導入的映像層，系統可能會報告它們有安全性弱點。 

  定期稽核生產環境，來識別已過期或尚未更新在一段時間中的映像中部署的映像。 您可以使用藍綠部署方法和輪流升級機制來更新容器映像，而不需要停機。 您可以使用上一節中所述的工具來掃描映像。 

* 使用整合式安全性掃描建置安全的映像，並將其推送至您的私人登錄使用持續整合 (CI) 管線。 CI 解決方案內建的弱點掃描，可確保通過所有測試的映像都會推送至用來部署生產工作負載的私人登錄。 

  CI 管線失敗可確保易受攻擊的映像不會推送到用於部署生產工作負載的私人登錄。 它也會自動化映像安全性掃描是否有大量映像。 否則，手動稽核映像中是否有安全性弱點會是一件痛苦、冗長又容易出錯的工作。 

### <a name="enforce-least-privileges-in-runtime"></a>強制執行執行階段中的最低權限 

最低權限的概念是基本的安全性最佳作法也適用於容器。 時的弱點可能會遭人利用，它通常會允許攻擊者存取和權限等同的入侵的應用程式或處理程序。 確保容器操作的最低權限，並降低風險暴露在完成工作所需存取權。 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>移除不必要的權限以縮小容器受攻擊面 

您也可以減少潛在的攻擊面，容器執行階段中移除任何未使用或不必要的程序或權限。 以 root 身分執行特殊權限的容器。 如果惡意使用者或工作負載中的逸出的特殊權限的容器，容器會再以 root 身分執行該系統上。

### <a name="whitelist-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>列入允許清單的檔案和容器可以存取或執行可執行檔 

減少的變數或突發情況，可協助您維護既穩定又可靠的環境。 限制讓他們可以存取的容器或僅預先的執行或列入允許清單的檔案和可執行檔是經證實的方法可以限制暴露於風險。  

它會更容易管理的白名單，實作從一開始時。 允許清單提供控制和管理能力的量值，因為您將了解哪些檔案和可執行檔所需的應用程式正確運作。 

白名單不僅減少受攻擊面，但可以也提供基準異常，並防止 「 吵雜鄰居 」 和容器 breakout 案例的使用案例。 

### <a name="enforce-network-segmentation-on-running-containers"></a>強制執行網路分割上執行容器  

為了協助保護某個子網路中的容器，從另一個子網路的安全性風險，維護網路分割 （或 nano-區隔） 或執行中容器間的隔離。 維護網路分割也必須使用符合合規性目標所需的產業中的容器。  

比方說，協力廠商工具[Aqua](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) nano-區隔提供自動化的方法。 青色監視容器的網路活動，在執行階段。 它會識別至/從其他容器、 服務、 IP 位址和公用網際網路的所有輸入和輸出網路連線。 Nano 分割會自動建立根據受監視的流量。 

### <a name="monitor-container-activity-and-user-access"></a>監視容器活動和使用者存取權 

如同任何 IT 環境中，您應該持續到您的容器生態系統，以快速找出任何可疑或惡意活動監視活動和使用者存取權。 Azure 提供容器監視解決方案，包括：

* [適用於容器的 azure 監視器](../azure-monitor/insights/container-insights-overview.md)來監視您的工作負載部署到裝載在 Azure Kubernetes Service (AKS) 中的 Kubernetes 環境的效能。 適用於容器的 Azure 監視器可藉由透過計量 API 從 Kubernetes 中取得的控制器、節點與容器來收集記憶體與處理器計量，為您提供效能可見度。 

* [Azure 容器監視解決方案](../azure-monitor/insights/containers.md)協助您檢視及管理其他 Docker 和 Windows 容器主機，在單一位置。 例如︰

  * 檢視會顯示與容器搭配使用的命令的詳細的稽核資訊。 
  * 對容器進行檢視及搜尋集中式記錄檔，而不需從遠端檢視 Docker 或 Windows 主機的疑難排解。  
  * 尋找可能會有雜訊且耗用過多的資源，在主機上的容器。
  * 集中式的 CPU、 記憶體、 儲存和適用於容器的網路使用量和效能資訊檢視。  

  解決方案支援包括 Docker Swarm、 DC/OS、 未受管理 Kubernetes、 Service Fabric 和 Red Hat OpenShift 的容器協調器。 

### <a name="monitor-container-resource-activity"></a>監視資源的容器活動 

監視您的資源活動，例如檔案、 網路和您的容器存取其他資源。 監視資源活動和耗用量是適用於效能監視以及基於安全性考量。 

[Azure 監視器](../azure-monitor/overview.md)會透過允許集合計量、活動記錄及診斷記錄，來啟用 Azure 服務的核心監視功能。 例如，活動記錄會告訴您新資源的建立或修改時間。 

系統會提供計量，這些計量可提供不同資源 (甚至是虛擬機器內的作業系統) 的效能統計資料。 您可以在 Azure 入口網站中使用其中一個總管檢視這項資料，並且根據這些計量建立警示。 Azure 監視器提供最快的計量管線 （5 分鐘降至 1 分鐘），因此您應該將它用於時間緊迫的警示和通知。 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>記錄進行稽核的所有容器的系統管理使用者存取 

維護您的容器生態系統中，容器登錄和容器映像系統管理權限的精確的稽核記錄。 這些記錄可能需要的稽核用途，任何安全性事件之後會做為鑑識的辨識項非常有用。 您可以使用[Azure 容器監視解決方案](../azure-monitor/insights/containers.md)可達到這個目的。 

## <a name="next-steps"></a>後續步驟

* 深入了解管理解決方案，從容器弱點[Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/)並[Aqua Security](https://www.aquasec.com/solutions/azure-container-security/)。

* 深入了解[在 Azure 中的容器安全性](https://azure.microsoft.com/resources/container-security-in-microsoft-azure/)。