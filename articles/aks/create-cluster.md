---
title: "建立 Azure Container Service (AKS) 叢集"
description: "使用 CLI 或 Azure 入口網站來建立 AKS 叢集。"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 02/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 37d6dfc0aa6b3e4fcd88a53e83a3a3d7f2157681
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/23/2018
---
# <a name="create-an-azure-container-service-aks-cluster"></a>建立 Azure Container Service (AKS) 叢集

您可以使用 Azure CLI 或 Azure 入口網站來建立 Azure Container Service (AKS) 叢集。

## <a name="azure-cli"></a>Azure CLI

使用 [az aks create][az-aks-create] 命令來建立 AKS 叢集。

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster
```

以下是使用 `az aks create` 命令時可用的選項。

| 引數 | 說明 | 必要 |
|---|---|:---:|
| `--name` `-n` | 受控叢集的資源名稱。 | 是 |
| `--resource-group` `-g` | Azure Container Service 資源群組的名稱。 | 是 |
| `--admin-username` `-u` | Linux 虛擬機器的使用者名稱。  預設值：azureuser。 | no |
| ` --client-secret` | 與服務主體關聯的密碼。 | no |
| `--dns-name-prefix` `-p` | 叢集公用 IP 位址的 DNS 首碼。 | no |
| `--generate-ssh-keys` | 產生 SSH 公開金鑰和私密金鑰檔案 (如果遺失)。 | no |
| `--kubernetes-version` `-k` | 要用於建立叢集的 Kubernetes 版本，例如 '1.7.9' 或 '1.8.2'。  預設值：1.7.7。 | no |
| `--no-wait` | 不等候長時間執行的作業完成。 | no |
| `--node-count` `-c` | 節點集區的預設節點數目。  預設值：3。 | no |
| `--node-osdisk-size` | 節點集區虛擬機器的 osDisk 大小 (單位為 GB)。 | no |
| `--node-vm-size` `-s` | 虛擬機器的大小。  預設值：Standard_D1_v2。 | no |
| `--service-principal` | 用於叢集驗證的服務主體。 | no |
| `--ssh-key-value` | SSH 金鑰檔值或金鑰檔路徑。  預設值：~/.ssh/id_rsa.pub。 | no |
| `--tags` | 格式為 'key[=value]' 的空格分隔標記。 請使用 '' 來清除現有的標記。 | no |

## <a name="azure-portal"></a>Azure 入口網站

如需有關使用 Azure 入口網站來部署 AKS 叢集的指示，請參閱 Azure Container Service (AKS) [Azure 入口網站快速入門][aks-portal-quickstart]。 

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az_aks_create
[aks-portal-quickstart]: kubernetes-walkthrough-portal.md
