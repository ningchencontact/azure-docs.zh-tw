---
title: Azure Kubernetes Service (AKS) 的常見問題集
description: 尋找一些 Azure Kubernetes Service (AKS) 的相關常見問題的解答。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/03/2019
ms.author: iainfou
ms.openlocfilehash: 1cc03cbcffc5253e8b357b6702cd21c45740ff81
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514497"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 的常見問題集

本文將說明關於 Azure Kubernetes Service (AKS) 的常見問題。

## <a name="which-azure-regions-currently-provide-aks"></a>哪些 Azure 區域目前提供 AKS？

如需可用區域的完整清單，請參閱 < [AKS 區域和可用性][aks-regions]。

## <a name="does-aks-support-node-autoscaling"></a>AKS 是否支援節點自動調整？

是，自動調整是可透過[Kubernetes autoscaler] [ auto-scaler]從 Kubernetes 1.10 開始。 如需如何手動設定，並使用叢集中自動調整程式的資訊，請參閱[AKS 叢集中自動調整][aks-cluster-autoscale]。

您也可以使用內建的叢集中自動調整程式 （目前在 AKS 中的預覽），來管理節點的縮放比例。 如需詳細資訊，請參閱 <<c0> [ 自動調整規模以符合應用程式需求在 AKS 叢集中][aks-cluster-autoscaler]。

## <a name="does-aks-support-kubernetes-rbac"></a>AKS 是否支援 Kubernetes RBAC？

是，將 Kubernetes 角色型存取控制 (RBAC) 啟用預設情況下，使用 Azure CLI 建立叢集時。 您可以使用 Azure 入口網站或範本所建立的叢集啟用 RBAC。

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>可以將 AKS 部署到我現有的虛擬網路嗎？

是，您可以使用部署到現有的虛擬網路的 AKS 叢集[進階網路功能][aks-advanced-networking]。

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>可以讓 Kubernetes API 伺服器可存取只在我的虛擬網路內？

目前沒有。 Kubernetes API 伺服器會以公用完整網域名稱 (FQDN) 公開。 您也可以使用您的叢集來控制存取[Kubernetes RBAC 與 Azure Active Directory (Azure AD)][aks-rbac-aad]。

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>安全性更新是否會套用至 AKS 代理程式節點？

Azure 會自動套用至您在夜間排程上的叢集中的 Linux 節點的安全性修補程式。 不過，您有責任確保節點重新開機為這些 Linux 所需。 您有數個選項來重新啟動節點：

