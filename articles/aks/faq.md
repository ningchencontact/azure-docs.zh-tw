---
title: Azure Kubernetes Service 的常見問題集
description: 提供一些關於 Azure Kubernetes Service 常見問題的解答。
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/14/2018
ms.author: nepeters
ms.openlocfilehash: 55006a3f0193c96849c52f87ab01dc13ac0c7a16
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 的常見問題集

本文將說明關於 Azure Kubernetes Service (AKS) 的常見問題。

> [!IMPORTANT]
> Azure Kubernetes Service (AKS) 目前為**預覽**狀態。 若您同意[補充的使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。
>

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>目前，哪些 Azure 區域提供 Azure Kubernetes Service (AKS)？

- 加拿大中部
- 加拿大東部
- 美國中部
- 美國東部
- 東南亞
- 西歐
- 美國西部 2

## <a name="when-will-additional-regions-be-added"></a>何時會新增其他區域？

需求增加時，便會新增其他區域。

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>安全性更新是否會套用至 AKS 代理程式節點？

Azure 會透過夜間排程將安全性修補程式自動套用至叢集中的節點。 不過，您有責任確保節點已視需要重新啟動。 您有多個執行節點重新啟動的選項：

- 手動、透過 Azure 入口網站，或透過 Azure CLI。
- 藉由升級 AKS 叢集。 叢集會自動升級 [cordon 和 drain 節點](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/)，然後使用最新的 Ubuntu 映像加以備份。 在節點上更新作業系統映像，無需透過在 `az aks upgrade` 指定目前叢集版本以變更 Kubernetes 版本。
- 使用 [Kured](https://github.com/weaveworks/kured)，這是一款針對 Kubernetes 所推出的的開放原始碼重新啟動精靈。 Kured 會以 [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) 執行，並監視每個節點，查看是否有檔案指示需重新啟動。 然後按照先前所述的相同的 cordon 和 drain 程序，在整個叢集中協調這些重新啟動作業。

## <a name="do-you-recommend-customers-use-acs-or-aks"></a>是否建議客戶使用 ACS 或 AKS？

雖然 AKS 仍處於預覽狀態，但我們建議使用 ACS-Kubernetes 或 [acs-engine](https://github.com/azure/acs-engine) 建立生產叢集。 使用 AKS 進行概念證明部署，和開發/測試環境。

## <a name="when-will-acs-be-deprecated"></a>何時會取代 ACS？

大約會在 AKS 正式運作時取代 ACS。 在該日期之後，您會有 12 個月的時間可將叢集移轉至 AKS。 在這 12 個月的期間，您可以執行所有 ACS 作業。

## <a name="does-aks-support-node-autoscaling"></a>AKS 是否支援節點自動調整？

不支援節點自動調整，但目前正在規劃。 您可以看一下這個開放原始碼架構的[自動調整實作][auto-scaler]。

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>AKS 是否支援 Kubernetes 角色型存取控制 (RBAC)？

否，AKS 中目前不支援 RBAC，但即將可行。

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>可以將 AKS 部署到我現有的虛擬網路嗎？

無法，這項功能尚無法使用，但即將可行。

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault 是否會與 AKS 整合？

不會，但已在規劃這項整合。 同時，請嘗試 [Hexadite][hexadite] 所提供的下列解決方案。

## <a name="can-i-run-windows-server-containers-on-aks"></a>我是否可以在 AKS 上執行 Windows Server 容器？

不可以，AKS 目前未提供以 Windows Server 為基礎的代理程式節點，因此您無法執行 Windows Server 容器。 如果您需要在 Azure 中的 Kubernetes 上執行 Windows Server 容器，請參閱 [acs 引擎的說明文件](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/windows.md)。

## <a name="why-are-two-resource-groups-created-with-aks"></a>為何會使用 AKS 建立兩個資源群組？

每個 AKS 部署皆跨越兩個資源群組。 第一個是您所建立的資源群組，僅包含 AKS 資源。 AKS 資源提供者會在部署期間自動建立第二個資源群組，例如 *MC_myResourceGRoup_myAKSCluster_eastus*。 第二個資源群組包含所有與該叢集相關聯的基礎結構資源，例如虛擬機器、網路功能，以及儲存體。 建立該資源群組是為了簡化資源清除。

如果您想建立與 AKS 叢集搭配使用的資源，例如儲存體帳戶或保留的公用 IP 位址，您應該將這些資源置於自動產生的資源群組中。

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent