---
title: Azure Kubernetes Service (AKS) 的支援原則
description: 深入了解 Azure Kubernetes Service (AKS) 的支援政策、 共用的責任、 預覽/Alpha/beta 版的功能。
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 04/01/2019
ms.author: jenoller
ms.openlocfilehash: f173fc7c794729eae8c60cceefa88d153800a816
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032464"
---
# <a name="azure-kubernetes-service-aks-support-policies"></a>Azure Kubernetes Service (AKS) 的支援原則

這篇文章提供有關 AKS 技術的支援原則，限制，詳細資料和詳細資料，包括受管理的背景工作節點管理控制平面元件、 第三方開放原始碼元件，以及安全性 / 修補程式管理。

## <a name="service-updates--releases"></a>服務更新與版本

* 版本資訊，請參閱[AKS 版本資訊][1]
* 如需公開預覽功能，請參閱[AKS 預覽功能和相關的專案][2]

## <a name="what-is-managed"></a>什麼是 「 受管理 」

不同於基底 IaaS 雲端元件，例如計算或網路功能，它們會公開低層級的控制項和自訂選項，讓使用者運用，AKS 服務會提供周全的 Kubernetes 部署，代表一組通用的組態和針對 Kubernetes 所需的功能。 使用這項服務的客戶會有有限的數目的自訂、 部署和其他選項。 這也表示，客戶不需要擔心，或直接管理 Kubernetes 叢集。

有了 AKS，客戶可以取得完全受控**控制平面**– 控制平面其中包含所有的元件和服務才能運作，並提供給使用者的 Kubernetes 叢集。 Kubernetes 的所有元件會維護，以及由 Microsoft。

與 managed**控制平面**下列元件所管理且由 Microsoft 監視：

* Kubelet / Kubernetes API 伺服器
* Etcd 或相容的索引鍵/值存放區 – 包括服務、 延展性和執行階段的品質
* DNS 服務 (例如，kubernetes dns / CoreDNS)
* Kubernetes Proxy/網路

