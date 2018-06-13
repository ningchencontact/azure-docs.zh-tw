---
title: 使用 Azure CLI 2.0 建立虛擬機器擴展集 | Microsoft Docs
description: 了解如何使用 Azure PowerShell 快速建立虛擬機器縮放
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: get-started-article
ms.date: 12/19/2017
ms.author: iainfou
ms.openlocfilehash: 8794ea7d998293e7ea88ba780f67ef8a021f2298
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2018
ms.locfileid: "30201099"
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>使用 Azure CLI 2.0 建立虛擬機器擴展集
虛擬機器擴展集可讓您部署和管理一組相同、自動調整的虛擬機器。 您可以手動調整擴展集中的 VM 數目，或定義規則以根據如 CPU、記憶體需求或網路流量的資源使用量來自動調整。 在本使用者入門文章中，您要使用 Azure CLI 2.0 來建立虛擬機器擴展集。 您還可以使用 [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) 或 [Azure 入口網站](virtual-machine-scale-sets-create-portal.md)來建立擴展集。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本教學課程會要求您執行 Azure CLI 2.0.20 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 


## <a name="create-a-scale-set"></a>建立擴展集
請先使用 [az group create](/cli/azure/group#az_group_create) 建立資源群組，才可以建立擴展集。 下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組：

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

現在使用 [az vmss create](/cli/azure/vmss#az_vmss_create) 建立虛擬機器擴展集。 下列範例會建立名為 myScaleSet 的擴展集，以及產生 SSH 金鑰 (如果不存在)︰

```azurecli-interactive 
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys
```

建立及設定所有擴展集資源和 VM 需要幾分鐘的時間。


## <a name="install-nginx-webserver"></a>安裝 NGINX Web 伺服器
若要測試擴展集，請使用自訂指令碼延伸模組來下載及執行 VM 執行個體上安裝 NGINX 的指令碼。 此擴充功能適用於部署後組態、軟體安裝或其他任何組態/管理工作。 如需詳細資訊，請參閱[自訂指令碼延伸模組概觀](../virtual-machines/linux/extensions-customscript.md)。

套用安裝 NGINX 的自訂指令碼延伸模組，如下所示：

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],"commandToExecute":"./automate_nginx.sh"}'
```


## <a name="allow-web-traffic"></a>允許 Web 流量
若要允許流量觸達 Web 伺服器，請使用 [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) 建立負載平衡器規則。 下列範例會建立名為 myLoadBalancerRuleWeb 的規則：

```azurecli-interactive 
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```


## <a name="test-your-web-server"></a>測試您的 Web 伺服器
若要查看作用中的 web 伺服器，可使用 [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) 取得負載平衡器的公用 IP 位址。 下列範例會取得建立作為擴展集一部分的 myScaleSetLBPublicIP IP 位址︰

```azurecli-interactive 
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

將負載平衡器的公用 IP 位址輸入網頁瀏覽器中。 負載平衡器會將流量散發至您的其中一個 VM 執行個體，如下列範例所示：

![NGINX 中的預設網頁](media/virtual-machine-scale-sets-create-cli/running-nginx-site.png)


## <a name="clean-up-resources"></a>清除資源
若不再需要，您可以使用 [az group delete](/cli/azure/group#az_group_delete) 來移除資源群組、擴展集和所有相關資源，如下所示：

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>後續步驟
在本使用者入門文章中，您建立了基本的擴展集，並使用自訂指令碼延伸模組，在 VM 執行個體上安裝基本的 NGINX web 伺服器。 如需更佳的延展性和自動化，請使用下列使用說明文章展開您的擴展集：

- [在虛擬機器擴展集上部署您的應用程式](virtual-machine-scale-sets-deploy-app.md)
- 利用 [Azure CLI](virtual-machine-scale-sets-autoscale-cli.md)、[Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md) 或 [Azure 入口網站](virtual-machine-scale-sets-autoscale-portal.md)自動縮放
- [將自動作業系統升級用於擴展集 VM 執行個體](virtual-machine-scale-sets-automatic-upgrade.md)