- 手動、透過 Azure 入口網站，或透過 Azure CLI。
- 藉由升級 AKS 叢集。 叢集升級[cordon 和 drain 節點][ cordon-drain]自動，然後將新節點上線與最新的 Ubuntu 映像和新的修補程式版本或次要的 Kubernetes 版本。 如需詳細資訊，請參閱[升級 AKS 叢集][aks-upgrade]。
- 藉由使用[Kured](https://github.com/weaveworks/kured)，Kubernetes 開放原始碼重新啟動服務精靈。 Kured [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)並監視每個節點，表示需要重新開機的檔案是否存在。 在叢集中，由相同管理 OS 重新開機[cordon 和 drain 程序][ cordon-drain]叢集升級。

如需使用 kured 的詳細資訊，請參閱[將安全性和核心更新套用至 AKS 中的節點][node-updates-kured]。

### <a name="windows-server-nodes"></a>Windows Server 的節點

對於 Windows Server （目前在 AKS 中的預覽） 的節點，Windows Update 不會自動執行，並套用最新的更新。 Windows Update 發行週期和您自己的驗證程序的定期排程，您應該在 AKS 叢集中執行 Windows Server 的節點集區上的升級。 此升級程序會建立執行的最新的 Windows Server 映像和修補程式的節點，然後移除舊的節點。 如需有關此程序的詳細資訊，請參閱 <<c0> [ 升級 AKS 中的節點集區][nodepool-upgrade]。

## <a name="why-are-two-resource-groups-created-with-aks"></a>為何會使用 AKS 建立兩個資源群組？

每個 AKS 部署皆跨越兩個資源群組：

1. 您建立第一個資源群組。 此群組僅包含 Kubernetes 服務資源。 AKS 資源提供者會在部署期間，自動建立第二個資源群組。 舉例來說，第二個資源群組*MC_myResourceGroup_myAKSCluster_eastus*。 如需如何指定的名稱，此第二個資源群組的資訊，請參閱下一節。
1. 第二個資源群組，例如*MC_myResourceGroup_myAKSCluster_eastus*，包含所有與叢集相關聯的基礎結構資源。 這些資源包括 Kubernetes 節點 VM、虛擬網路和儲存體。 此資源群組的目的是為了簡化資源清除。

如果您建立您的 AKS 叢集搭配使用的資源，例如儲存體帳戶或保留公用 IP 位址，將它們放在自動產生的資源群組中。

## <a name="can-i-provide-my-own-name-for-the-aks-infrastructure-resource-group"></a>提供我自己 AKS 基礎結構資源群組的名稱？

是。 根據預設，AKS 資源提供者會自動建立次要的資源群組 (例如*MC_myResourceGroup_myAKSCluster_eastus*) 在部署期間。 為了符合公司原則，您可以提供您自己的名稱，此受管理的叢集 (*MC_* ) 的資源群組。

若要指定您自己的資源群組名稱，請安裝[aks 預覽][ aks-preview-cli] Azure CLI 擴充功能版本*0.3.2*或更新版本。 當您使用建立 AKS 叢集時[az aks 建立][ az-aks-create]命令，使用 *-節點資源群組*參數並指定資源群組的名稱。 如果您[使用 Azure Resource Manager 範本][ aks-rm-template]來部署 AKS 叢集，您可以利用定義的資源群組名稱*nodeResourceGroup*屬性。

* 次要的資源群組會自動建立您自己的訂用帳戶中的 Azure 資源提供者。
* 只有在建立叢集時，您可以指定自訂的資源群組名稱。

當您使用*MC_* 資源群組，請記住，您不能：

* 指定的現有資源群組*MC_* 群組。
* 指定不同的訂用帳戶，如*MC_* 資源群組。
* 變更*MC_* 在建立叢集之後，資源群組名稱。
* 指定在受管理的資源名稱*MC_* 資源群組。
* 修改或刪除的受管理資源內的標記*MC_* 資源群組。 （請參閱下一節中的其他資訊）。

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc-resource-group"></a>可以修改標記與 AKS 資源 MC_ 資源群組中的其他內容嗎？

如果您修改或刪除 Azure 建立的標記和其他資源屬性*MC_* 資源群組中，您可以取得非預期的結果，例如縮放比例和升級的錯誤。 AKS 可讓您建立和修改自訂標籤。 您可以建立或修改自訂標籤，例如，若要將指派商務單位或成本中心。 藉由修改資源*MC_* 在 AKS 叢集中，您會中斷服務等級目標 (SLO)。 如需詳細資訊，請參閱[沒有 AKS 提供服務等級協定？](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>AKS 支援哪些 Kubernetes 許可控制器？ 是否可以新增或移除許可控制器？

AKS 支援下列[許可控制器][admission-controllers]：

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *DenyEscalatingExec*
- *AlwaysPullImages*

目前，您無法修改 AKS 中的許可控制站的清單。

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault 是否會與 AKS 整合？

AKS 目前原生不使用 Azure Key Vault 整合。 不過， [Kubernetes 專案的 Azure 金鑰保存庫 FlexVolume] [ keyvault-flexvolume]可讓您直接從 Kubernetes pod 整合 Key Vault 祕密。

## <a name="can-i-run-windows-server-containers-on-aks"></a>我是否可以在 AKS 上執行 Windows Server 容器？

Windows Server 容器是以預覽形式提供。 若要在 AKS 中執行 Windows Server 容器，您會建立為客體 OS 中執行 Windows Server 的節點集區。 Windows Server 容器可以使用 Windows Server 2019。 若要開始，請參閱[與 Windows Server 的節點集區建立 AKS 叢集][aks-windows-cli]。

節點集區的視窗伺服器支援包含屬於上游 Kubernetes 專案中的 Windows Server 的一些限制。 如需有關這些限制的詳細資訊，請參閱 < [AKS 限制中的 Windows Server 容器][aks-windows-limitations]。

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS 是否提供服務等級協定？

服務等級協定 (SLA)，提供者都會給予客戶資料服務的成本，如果不符合已發佈的服務層級同意。 因為 AKS 是免費，免費可給予，因此 AKS 不正式的 SLA。 不過，AKS 致力於維持至少 99.5%Kubernetes API 伺服器的可用性。

## <a name="why-cant-i-set-maxpods-below-30"></a>為何無法設定下列 30 maxPods？

在 AKS，您可以設定`maxPods`使用 Azure CLI 和 Azure Resource Manager 範本建立叢集時的值。 不過，Kubenet 和 Azure CNI 需要*最小值*（在建立時驗證）：

| 網路功能 | 最小值 | 最大值 |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

由於 AKS 是受管理的服務，我們以部署和管理附加元件和 pod 叢集的一部分。 在過去，使用者可以定義`maxPods`低於執行 （例如，30） 所需的受管理的 pod 的值。 AKS 現在使用下列公式計算的 pod 數目下限: ((maxPods 或 (maxPods * vm_count)) > 受管理的附加元件的 pod 的最小值。

使用者無法覆寫最小值`maxPods`驗證。

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>可以套用 Azure 保留折扣至我的 AKS 代理程式節點嗎？

AKS 代理程式節點計費作為標準的 Azure 虛擬機器，因此，如果您已購買[Azure 保留的項目][ reservation-discounts] AKS 中使用的 VM 大小，這些折扣會自動套用。

<!-- LINKS - internal -->

[aks-regions]: ./quotas-skus-regions.md#region-availability
[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[virtual-kubelet]: virtual-kubelet.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /rest/api/aks/managedclusters/createorupdate#managedcluster
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md
[reservation-discounts]: ../billing/billing-save-compute-costs-reservations.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
