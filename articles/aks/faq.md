---
title: Azure Kubernetes Service 的常見問題集
description: 提供一些關於 Azure Kubernetes Service 常見問題的解答。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 6/25/2018
ms.author: iainfou
ms.openlocfilehash: ffd81835de82cc5a00b3f6705a7607a51bb3bfa0
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096446"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 的常見問題集

本文將說明關於 Azure Kubernetes Service (AKS) 的常見問題。

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>目前，哪些 Azure 區域提供 Azure Kubernetes Service (AKS)？

- 澳洲東部
- 加拿大中部
- 加拿大東部
- 美國中部
- 美國東部
- 美國東部 2
- 北歐
- 英國南部
- 西歐
- 美國西部
- 美國西部 2

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>安全性更新是否會套用至 AKS 代理程式節點？

Azure 會透過夜間排程將安全性修補程式自動套用至叢集中的節點。 不過，您有責任確保節點已視需要重新啟動。 您有多個執行節點重新啟動的選項：

- 手動、透過 Azure 入口網站，或透過 Azure CLI。
- 藉由升級 AKS 叢集。 叢集會自動升級 [cordon 和 drain 節點](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/)，然後使用最新的 Ubuntu 映像加以備份。 在節點上更新作業系統映像，無需透過在 `az aks upgrade` 指定目前叢集版本以變更 Kubernetes 版本。
- 使用 [Kured](https://github.com/weaveworks/kured)，這是一款針對 Kubernetes 所推出的的開放原始碼重新啟動精靈。 Kured 會以 [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) 執行，並監視每個節點，查看是否有檔案指示需重新啟動。 然後按照先前所述的相同 cordon 和 drain 程序，在整個叢集中協調重新啟動作業。

## <a name="does-aks-support-node-autoscaling"></a>AKS 是否支援節點自動調整？

是，從 Kubernetes 1.10 開始，可透過 [Kubernetes Autoscaler][auto-scaler] 使用自動調整。

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>AKS 是否支援 Kubernetes 角色型存取控制 (RBAC)？

是，從 Azure CLI 或 Azure Resource Manager 範本部署 AKS 叢集時，可以啟用 RBAC。 這項功能即將在 Azure 入口網站推出。

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-this-be-configured"></a>AKS 支援哪些 Kubernetes 許可控制器？ 可以設定這個項目嗎？

AKS 支援下列[許可控制器][admission-controllers]：

* NamespaceLifecycle
* LimitRanger
* ServiceAccount
* DefaultStorageClass
* DefaultTolerationSeconds
* MutatingAdmissionWebhook
* ValidatingAdmissionWebhook
* ResourceQuota
* DenyEscalatingExec
* AlwaysPullImages

目前無法修改 AKS 中的許可控制器清單。

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>可以將 AKS 部署到我現有的虛擬網路嗎？

是，您可以使用[進階網路功能](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/aks/networking-overview.md) \(英文\)，將 AKS 叢集部署到現有的虛擬網路。

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault 是否會與 AKS 整合？

AKS 目前並未原生整合到 Azure Key Vault。 不過，有一些社群解決方案，例如 [Hexadite 的 acs-keyvault-agent t][hexadite]。

## <a name="can-i-run-windows-server-containers-on-aks"></a>我是否可以在 AKS 上執行 Windows Server 容器？

若要執行 Windows Server 容器，您需要執行以 Windows Server 為基礎的節點。 目前在 AKS 中並未提供以 Windows Server 為基礎的節點。 如果您需要在 Azure 中的 Kubernetes 上執行 Windows Server 容器，請參閱 [acs 引擎的說明文件](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/windows.md)。

## <a name="why-are-two-resource-groups-created-with-aks"></a>為何會使用 AKS 建立兩個資源群組？

每個 AKS 部署皆跨越兩個資源群組。 第一個是您所建立的資源群組，其中僅包含 Kubernetes 服務資源。 AKS 資源提供者會在部署期間自動建立第二個資源群組，例如 *MC_myResourceGRoup_myAKSCluster_eastus*。 第二個資源群組包含所有與該叢集相關聯的基礎結構資源，例如虛擬機器、網路功能，以及儲存體。 建立該資源群組是為了簡化資源清除。

如果您想建立與 AKS 叢集搭配使用的資源，例如儲存體帳戶或保留的公用 IP 位址，您應該將這些資源置於自動產生的資源群組中。

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS 是否提供服務等級協定？

在服務等級協定 (SLA) 中，提供者同意就未達到服務等級的情況，補償該項服務費用。 由於 AKS 本身即免費，因此我們並不提供補償費用，亦無任何正式的 SLA。 不過，我們會盡量將 Kubernetes API 伺服器的可用性維持在至少 99.5%。

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
