---
title: Azure Kubernetes Service (AKS) 的支援政策
description: 了解 Azure Kubernetes Service (AKS) 的支援政策、 共同的責任，以及預覽 （或 alpha 或 beta） 中的功能。
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 04/01/2019
ms.author: jenoller
ms.openlocfilehash: 0d2c080be727d2ae13d6d9e5274f17cadffbe640
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65786467"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Azure Kubernetes 服務的支援原則

本文章提供技術的支援原則和限制的 Azure Kubernetes Service (AKS) 的相關詳細資料。 本文也會詳述背景工作節點管理、 受管理的控制平面元件、 第三方開放原始碼元件和安全性或修補程式管理。

## <a name="service-updates-and-releases"></a>服務更新和版本

* 版本資訊，請參閱[AKS 版本資訊](https://github.com/Azure/AKS/releases)。
* 如需預覽版的功能資訊，請參閱[AKS 預覽功能和相關的專案](https://github.com/Azure/AKS/blob/master/previews.md)。

## <a name="managed-features-in-aks"></a>管理 AKS 中的功能

基底基礎結構即服務 (IaaS) 雲端元件，例如計算或網路元件，讓使用者存取低層級的控制項和自訂選項。 相較之下，AKS 提供周全的 Kubernetes 部署，提供客戶一組常用的設定和所需的功能。 AKS 客戶具備有限的自訂、 部署和其他選項。 這些客戶不需要擔心，或直接管理 Kubernetes 叢集。

有了 AKS，客戶可以取得完全受控*控制平面*。 控制平面會包含所有的元件和客戶的需要來運作，並提供給使用者的 Kubernetes 叢集的服務。 Kubernetes 的所有元件會維護，以及由 Microsoft。

Microsoft 會管理並監視透過控制項窗格的下列元件：

* Kubelet 或 Kubernetes API 伺服器
* Etcd 或相容的索引鍵-值存放區，提供服務品質 (QoS)、 延展性和執行階段
* DNS 服務 （例如，kubernetes dns 或 CoreDNS）
* Kubernetes proxy 或網路

AKS 並非完全受管理的叢集解決方案。 例如背景工作角色節點的部分元件有*責任分擔*，使用者必須協助維護 AKS 叢集。 使用者輸入才能，比方說，套用背景工作節點的作業系統 (OS) 安全性修補程式。

在服務，包括*受控*方面，Microsoft 和 AKS 團隊部署、 運作，而且會負責服務的可用性和功能。 客戶無法改變這些受管理的元件。 Microsoft 會限制以確保一致且可調整的使用者體驗的自訂。 如需可完全自訂的解決方案，請參閱[AKS 引擎](https://github.com/Azure/aks-engine)。

> [!NOTE]
> AKS 背景工作角色節點會顯示在 Azure 入口網站中，為一般的 Azure IaaS 資源。 但這些虛擬機器會部署到自訂的 Azure 資源群組 (加上 MC\\*)。 可以變更 AKS 背景工作角色節點。 比方說，您可以使用安全殼層 (SSH) 來變更 AKS 背景工作角色節點變更一般虛擬機器的方式 （不過，您不能變更基底的 OS 映像，並變更不會保存透過更新，或不可能重新開機），以及您可以將其他 Azure 資源附加到 AKS背景工作角色節點。 當您進行變更，但是*頻外管理和自訂，* AKS 叢集可能會變得不受支援。 避免變更背景工作角色節點，除非 Microsoft 支援服務將引導您進行變更。

## <a name="shared-responsibility"></a>共同責任

建立叢集時，客戶會定義 AKS 建立 Kubernetes 背景工作角色節點。 兩個節點上執行客戶工作負載。 客戶擁有及可以檢視或修改的背景工作節點。

由於客戶叢集節點執行私用程式碼，並儲存機密資料，Microsoft 支援服務可以存取它們在只有有限的情況。 Microsoft 支援服務無法登入、 執行命令，或檢視記錄檔，而不需要快速的客戶權限或協助這些節點。

因為背景工作角色節點是機密，Microsoft 將會十分小心，以限制其背景管理。 在許多情況下，您的工作負載將會繼續執行，即使 Kubernetes 主要節點、 etcd，以及其他 Microsoft 管理元件失敗。 而修改過的背景工作節點可能會導致遺失資料和工作負載，並可使叢集不受支援。

## <a name="aks-support-coverage"></a>AKS 支援涵蓋範圍

Microsoft 提供下列技術支援：

* Kubernetes 服務提供及支援，例如 API 伺服器的所有 Kubernetes 元件的連線。
* 管理、 執行時間、 QoS 和作業的 Kubernetes 控制平面服務 （Kubernetes 的主要節點、 API 伺服器、 etcd 和 kubernetes dns，例如）。
* Etcd。 支援包括透明 etcd 的所有資料的自動化備份每隔 30 分鐘的時間災害規劃和叢集狀態還原。 這些備份不可以直接提供給客戶或使用者。 它們可確保資料可靠性與一致性。
* Kubernetes 的 Azure 雲端提供者驅動程式的任何整合點為單位。 這些包括整合至其他 Azure 服務，例如負載平衡器、 永續性磁碟區，或是網路 （Kubernetes 和 Azure CNI）。
* 問題或自訂控制項平面元件，例如 Kubernetes API 伺服器、 etcd 和 kubernetes dns 相關的問題。
* 網路功能，例如 Azure CNI、 kubenet，或其他網路存取和功能問題的相關問題。 問題可能包括 DNS 解析、 封包遺失、 路由、 等等。 Microsoft 支援各種網路功能案例：
  * Kubenet （基本） 和進階叢集內的網路功能 (Azure CNI) 和相關聯的元件
  * 連線至其他 Azure 服務和應用程式
  * 輸入控制器和輸入或負載平衡器組態
  * 網路效能和延遲

Microsoft 不提供下列的技術支援：

* 有關如何使用 Kubernetes 的問題。 例如，Microsoft 支援服務不提供有關如何建立自訂輸入控制器，請使用應用程式工作負載，或適用於第三方或開放原始碼軟體套件或工具的建議。
  > [!NOTE]
  > 在 AKS 叢集功能、 自訂和調整 （例如 Kubernetes 作業問題和程序），可以通知 Microsoft 支援服務。
* 不提供 Kubernetes 一部分的第三方開放原始碼專案控制平面，或部署 AKS 叢集。 這些專案可能包含 Istio、 Helm、 Envoy 中，或其他項目。
  > [!NOTE]
  > Microsoft 可以提供最佳支援 Kured Helm 等第三方開放原始碼專案。 在與 Kubernetes 的 Azure 雲端提供者或其他 AKS 特定錯誤，整合第三方開放原始碼工具，Microsoft 會支援範例和 Microsoft 文件中的應用程式。
* 第三方關閉原始碼軟體。 此軟體可以包含安全掃描工具和網路裝置或軟體。
* 關於上互別苗頭或多組建報告的問題。 例如，Microsoft 不支援執行同盟 multipublic 雲端廠商解決方案相關的問題。
* 網路中所列以外的自訂[AKS 文件](https://docs.microsoft.com/azure/aks/)。
  > [!NOTE]
  > Microsoft 支援問題和相關網路安全性群組 (Nsg) 的 bug。 例如，Microsoft 支援服務可以回答有關更新 NSG 失敗或未預期的 NSG 或負載平衡器行為的問題。

## <a name="aks-support-coverage-for-worker-nodes"></a>AKS 支援涵蓋範圍的背景工作角色節點

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>Microsoft 的責任，AKS 背景工作節點

Microsoft 與客戶分享責任 Kubernetes 背景工作角色節點的位置：

* 基本 OS 映像有必要新增項目 （例如監視和網路代理程式）。
* 背景工作角色節點會自動收到 OS 修補程式。
* 使用 Kubernetes 的問題會控制在背景工作節點執行的元件會自動進行補救的平面。 元件包括下列各項：
  * Kube-proxy
  * kubernetes 提供通訊路徑的網路通道主要元件
  * Kubelet
  * Docker 或白鯨精靈

> [!NOTE]
> 背景工作節點上，如果控制項平面元件處於非正常運作，AKS 小組可能需要重新啟動整個背景工作節點。 由於其客戶的作用中工作負載和資料的限制存取權，AKS 小組重新啟動背景工作角色節點，客戶呈報此問題時，才。 可能的話，AKS 小組運作，以防止會影響應用程式需要重新開機。

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>AKS 背景工作角色節點的客戶責任

Microsoft 不會自動重新啟動背景工作節點，以套用 OS 層級修補程式。 OS 修補程式會傳遞至背景工作角色節點中，雖然*客戶*會負責重新啟動背景工作節點，以套用變更。 自動修補共用程式庫，例如混合式固態磁碟機 (SSHD)，精靈和其他元件的系統或作業系統層級。

客戶須負責執行 Kubernetes 升級。 也可以執行的 Azure 控制台或 Azure CLI 透過升級。 這適用於包含安全性或 Kubernetes 的功能增強功能的更新。

> [!NOTE]
> 因為 AKS*受管理的服務*，其最終目標包括移除負責修補程式更新，以及記錄檔以進行服務管理，更完整且免動手的集合。 端對端管理的服務的容量增加時，未來版本可能會省略某些函式 （例如，節點重新開機和自動修補功能）。

### <a name="security-issues-and-patching"></a>安全性問題和修補

如果一或多個元件的 AKS 中找到安全性缺陷，AKS 小組將修補所有受影響的叢集，以解決問題。 或者，小組會為使用者提供的升級指導方針。

安全性弱點影響，如果零停機時間修補程式，仍可使用的背景工作節點，AKS 小組會套用該修補程式，並通知使用者的變更。

當安全性修補程式會要求背景工作節點重新開機時，Microsoft 會通知這項需求的客戶。 客戶須負責重新開機，或若要取得叢集修補程式更新。 如果使用者未套用的修補程式，根據 AKS 指引，其叢集仍很容易受到安全性問題。

### <a name="node-maintenance-and-access"></a>節點維護及存取

背景工作角色節點是共同的責任，並由客戶所擁有。 因此，客戶能夠登入其背景工作角色節點，並進行可能會造成損害的變更，例如核心更新並安裝或移除套件。

如果客戶進行破壞性的變更，或導致控制項平面離線或變成無法運作的元件，AKS 會偵測此失敗，並會自動還原到先前的工作狀態的 背景工作角色節點。

雖然客戶可以登入，並變更背景工作角色節點，執行此動作會建議因為變更會使叢集不受支援。

## <a name="network-ports-access-and-nsgs"></a>網路連接埠、 存取和 Nsg

做為受管理的服務，AKS 會有特定的網路和連線需求。 這些需求是彈性不如一般 IaaS 元件的需求。 在 AKS，作業，例如自訂 NSG 規則，封鎖特定連接埠 （例如，使用防火牆規則封鎖輸出連接埠 443），而 Url 置於允許清單可以讓您的叢集不受支援。

> [!NOTE]
> 目前，AKS 不允許您完全鎖住您的叢集 （例如，明確的網域或連接埠設為允許清單） 的輸出。 Url 和連接埠的清單可能會變更而不發出警告。 您可以建立一個 Azure 支援票證，以取得更新的清單。 清單是只針對願意接受其叢集可用性可能會受到影響的客戶*在任何時間。*

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>不支援的 alpha 和 beta 版 Kubernetes 功能

AKS 支援穩定功能只在上游 Kubernetes 專案內。 除非另有說明，AKS 不支援 alpha 和 beta 版中的上游 Kubernetes 專案可用的功能。

在兩個案例中，alpha 版本或 beta 版的功能可能會推出先將它們正式推出：

* 客戶符合與 AKS 產品、 支援或工程小組，且已被要求若要試用這些新功能。
* 這些功能已被[啟用的功能旗標加以](https://github.com/Azure/AKS/blob/master/previews.md)。 客戶必須明確地選擇要使用這些功能。

## <a name="preview-features-or-feature-flags"></a>預覽功能或功能旗標

功能，需要擴充的測試與使用者意見反應，Microsoft 發行新的預覽功能或功能旗標後方的功能。 這些功能視為發行前版本或 beta 版的功能。

預覽功能旗標不適用於生產環境。 進行中的變更，在 Api 和行為、 bug 修正和其他變更可能會導致不穩定的叢集和停機時間。

這些功能處於預覽狀態並不打算實際執行和在營業時間內只支援由 AKS 技術支援小組，現供公開預覽的功能將會位於 '盡力' 支援。 如需詳細資訊請參閱：

* [Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> 在 Azure 預覽功能才會生效*訂用帳戶*層級。 請勿在生產訂用帳戶上安裝預覽功能。 生產訂用帳戶，預覽功能可以變更預設 API 行為，並會影響正常的作業。

## <a name="upstream-bugs-and-issues"></a>上游的 bug 和問題

上游 Kubernetes 專案中指定的開發速度，bug 無可避免地會發生。 這些錯誤的某些無法修補或解決 AKS 系統內。 相反地，bug 修正程式需要較大的修補程式 （例如 Kubernetes、 節點或背景工作角色的作業系統及核心） 的上游專案。 Microsoft 擁有 （如 Azure 雲端提供者） 的元件，AKS 和 Azure 的人員會致力於修正社群中的上游的問題。

技術支援問題是由一或多個上游錯誤根本原因，AKS 支援 」 和 「 工程小組將會：

* 找出並連結任何支援的詳細資料，以協助說明為什麼此問題影響您的叢集或工作負載與上游的 bug。 客戶會收到所需的存放庫的連結，讓他們可以監看問題，並查看新的版本提供修正的時。
* 提供可能的因應措施或緩和措施。 如果可以降低此問題，[已知問題](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue)將 AKS 儲存機制中提出。 已知問題歸檔說明：
  * 此問題，包括連結到上游的錯誤。
  * 因應措施和詳細的升級或另一個解決方案的持續性。
  * 粗略的問題包含，根據上游發行頻率愈來愈快的時間軸。
