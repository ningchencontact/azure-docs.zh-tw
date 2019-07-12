---
title: 在 Azure Kubernetes Service (AKS) 中檢視 kubelet 記錄
description: 了解如何檢視 Azure Kubernetes Service (AKS) 節點 kubelet 記錄中的疑難排解資訊
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/05/2019
ms.author: mlearned
ms.openlocfilehash: 65b16b3ddc209ef5d2f6287a04cfe402c3b205c6
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615172"
---
# <a name="get-kubelet-logs-from-azure-kubernetes-service-aks-cluster-nodes"></a>從 Azure Kubernetes Service (AKS) 叢集節點取得 kubelet 記錄

在操作 AKS 叢集，您可能需要檢閱的問題進行疑難排解的記錄檔。 內建於 Azure 入口網站是以檢視記錄檔的能力[AKS 主要元件][aks-master-logs] or [containers in an AKS cluster][azure-container-logs]。 有時候，您可能需要取得*kubelet* AKS 節點以進行疑難排解的記錄檔。

本文說明如何使用`journalctl`若要檢視*kubelet* AKS 節點上的記錄。

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱 AKS 快速入門[使用 Azure CLI][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal]。

## <a name="create-an-ssh-connection"></a>建立 SSH 連線

首先，請針對您需要檢視 kubelet  記錄的節點建立 SSH 連線。 這項作業會詳細說明[透過 ssh 連線到 Azure Kubernetes Service (AKS) 叢集節點][aks-ssh]文件。

## <a name="get-kubelet-logs"></a>取得 kubelet 記錄

一旦您已連線到節點後，請執行下列命令以提取 kubelet  記錄：

```console
sudo journalctl -u kubelet -o cat
```

下列範例輸出顯示 kubelet  記錄資料：

```
I0508 12:26:17.905042    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:27.943494    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:28.920125    8672 server.go:796] GET /stats/summary: (10.370874ms) 200 [[Ruby] 10.244.0.2:52292]
I0508 12:26:37.964650    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:47.996449    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:26:58.019746    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:05.107680    8672 server.go:796] GET /stats/summary/: (24.853838ms) 200 [[Go-http-client/1.1] 10.244.0.3:44660]
I0508 12:27:08.041736    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:18.068505    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:28.094889    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:38.121346    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:44.015205    8672 server.go:796] GET /stats/summary: (30.236824ms) 200 [[Ruby] 10.244.0.2:52588]
I0508 12:27:48.145640    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:27:58.178534    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:05.040375    8672 server.go:796] GET /stats/summary/: (27.78503ms) 200 [[Go-http-client/1.1] 10.244.0.3:44660]
I0508 12:28:08.214158    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:18.242160    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:28.274408    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:38.296074    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:48.321952    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
I0508 12:28:58.344656    8672 kubelet_node_status.go:497] Using Node Hostname from cloudprovider: "aks-agentpool-11482510-0"
```

## <a name="next-steps"></a>後續步驟

如果您需要從 Kubernetes 主機的其他疑難排解資訊，請參閱[檢視主要節點會記錄在 AKS 的 Kubernetes][aks-master-logs]。

<!-- LINKS - internal -->
[aks-ssh]: ssh.md
[aks-master-logs]: view-master-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-master-logs]: view-master-logs.md
[azure-container-logs]: ../azure-monitor/insights/container-insights-overview.md
