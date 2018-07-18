---
title: 快速入門 - 使用 PowerShell 建立您的第一個 Azure Container Instances 容器
description: 在本快速入門中，您會使用 Azure PowerShell 在 Azure Container Instances 中部署 Windows 容器
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: quickstart
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 4a1d338304dbd5e2845768b7bf0273eed23af0ec
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2018
ms.locfileid: "38453561"
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>快速入門：在 Azure Container Instances 中建立您的第一個容器

Azure Container Instances 能讓您在 Azure 中輕鬆建立及管理 Docker 容器，不需要佈建虛擬機器或採用高階服務即可完成。 在本快速入門中，您會在 Azure 中建立 Windows 容器，並使用完整網域名稱 (FQDN) 向網際網路公開此容器。 只需要一個命令就能完成這項作業。 在幾分鐘內，即可看到應用程式在瀏覽器上執行：

![在瀏覽器中檢視使用 Azure Container Instances 所部署的應用程式][qs-powershell-01]

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/)。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

如果您選擇在本機安裝和使用 PowerShell，則在執行本教學課程時，您必須使用 Azure PowerShell 模組 5.5 版或更新版本。 執行 `Get-Module -ListAvailable AzureRM` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzureRmAccount` 以建立與 Azure 的連線。

## <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzureRmResourceGroup][New-AzureRmResourceGroup] 建立 Azure 資源群組。 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

 ```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>建立容器

您可以對 [New-AzureRmContainerGroup][New-AzureRmContainerGroup] Cmdlet 提供名稱、Docker 映像和 Azure 資源群組來建立容器。 您可使用 DNS 名稱標籤，選擇性地向網際網路公開容器。

執行下列命令，以啟動執行 Internet Information Services (IIS) 的 Nano 伺服器容器。 `-DnsNameLabel` 值必須是您建立執行個體的 Azure 區域中的唯一值，因此您可能需要修改這個值以確保唯一性。

 ```azurepowershell-interactive
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

在幾秒內，您就會收到要求的回應。 一開始，容器會處於**建立中**狀態，但應該會在一兩分鐘內啟動。 您可以使用 [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] Cmdlet 來檢查部署狀態：

 ```azurepowershell-interactive
Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

容器的佈建狀態、完整網域名稱 (FQDN) 和 IP 位址會顯示在 Cmdlet 的輸出中：

```console
PS Azure:\> Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer


ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            : Always
IpAddress                : 52.226.19.87
DnsNameLabel             : aci-demo-win
Fqdn                     : aci-demo-win.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Windows
Volumes                  :
State                    : Pending
Events                   : {}
```

容器的 **ProvisioningState** 在進展到 `Succeeded` 後，請在瀏覽器中瀏覽至其 `Fqdn`：

![在瀏覽器中檢視使用 Azure Container Instances 所部署的 IIS][qs-powershell-01]

## <a name="clean-up-resources"></a>清除資源

當容器使用完畢後，請使用 [Remove-AzureRmContainerGroup][Remove-AzureRmContainerGroup] Cmdlet 加以移除：

 ```azurepowershell-interactive
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已透過來自公用 Docker Hub 登錄中的映像建立 Azure 容器執行個體。 如果您想要自行建置容器映像，並從私人的 Azure 容器登錄將其部署至 Azure 容器執行個體，請繼續進行 Azure 容器執行個體教學課程。

> [!div class="nextstepaction"]
> [Azure 容器執行個體教學課程](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/remove-azurermcontainergroup
