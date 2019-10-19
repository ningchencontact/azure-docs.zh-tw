---
title: 如何啟用容器的 Azure 監視器 |Microsoft Docs
description: 本文說明如何啟用和設定容器的 Azure 監視器，讓您可以瞭解容器的執行方式，以及已識別出哪些效能相關問題。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/12/2019
ms.openlocfilehash: 44cdc2d6b93ac9a62f96875ca6c679fbb97d85a9
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555394"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>如何啟用容器的 Azure 監視器

本文概述適用于容器的設定 Azure 監視器的選項，以監視部署至 Kubernetes 環境並裝載于[Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/)的工作負載效能。

使用下列受支援的方法，即可為全新、一或多個現有的 AKS 部署啟用適用於容器的 Azure 監視器：

* 從 Azure 入口網站、Azure PowerShell 或 Azure CLI
* 使用 [Terraform 和 AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必要條件
開始之前，請確定您有下列項目：

* **Log Analytics 工作區。**

    容器的 Azure 監視器支援[依區域](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)在 Azure 產品中列出的區域中的 Log Analytics 工作區。

    當您啟用新 AKS 叢集的監視，或讓上線體驗在 AKS 叢集訂用帳戶的預設資源群組中建立預設工作區時，您可以建立工作區。 若選擇自行建立它 ，您可以透過 [Azure Resource Manager](../platform/template-workspace-configuration.md)、透過 [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)，或是在 [Azure 入口網站](../learn/quick-create-workspace.md)中建立它。 如需預設工作區所支援對應配對的清單，請參閱[容器 Azure 監視器的區域對應](container-insights-region-mapping.md)。

* 您是**Log Analytics 參與者角色**的成員，可啟用容器監視。 如需有關如何控制 Log Analytics 工作區存取的詳細資訊，請參閱[管理工作區](../platform/manage-access.md)。

* 您是 AKS 叢集資源上 [ **[擁有](../../role-based-access-control/built-in-roles.md#owner)** 者] 角色的成員。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* 預設不會收集 Prometheus 計量。 在設定[代理程式](container-insights-agent-config.md)來收集它們之前，請務必先參閱 Prometheus[檔](https://prometheus.io/)，以瞭解您可以定義的內容。

## <a name="components"></a>元件

監視效能的能力依賴適用於 Linux 的容器化 Log Analytics 代理程式，此代理程式特別為適用於容器的 Azure 監視器所開發。 這個特製化的代理程式會收集叢集中所有節點的效能和事件資料，且會自動部署並在部署期間向指定的 Log Analytics 工作區進行註冊。 代理程式版本是 microsoft/oms:ciprod04202018 或更新版本，且會以下列格式的日期呈現：*mmddyyyy*。

>[!NOTE]
>有了 AKS 的 Windows Server 支援預覽版本，具有 Windows Server 節點的 AKS 叢集並不會安裝代理程式來收集資料並轉送到 Azure 監視器。 相反地，在叢集中自動部署為標準部署一部分的 Linux 節點會收集資料，並將其轉送到叢集中的所有 Windows 節點 Azure 監視器。  
>

發行代理程式的新版本時，代理程式會在您裝載於 Azure Kubernetes Service (AKS) 的受控 Kubernetes 叢集上自動升級。 若要遵循所發行的版本，請參閱[代理程式發行公告](https://github.com/microsoft/docker-provider/tree/ci_feature_prod) \(英文\)。

>[!NOTE]
>如果您已部署 AKS 叢集，則可以使用 Azure CLI 或提供的 Azure Resource Manager 範本來啟用監視，如此文章稍後所示。 您無法使用 `kubectl` 來生集、刪除、重新部署或部署此代理程式。
>範本必須部署在叢集所在的資源群組。

您可以使用下表所述的下列其中一種方法來啟用容器的 Azure 監視器。

| 部署狀態 | 方法 | 描述 |
|------------------|--------|-------------|
| 新增 AKS 叢集 | [使用 Azure CLI 建立叢集](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| 您可以啟用使用 Azure CLI 建立的新 AKS 叢集的監視。 |
| | [使用 Terraform 建立叢集](container-insights-enable-new-cluster.md#enable-using-terraform)| 您可以使用開放原始碼工具 Terraform，啟用您所建立之新 AKS 叢集的監視。 |
| 現有的 AKS 叢集 | [使用 Azure CLI 啟用](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | 您可以使用 Azure CLI 來啟用已部署之 AKS 叢集的監視。 |
| |[啟用使用 Terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | 您可以使用開放原始碼工具 Terraform，啟用已部署之 AKS 叢集的監視。 |
| | [從 Azure 監視器啟用](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| 您可以從 Azure 監視器中的 [AKS 多重叢集] 頁面，啟用監視已部署的一或多個 AKS 叢集。 |
| | [從 AKS 叢集啟用](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| 您可以直接從 Azure 入口網站中的 AKS 叢集啟用監視。 |
| | [使用 Azure Resource Manager 範本啟用](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| 您可以使用預先設定的 Azure Resource Manager 範本來啟用 AKS 叢集的監視。 |

## <a name="next-steps"></a>後續步驟

* 藉由啟用監視來擷取 AKS 叢集節點和 Pod 的健康情況計量，這些健康情況計量都可在 Azure 入口網站中取得。 若要了解如何使用適用於容器的 Azure 監視器，請參閱[檢視 Azure Kubernetes Service 健康情況](container-insights-analyze.md)。
