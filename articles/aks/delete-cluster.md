---
title: 刪除 Azure Kubernetes Service (AKS) 叢集
description: 使用 CLI 或 Azure 入口網站來刪除 AKS 叢集。
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/05/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: e006466d1450471900a8635c49d3bc6c3a73d476
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="delete-an-azure-kubernetes-service-aks-cluster"></a>刪除 Azure Kubernetes Service (AKS) 叢集

刪除 Azure Kubernetes Service 叢集時，會保留叢集部署所在的資源群組，但會刪除所有 AKS 相關資源。 本文件說明如何使用 Azure CLI 和 Azure 入口網站來刪除 AKS 叢集。

除了刪除叢集之外，也可以刪除叢集部署所在的資源群組，這也會一併刪除 AKS 叢集。

## <a name="azure-cli"></a>Azure CLI

使用 [az aks delete][az-aks-delete] 命令來刪除 AKS 叢集。

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

以下是使用 `az aks delete` 命令時可用的選項。

| 引數 | 說明 | 必要 |
|---|---|:---:|
| `--name` `-n` | 受控叢集的資源名稱。 | 是 |
| `--resource-group` `-g` | Azure Kubernetes Service 資源群組的名稱。 | 是 |
| `--no-wait` | 不等候長時間執行的作業完成。 | no |
| `--yes` `-y` | 不提示確認。 | no |

## <a name="azure-portal"></a>Azure 入口網站

在 Azure 入口網站中，瀏覽至包含 Azure Kubernetes Service (AKS) 資源的資源群組，然後按一下 [刪除]。 系統會提示您確認刪除作業。

![刪除 AKS 叢集入口網站](media/container-service-delete-cluster/delete-aks-portal.png)

<!-- LINKS - internal -->
[az-aks-delete]: /cli/azure/aks?view=azure-cli-latest#az_aks_delete