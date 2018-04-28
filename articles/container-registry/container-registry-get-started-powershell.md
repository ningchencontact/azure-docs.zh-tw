---
title: 快速入門 - 使用 PowerShell 在 Azure 中建立私用的 Docker 登錄
description: 快速了解如何使用 PowerShell 建立私用的 Docker 容器登錄。
services: container-registry
author: neilpeterson
manager: jeconnoc
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 3097696d85c738730e725ede84437d0e36ec6bc4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-an-azure-container-registry-using-powershell"></a>快速入門：使用 PowerShell 建立 Azure Container Registry

Azure Container Registry 是用於儲存私用 Docker 容器映像的受控 Docker 容器登錄服務。 本指南詳述如何使用 PowerShell 建立 Azure Container Registry 執行個體、將容器映像推送到登錄中，最後將容器從登錄部署至 Azure 容器執行個體 (ACI)。

本快速入門需要 Azure PowerShell 模組 3.6 版或更新版本。 執行 `Get-Module -ListAvailable AzureRM` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。

您也必須在本機上安裝 Docker。 Docker 提供可輕鬆在 [Mac][docker-mac]、[Windows][docker-windows] 或 [Linux][docker-linux] 系統上設定 Docker 的套件。

## <a name="log-in-to-azure"></a>登入 Azure

使用 `Connect-AzureRmAccount` 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

```powershell
Connect-AzureRmAccount
```

## <a name="create-resource-group"></a>建立資源群組

使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 建立 Azure 資源群組。 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container-registry"></a>建立容器登錄庫

使用 [New-AzureRMContainerRegistry](/powershell/module/containerregistry/New-AzureRMContainerRegistry) 命令建立 ACR 執行個體。

登錄名稱在 Azure 內必須是唯一的，且包含 5-50 個英數字元。 下列範例中使用 *myContainerRegistry007*。 請將此更新為唯一的值。

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-acr"></a>登入 ACR

發送和提取容器映像之前，您必須登入 ACR 執行個體。 首先，使用 [Get AzureRmContainerRegistryCredential](/powershell/module/containerregistry/get-azurermcontainerregistrycredential) 命令，取得 ACR 執行個體的管理員認證。

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

接著，使用 [docker login][docker-login] 命令登入 ACR 執行個體。

```powershell
docker login $registry.LoginServer -u $creds.Username -p $creds.Password
```

完成後，此命令會傳回 `Login Succeeded`。 您也可能會看到安全性警告，建議您使用 `--password-stdin` 參數。 其使用超出本文的範圍時，建議您遵循此最佳做法。 如需詳細資訊，請參閱 [docker login][docker-login] 命令參考。

## <a name="push-image-to-acr"></a>推送映像到 ACR

若要推送映像到 Azure Container Registry，您必須先有映像。 如有需要，請執行下列命令，以從 Docker Hub 提取預先建立的映像。

```powershell
docker pull microsoft/aci-helloworld
```

映像必須加上 ACR 登入伺服器名稱。 使用 [docker tag][docker-tag] 命令來執行這項操作。

```powershell
$image = $registry.LoginServer + "/aci-helloworld:v1"
docker tag microsoft/aci-helloworld $image
```

最後，使用 [docker push][docker-push] 將映像推送到 ACR。

```powershell
docker push $image
```

## <a name="deploy-image-to-aci"></a>將映像部署至 ACI
若要在 Azure 容器執行個體 (ACI) 中將映像部署為容器執行個體，請先將登錄認證轉換為 PSCredential。

```powershell
$secpasswd = ConvertTo-SecureString $creds.Password -AsPlainText -Force
$pscred = New-Object System.Management.Automation.PSCredential($creds.Username, $secpasswd)
```

若要從容器登錄部署包含 1 個 CPU 核心和 1 GB 記憶體的容器映像，請執行下列命令：

```powershell
New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name mycontainer -Image $image -Cpu 1 -MemoryInGB 1 -IpAddressType public -Port 80 -RegistryCredential $pscred
```

您應會從 Azure Resource Manager 得到首次回應，其中包含容器的詳細資料。 若要監視容器的狀態以及查看它何時執行，請重複 [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] 命令。 此作業所需的時間應該不到一分鐘。

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
```

範例輸出：`Succeeded`

## <a name="view-the-application"></a>檢視應用程式
成功部署至 ACI 之後，請使用 [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] 命令來擷取容器的公用 IP 位址：

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).IpAddress
```

範例輸出：`"13.72.74.222"`

若要查看執行中的應用程式，請在您慣用的瀏覽器中瀏覽至該公用 IP 位址。 您應該會看到類似下面的畫面：

![瀏覽器中的 Hello World 應用程式][qs-portal-15]

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以使用 [Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup] 命令來移除資源群組、Azure Container Registry 及所有的 Azure 容器執行個體。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已使用 Azure CLI 建立 Azure Container Registry，並在 Azure 容器執行個體中啟動其執行個體。 請繼續進行 Azure 容器執行個體教學課程，以深入了解 ACI。

> [!div class="nextstepaction"]
> [Azure 容器執行個體教學課程](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup

<!-- IMAGES> -->
[qs-portal-15]: ./media/container-registry-get-started-portal/qs-portal-15.png