AKS 不是 100 %managed**叢集**解決方案。 某些元件 （例如背景工作角色節點） 具有特定**共用責任**維護 AKS 叢集的動作，需要使用者互動的情況。 例如，背景工作角色節點 OS 安全性修補程式的應用程式。

 **管理**，Microsoft 和 AKS 小組部署的方式運作，以及負責的可用性和這些服務的功能。 **客戶無法改變這些元件**。 自訂僅限於確保一致且可調整的使用者體驗。 如需可完全自訂的解決方案，請參閱[AKS 引擎](https://github.com/Azure/aks-engine)。

> [!NOTE]
> 請務必了解 Azure Kubernetes 服務背景工作角色節點顯示在 Azure 入口網站中，做為一般的 Azure IaaS 資源，雖然這些虛擬機器會部署到自訂的 Azure 資源群組 (加上 MC\\*)。 使用者可能會改變，SSH 到它們就像一般的虛擬機器 （不過，您不能變更基底的 OS 映像，並變更不會保存透過更新，或不可能重新開機），而且您可以連接其他 Azure 資源，或修改它們。 **不過，這樣的頻外管理和自訂，表示，AKS 叢集本身可能會變得不受支援。除非 Microsoft 支援服務的指示，請避免背景工作節點的改變。**

## <a name="what-is-shared-responsibility"></a>什麼被共用的責任

在叢集建立時，AKS 建立 Kubernetes 由客戶定義的背景工作節點的數字。 這些節點，如所述，會執行客戶工作負載。 客戶擁有及可以檢視或修改這些背景工作角色節點。

因為這些節點會執行私用程式碼，並儲存機密資料，Microsoft 支援人員**有限存取**客戶所有叢集節點。 Microsoft 支援服務無法登入、 執行命令，或檢視記錄檔，這些節點不 express 的客戶權限及/或協助支援小組的指示，請執行偵錯命令。

由於這些背景工作角色節點的機密本質，Microsoft 會需要十分小心，以限制這些節點的 '幕後' 管理。 即使 Kubernetes 主要節點、 etcd 和其他元件失敗，（由 Microsoft 管理） 您的工作負載將繼續在許多情況下執行。 如果不小心修改背景工作角色節點，它就可能會導致資料及/或工作負載損失，並使叢集不受支援。

## <a name="azure-kubernetes-service-support-coverage"></a>Azure Kubernetes 服務支援涵蓋範圍

**Microsoft 提供下列技術支援：**

* 連線到 Kubernetes 的所有元件提供及支援 Kubernetes 服務 （例如 API 伺服器中）
* 管理、 執行時間、 QoS 及營運 Kubernetes 控制平面服務 (Kubernetes 主要 API 伺服器的節點 etcd，例如 kube dns。
* Etcd 支援包括透明 etcd 的所有資料的自動化備份每隔 30 分鐘的時間災害規劃和叢集狀態還原。 這些備份不提供直接向客戶/使用者，以及用來確保資料可靠性與一致性
* 任何整合點，在 Azure 雲端提供者驅動程式針對 Kubernetes，例如整合至其他 Azure 服務，例如負載平衡器、 永續性磁碟區、 網路功能 （Kubernetes 和 Azure CNI）
* 問題或自訂控制項平面元件，例如 Kubernetes API 伺服器、 etcd 和 kubernetes dns 的相關問題。
* 發出有關網路方面的主題，例如 Azure CNI、 Kubenet 或其他網路存取和功能的問題 （DNS 解析、 封包遺失、 路由等等）。
  * 支援的網路功能案例會包含 Kubenet （基本） 和進階網路功能 (Azure CNI) 內的叢集和相關聯的元件、 其他 Azure 服務和應用程式的連線。 此外，Microsoft 會支援輸入控制器和輸入/負載平衡器組態、 網路效能和延遲。

**Microsoft 不提供下列技術支援：**

* 諮詢 / 「 使用說明 」 使用 Kubernetes 的問題，例如如何建立自訂輸入控制器，應用程式工作負載的問題，以及協力廠商/OSS 封裝或工具不在範圍內。
  * 諮詢票證 AKS 叢集功能，自訂項目，調整 – 例如 Kubernetes 作業問題/tos 作法是在範圍內。
* 第三方開放原始碼專案，不提供 Kubernetes 控制平面，或與 AKS 叢集，例如 Istio、 Helm、 Envoy 中，等部署。
  * Helm 和 Kured，等第三方開放原始碼專案的最佳技術支援提供的範例和應用程式提供 Microsoft 文件中，其中該協力廠商開放原始碼工具整合了 Kubernetes Azure 雲端提供者或其他 AKS 特有的錯誤。
* 第三方關閉原始碼軟體 – 這可以包括掃描工具，網路裝置或軟體的安全性。
* 不支援 「 多重雲端 」 或多個廠商的組建報告相關的問題，例如執行聯合多個公用雲端廠商解決方案不支援。
* 特定網路的自訂設定，除了記載於官方[AKS 文件][3]。
  > [!NOTE]
  > 支援的問題與 bug 有關於網路安全性群組。 例如，支援可以回答周圍 Nsg 無法更新或未預期的 NSG 或負載平衡器行為的問題。

## <a name="azure-kubernetes-service-support-coverage-worker-nodes"></a>Azure Kubernetes 服務支援涵蓋範圍 （背景工作角色節點）

### <a name="microsoft-responsibilities-for-azure-kubernetes-service-worker-nodes"></a>Microsoft Azure Kubernetes 服務背景工作節點的責任

如中所述`shared responsibility` 區段中，Kubernetes 背景工作角色節點分成共同責任模型，其中：

* 提供必要的新增項目 （例如監視和網路代理程式） 的基底作業系統映像
* 自動傳遞給背景工作角色節點的作業系統修補程式
* 自動修復問題，使用 Kubernetes 控制平面元件的背景工作節點上執行，例如：
  * kube-proxy
  * kubernetes 提供通訊路徑的網路通道主要元件
  * kubelet
  * docker/白鯨精靈

> [!NOTE]
> 如果控制平面元件處於背景工作節點上，AKS 小組可能需要重新啟動整個背景工作節點，以解決此問題。 這是代表使用者執行，而且不會執行，除非客戶擴大進行 （因為客戶作用中工作負載和資料存取）。 每當可能 AKS 人員會進行任何必要的工作重新啟動非應用程式的影響。

### <a name="customer-responsibilities-for-azure-kubernetes-service-worker-nodes"></a>Azure Kubernetes 服務背景工作角色節點的客戶責任

**Microsoft 不會執行動作：**

- 自動重新啟動背景工作角色節點的 OS 層級才會生效的修補程式 **（目前，請參閱下文）**

作業系統修補程式會傳遞至背景工作節點，但是它還**客戶有責任**重新啟動背景工作節點，變更才會生效。 此自動修補功能包括共用程式庫，例如 SSHD，精靈和其他系統/OS 層級的元件。

針對 Kubernetes 升級**客戶有責任的升級執行**透過 Azure 控制台或 Azure CLI。 這適用於包含安全性或 Kubernetes 的功能增強功能的更新。

> [!NOTE]
> 因為 AKS`managed service`我們結束的目標服務包括移除修補程式、 更新、 記錄檔收集和一些功能讓它更多的完全受控且自動處理服務的責任。 當我們執行端對端管理的功能增加，可能在未來版本中移除 （節點重新開機、 自動修補），這些項目。

### <a name="security-issues-and-patching"></a>安全性問題和修補

在某些情況下 （例如 Cve) 中，於一或多個 AKS 服務的元件可以找到安全性缺陷。 在此情況下，AKS 將會修補所有受影響的叢集，可能的話，請解決問題，或為使用者提供的升級指導方針。

背景工作節點受到這類安全性漏洞，AKS 小組是否可零停機時間修補程式的問題，會套用該修正，並通知使用者的變更。

如果需要安全性修補程式背景工作角色節點重新開機，Microsoft 會通知這項需求的客戶和客戶需負責執行重新開機，或若要取得其叢集的修補程式更新。 如果使用者不會套用 AKS 指導的修補程式，其叢集仍會受到問題。

### <a name="node-maintenance-and-access"></a>節點維護及存取

由於背景工作角色節點是共同的責任，且在客戶的擁有權之下，客戶就可以登入這些背景工作角色，並移除套件並安裝新的封裝執行可能會造成損害的變更，例如核心更新。

如果客戶執行破壞性動作或觸發程序的動作控制平面元件，以離線或否則將會成為非功能性，AKS 服務會偵測此失敗，並執行還原至先前的工作的背景工作角色節點的自動修復狀態。

雖然客戶可以登入，並改變背景工作角色節點，就*建議您不要使用*因為這可讓您的叢集不受支援。

## <a name="network-ports-access-and-network-security-groups"></a>網路連接埠、 存取和網路安全性群組

做為受管理的服務，AKS 會有特定的網路和連線需求。 這些需求是彈性不如一般 IaaS 元件。 不同於其他 IaaS 的元件，某些作業 （例如自訂的網路安全性群組規則，封鎖特定連接埠、 URL 加入允許清單，以及等等） 可能會呈現您的叢集不受支援 （例如，防火牆規則封鎖輸出連接埠 443）。

> [!NOTE]
> 完全鎖住輸出 （比方說，是明確的網域/連接埠允許清單），從您的叢集不支援的 AKS 案例這一次。 Url 和連接埠清單有所變更，而不發出警告，並可提供 Azure 支援透過票證。 提供的清單是只針對願意接受新密碼的客戶*您叢集的可用性可能會受到影響，在任何時間。*

## <a name="alphabeta-kubernetes-features-not-supported"></a>Alpha/beta 版 Kubernetes 功能 （不支援）

AKS 僅支援上游 Kubernetes 專案內的穩定功能。 不支援上游 Kubernetes 中可用的 alpha/beta 版功能，除非否則通訊，而且記載。

有兩種情況，其中 alpha 版本或 beta 版的功能可能會推出在 GA 之前：

* 客戶已符合與 AKS 產品、 支援或工程團隊，並明確地要求若要試用這些新功能。
* 這些功能已被[透過功能旗標來啟用][ 2] （明確選用功能）

## <a name="preview-features--feature-flags"></a>預覽功能/功能旗標

如需功能和需要擴充的測試，社群和使用者意見反應的功能，Microsoft 將發行新的預覽功能或在功能旗標後方的功能。 這些功能應該視為發行前版本/beta 版，並公開，讓使用者有機會試用這些新功能。

不過，這些預覽/功能旗標功能並不適用於生產環境中使用 – Api、 行為變更、 bug 修正和其他變更可，可能會導致不穩定的叢集和停機時間。 這些功能的支援僅限於錯誤/問題報告。 請勿啟用這些功能的生產系統或訂用帳戶。

> [!NOTE]
> 啟用預覽功能會在 Azure 的生效**訂用帳戶**層級。 因為它可以變更預設 API 行為影響正常的作業，請勿在生產訂用帳戶上安裝預覽功能。

## <a name="upstream-bugs-and-issues"></a>上游的 bug 和問題

上游 Kubernetes 專案中指定的開發速度，有無可避免地，無法修補或運作的因應措施 AKS 在系統內，而是需要較大的修補程式 （例如 Kubernetes、 背景工作節點/Os 和核心） 的上游專案的 bug。 我們擁有 （例如 Azure 雲端提供者） 的元件，AKS/Azure 人員會認可到社群中修正問題上游的。

技術支援問題的根本原因的一個或多個上游錯誤的情況下，AKS 支援，工程會執行下列項目：

* 找出並連結任何支援的詳細資料，說明為什麼這會影響您的叢集和/或工作負載與上游的 bug。 客戶會收到的連結需要監看問題，並查看 當新的 Kubernetes/其他版本將包含 fix(es) 存放庫/問題
* 可能的因應措施或緩和措施：在某些情況下，它或許可以解決問題 – 在此情況下，「[已知問題](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue)」 將會歸類說明 AKS 存放庫：
  * 問題，並且連結到上游的錯誤
  * 因應措施，以及關於升級/其他持續性解決方案的詳細資料
  * 包含根據上游發行頻率愈來愈快的概略時間表

[1]: https://github.com/Azure/AKS/releases
[2]: https://github.com/Azure/AKS/blob/master/previews.md
[3]: https://docs.microsoft.com/azure/aks/
