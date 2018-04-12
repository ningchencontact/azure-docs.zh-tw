---
title: Azure CLI 2.0 範例 - 安裝應用程式 | Microsoft Docs
description: Azure CLI 2.0 範例
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 22c2f8d811da3dfc565ff32cb602e78b1097ffab
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2018
---
# <a name="install-applications-into-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>使用 Azure CLI 2.0 將應用程式安裝到虛擬機器擴展集
此指令碼會建立執行 Ubuntu 的虛擬機器擴展集，並使用自訂指令碼擴充功能來安裝基本 Web 應用程式。 執行指令碼之後，您就可以透過網頁瀏覽器來存取 Web 應用程式。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/install-apps/install-apps.sh "Install apps into a scale set")]

## <a name="clean-up-deployment"></a>清除部署
執行下列命令來移除資源群組、擴展集和所有相關資源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明
此指令碼使用下列命令來建立資源群組、虛擬機器擴展集和所有相關資源。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意 |
|---|---|
| [az group create](/cli/azure/ad/group#az_ad_group_create) | 建立用來存放所有資源的資源群組。 |
| [az vmss create](/cli/azure/vmss#az_vmss_create) | 建立虛擬機器擴展集，並將它連線到虛擬網路、子網路及網路安全性群組。 若要將流量散發到多個虛擬機器執行個體，也會建立負載平衡器。 此命令也會指定要使用的 VM 映像和管理認證。  |
| [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) | 安裝 Azure 自訂指令碼擴充功能，以執行指令碼來準備每個 VM 執行個體上的資料磁碟。 |
| [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) | 建立負載平衡器規則，將 TCP 通訊埠 80 上的流量散發至擴展集中的 VM 執行個體。 |
| [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) | 取得指派給負載平衡器使用的公用 IP 位址資訊。 |
| [az group delete](/cli/azure/ad/group#delete) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟
如需有關 Azure CLI 2.0 的詳細資訊，請參閱 [Azure CLI 2.0 文件](https://docs.microsoft.com/cli/azure/overview)。

您可以在 [Azure 虛擬機器擴展集文件](../cli-samples.md)中找到其他虛擬機器擴展集的 Azure CLI 2.0 指令碼範例。
