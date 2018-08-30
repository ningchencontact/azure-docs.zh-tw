---
title: 建立 Azure Kubernetes Service (AKS) 叢集
description: 使用 CLI 或 Azure 入口網站來建立 AKS 叢集。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6bfe6f9b76693ded79aa9b9d21ddcac4e1a0733e
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2018
ms.locfileid: "43110299"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster"></a>建立 Azure Kubernetes Service (AKS) 叢集

您可以使用 Azure CLI 或 Azure 入口網站來建立 Azure Kubernetes Service (AKS) 叢集。

## <a name="azure-cli"></a>Azure CLI

使用 [az aks create][az-aks-create] 命令來建立 AKS 叢集。

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster
```

以下是使用 `az aks create` 命令時可用的選項。 如需下列各引數的詳細資訊，請參閱 AKS 的 [Azure CLI 參考][az-aks-create]。

| 引數 | 說明 | 必要 |
|---|---|:---:|
| `--name` `-n` | 受控叢集的資源名稱。 | 是 |
| `--resource-group` `-g` | Azure Kubernetes Service 資源群組的名稱。 | 是 |
| `--admin-username` `-u` | Linux 虛擬機器的使用者名稱。  預設值：azureuser。 | no |
| `--aad-client-app-id` | (預覽)「原生」類型的 Azure Active Directory 用戶端應用程式的識別碼。 | no |
| `--aad-server-app-id` | (預覽)「Web 應用程式/API」類型的 Azure Active Directory 伺服器應用程式的識別碼。 | no |
| `--aad-server-app-secret` | (預覽) Azure Active Directory 伺服器應用程式的密碼。 | no |
| `--aad-tenant-id` | (預覽) Azure Active Directory 租用戶的識別碼。 | no |
| `--admin-username` `-u` | 要在節點 VM 上建立以進行 SSH 存取的使用者帳戶。  預設值：azureuser。 | no |
| ` --client-secret` | 與服務主體關聯的密碼。 | no |
| `--dns-name-prefix` `-p` | 叢集公用 IP 位址的 DNS 首碼。 | no |
| `--dns-service-ip` | 指派給 Kubernetes DNS 服務的 IP 位址。 | no |
| `--docker-bridge-address` | 指派給 Docker 橋接器的 IP 位址和網路遮罩。 | no |
| `--enable-addons` `-a` | 啟用逗號分隔清單中的 Kubernetes 附加元件。 | no |
| `--enable-rbac` `-r` | 啟用 Kubernetes 角色型存取控制。 | no |
| `--generate-ssh-keys` | 產生 SSH 公開金鑰和私密金鑰檔案 (如果遺失)。 | no |
| `--kubernetes-version` `-k` | 要用來建立叢集的 Kubernetes 版本，例如 '1.7.9' 或 '1.9.6'。 | no |
| `--location` `-l` | 自動建立的資源群組所在的位置 | no |
| `--max-pods` `-m` | 可部署至節點的 Pod 數目上限。 | no |
| `--network-plugin` | 要使用的 Kubernetes 網路外掛程式。 | no |
| `--no-ssh-key` `-x` | 請勿使用或建立本機 SSH 金鑰。 | no |
| `--no-wait` | 不等候長時間執行的作業完成。 | no |
| `--node-count` `-c` | 節點集區的預設節點數目。  預設值：3。 | no |
| `--node-osdisk-size` | 節點集區虛擬機器的 osDisk 大小 (單位為 GB)。 | no |
| `--node-vm-size` `-s` | 虛擬機器的大小。  預設值：Standard_D1_v2。 | no |
| `--pod-cidr` | 在使用 kubenet 時據以指派 Pod IP 的 CIDR 標記法 IP 範圍。 | no |
| `--service-cidr` | 據以指派服務叢集 IP 的 CIDR 標記法 IP 範圍。 | no |
| `--service-principal` | 用於叢集驗證的服務主體。 | no |
| `--ssh-key-value` | SSH 金鑰檔值或金鑰檔路徑。  預設值：~/.ssh/id_rsa.pub。 | no |
| `--tags` | 格式為 'key[=value]' 的空格分隔標記。 請使用 '' 來清除現有的標記。 | no |
| `--vnet-subnet-id` | 在現有的 VNet 中要部署叢集的子網路所具備的識別碼。 | no |
| `--workspace-resource-id` | 要用來儲存監視資料的現有 Log Analytics 工作區的資源識別碼。 | no |

## <a name="azure-portal"></a>Azure 入口網站

如需有關使用 Azure 入口網站來部署 AKS 叢集的指示，請參閱 Azure Kubernetes Service (AKS) [Azure 入口網站快速入門][aks-portal-quickstart]。

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-portal-quickstart]: kubernetes-walkthrough-portal.md
