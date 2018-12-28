---
title: 如何升級適用於容器的 Azure 監視器 (預覽) | Microsoft Docs
description: 本文說明如何升級適用於容器的 Azure 監視器所使用的 Log Analytics 代理程式。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: magoedte
ms.openlocfilehash: 08206b9de4fca3b422c5b076d7e0c1c180f82fbd
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184078"
---
# <a name="how-to-upgrade-the-azure-monitor-for-containers-preview-agent"></a>如何升級適用於容器的 Azure 監視器 (預覽) 代理程式
適用於容器的 Azure 監視器會使用適用於 Linux 的 Log Analytics 代理程式容器化版本。 發行代理程式的新版本時，代理程式會在您裝載於 Azure Kubernetes Service (AKS) 的受控 Kubernetes 叢集上自動升級。  

如果代理程式升級失敗，本文將說明手動升級代理程式的程序。 若要遵循所發行的版本，請參閱[代理程式發行公告](https://github.com/microsoft/docker-provider/tree/ci_feature_prod) \(英文\)。   

## <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>在受監視的 Kubernetes 叢集上升級代理程式
升級代理程式的程序由兩個直接簡單的步驟所組成。 第一個步驟是使用 Azure CLI 停用適用於容器的 Azure 監視器所進行的監視。  請依照[停用監視](container-insights-optout.md?toc=%2fazure%2fmonitoring%2ftoc.json#azure-cli)一文中說明的步驟操作。 我們可以使用 Azure CLI 從叢集中的節點移除代理程式，而不會影響解決方案和儲存在工作區中的對應資料。 

>[!NOTE]
>當您執行這項維護活動時，叢集中的節點將不會轉送收集的資料，且在您移除代理程式到安裝新版本的這段時間內，效能檢視不會顯示資料。 
>

若要安裝新版的代理程式，請依照[使監視上線](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-using-azure-cli)一文中說明的步驟，使用 Azure CLI 完成此程序。  

啟用監視之後，可能需要約 15 分鐘的時間才能檢視叢集的更新健康情況計量。 若要確認代理程式已順利升級，請執行下列命令：`kubectl logs omsagent-484hw --namespace=kube-system`

狀態應該類似下列範例，其中 *omi* 和 *omsagent* 的值應符合[代理程式發行歷程記錄](https://github.com/microsoft/docker-provider/tree/ci_feature_prod) \(英文\) 中所指定的最新版本。  

    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.5
    omsagent 1.6.0-163
    docker-cimprov 1.0.0.31

## <a name="next-steps"></a>後續步驟
如果您在升級代理程式時遇到問題，請檢閱[疑難排解指南](container-insights-troubleshoot.md)以取得支援。