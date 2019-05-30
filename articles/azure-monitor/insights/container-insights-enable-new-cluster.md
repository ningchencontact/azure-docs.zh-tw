---
title: 監視新的 Azure Kubernetes Service (AKS) 叢集 |Microsoft Docs
description: 了解如何啟用新的 Azure Kubernetes Service (AKS) 叢集使用 Azure 監視器監視容器的訂用帳戶。
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
ms.date: 04/25/2019
ms.author: magoedte
ms.openlocfilehash: d73ab2d5cca4f20f954a0b0e972111d3f395c3c8
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "65077526"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>若要啟用新的 Azure Kubernetes Service (AKS) 叢集的監視

本文說明如何設定適用於容器的 Azure 監視器來監視受管理的 Kubernetes 叢集上裝載[Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/)您準備部署您的訂用帳戶中。

您可以啟用監視 AKS 叢集使用其中一個支援的方法：

* Azure CLI
* Terraform

## <a name="enable-using-azure-cli"></a>啟用使用 Azure CLI

若要使用 Azure CLI 針對建立的新 AKS 叢集啟用監視，請依照快速入門文章中[建立 AKS 叢集](../../aks/kubernetes-walkthrough.md#create-aks-cluster)一節下的步驟執行。  

>[!NOTE]
>如果您選擇使用 Azure CLI，必須先在本機安裝並使用 CLI。 您必須執行 Azure CLI 2.0.59 版或更新版本。 若要知道您使用的版本，請執行 `az --version`。 如果您需要安裝或升級 Azure CLI，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 
>

## <a name="enable-using-terraform"></a>啟用使用 Terraform

如果您[使用 Terraform 部署新的 AKS 叢集](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)，若不選擇指定現有的引數，則可以指定設定檔中所需的引數[以建立 Log Analytics 工作區](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html)。 

>[!NOTE]
>如果您選擇使用 Terraform，則必須執行 Terraform Azure RM Provider 1.17.0 版或以上版本。

若要將適用於容器的 Azure 監視器新增至工作區，請參閱：[azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html)，並透過包含 [**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) 和指定 **oms_agent** 來完成設定檔。 

啟用監視並順利完成所有設定工作之後，您可以透過兩種方式來監視叢集效能：

* 選取左窗格中的 [健康情況]  ，直接在 AKS 叢集中進行。
* 在所選取叢集的 AKS 叢集頁面中，選取 [監視器容器深入解析]  圖格。 在 Azure 監視器中，選取左窗格中的 [健康情況]  。 

  ![在 AKS 中選取適用於容器的 Azure 監視器選項](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

啟用監視之後，可能需要約 15 分鐘的時間才能檢視叢集的健康情況計量。 

## <a name="verify-agent-and-solution-deployment"></a>驗證代理程式和解決方案部署
使用代理程式 *06072018* 版或更新版本時，您可以確認代理程式和方案皆已成功部署。 使用舊版的代理程式時，您只能確認代理程式部署。

### <a name="agent-version-06072018-or-later"></a>代理程式 06072018 版或更新版本
執行下列命令以確認代理程式已成功部署。 

```
kubectl get ds omsagent --namespace=kube-system
```

輸出應該像下面這樣，這表示它已正確部署：

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

若要確認解決方案的部署，請執行下列命令：

```
kubectl get deployment omsagent-rs -n=kube-system
```

輸出應該像下面這樣，這表示它已正確部署：

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>早於 06072018 的代理程式版本

若要確認已正確部署 06072018  版以前發行的 Log Analytics 代理程式，請執行下列命令：  

```
kubectl get ds omsagent --namespace=kube-system
```

輸出應該像下面這樣，這表示它已正確部署：  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>使用 CLI 來檢視設定
使用 `aks show` 命令來取得詳細資料，例如解決方案是否已啟用、Log Analytics 工作區 resourceID 為何，以及有關叢集的詳細資料。  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

在幾分鐘之後，此命令就會完成，並以 JSON 格式傳回叢集的相關資訊。  此命令的結果應該會顯示監視附加元件設定檔，而且應該會類似下列命令輸出：

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>後續步驟

* 如果您在試著將解決方案上線時遇到問題，請檢閱[疑難排解指南](container-insights-troubleshoot.md)

* 藉由啟用監視來擷取 AKS 叢集節點和 Pod 的健康情況計量，這些健康情況計量都可在 Azure 入口網站中取得。 若要了解如何使用適用於容器的 Azure 監視器，請參閱[檢視 Azure Kubernetes Service 健康情況](container-insights-analyze.md)。
