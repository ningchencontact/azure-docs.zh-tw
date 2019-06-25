---
title: 從範本在 Azure 中建立 Linux VM | Microsoft Docs
description: 如何使用 Azure CLI 從 Resource Manager 範本建立 Linux VM
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5cf213bec0b4d9e9662a9b554be72102f11e8d2d
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295462"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>如何使用 Azure Resource Manager 範本建立 Linux 虛擬機器

了解如何使用 Azure Resource Manager 範本和 Azure CLI 從 Azure Cloud shell 中建立 Linux 虛擬機器 (VM)。 若要建立 Windows 虛擬機器，請參閱[從 Resource Manager 範本建立 Windows 虛擬機器](../windows/ps-template.md)。

## <a name="templates-overview"></a>範本概觀

Azure Resource Manager 範本是 JSON 檔案，檔案中定義您的 Azure 解決方案的基礎結構和組態。 透過範本，您可以在整個生命週期中重複部署方案，並確信您的資源會以一致的狀態部署。 若要深入了解範本和其建構方式的格式，請參閱[快速入門：使用 Azure 入口網站建立及部署 Azure Resource Manager 範本](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)。 若要檢視資源類型的 JSON 語法，請參閱[在 Azure Resource Manager 範本中定義資源](/azure/templates/microsoft.compute/allversions)。

## <a name="create-a-virtual-machine"></a>建立虛擬機器

建立 Azure 虛擬機器通常包含兩個步驟：

1. 建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 資源群組必須在虛擬機器之前建立。
1. 建立虛擬機器。

下列範例會建立從 VM [Azure 快速入門範本](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json)。 僅 SSH 驗證允許針對此部署。 出現提示時，提供您自己 SSH 公開金鑰的值，例如 ~/.ssh/id_rsa.pub  的內容。 如果您需要建立 SSH 金鑰組，請參閱[如何為 Azure 中的 Linux VM 建立和使用的 SSH 金鑰組](mac-create-ssh-keys.md)。 以下是範本的複本：

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

若要執行 CLI 指令碼，請選取**試試**開啟 Azure Cloud shell 中。 若要將指令碼，shell 中，以滑鼠右鍵按一下，然後按**貼上**:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the project name (used for generating resource names):" &&
read projectName &&
echo "Enter the administrator username:" &&
read username &&
echo "Enter the SSH public key:" &&
read key &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json --parameters projectName=$projectName adminUsername=$username adminPublicKey="$key" &&
az vm show --resource-group $resourceGroupName --name "$projectName-vm" --show-details --query publicIps --output tsv
```

最後一個的 Azure CLI 命令會顯示新建立的 VM 的公用 IP 位址。 您需要連線至虛擬機器的公用 IP 位址。 請參閱本文的下一節。

在先前範例中，您已指定存放在 GitHub 中的範本。 您也可以下載或建立範本，並用 `--template-file` 參數指定本機路徑。

以下是一些其他資源：

- 若要了解如何開發 Resource Manager 範本，請參閱 [Azure Resource Manager 文件](/azure/azure-resource-manager/)。
- 若要查看 Azure 虛擬機器的結構描述，請參閱[Azure 範本參考](/azure/templates/microsoft.compute/allversions)。
- 若要查看更多的虛擬機器範本範例，請參閱[Azure 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)。

## <a name="connect-to-virtual-machine"></a>連線至虛擬機器

之後您便可以如常透過 SSH 連線至您的 VM。 提供您自己的公用 IP 位址 (透過先前命令取得)：

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>後續步驟

在此範例中，您建立基本的 Linux VM。 如需其他 Resource Manager 範本包含應用程式架構，或建立更複雜的環境，瀏覽[Azure 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)。

若要深入了解如何建立範本，請針對您部署的資源類型檢視 JSON 語法和屬性：

- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
