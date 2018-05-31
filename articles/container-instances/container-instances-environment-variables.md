---
title: 在 Azure 容器執行個體中設定環境變數
description: 了解如何在執行於 Azure 容器執行個體中的容器內設定環境變數
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 05/16/2018
ms.author: marsma
ms.openlocfilehash: 1a025ce647cb3c071a6549a433e6505b85409fdc
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199002"
---
# <a name="set-environment-variables"></a>設定環境變數

在您的容器執行個體中設定環境變數，可讓您提供由容器執行之應用程式或指令碼的動態設定。 若要在容器中設定環境變數，請在建立容器執行個體時加以指定。 您可以使用 [Azure CLI](#azure-cli-example)、[Azure PowerShell](#azure-powershell-example) 和 [Azure 入口網站](#azure-portal-example)，在容器時啟動時設定環境變數。

例如，如果您執行 [microsoft/aci-wordcount][aci-wordcount] 容器映像，可以藉由指定下列環境變數來修改其行為：

*NumWords*：傳送至 STDOUT 的字詞數。

*MinLength*：列入計算之字詞中的字元數上限。 較高的數目會略過常用字詞，例如 "of" 和 "the"。

## <a name="azure-cli-example"></a>Azure CLI 的範例

若要查看 [microsoft/aci-wordcount][aci-wordcount] 容器的預設輸出，請先搭配此 [az container create][az-container-create] 命令來執行 (未指定環境變數)：

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

若要修改輸出，請啟動第二個容器，並新增 `--environment-variables` 引數，然後為 NumWords 和 MinLength 變數指定值：

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

一旦這兩個容器的狀態顯示為「已終止」 (使用 [az container show][az-container-show] 檢查狀態)，即可使用 [az container logs][az-container-logs] 來顯示其記錄，以查看輸出。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

容器的輸出會顯示您如何藉由設定環境變數，來修改第二個容器的指令碼行為。

```console
azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer1
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]

azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Azure PowerShell 的範例

在 PowerShell 中設定環境變數類似於 CLI，但是使用 `-EnvironmentVariable` 命令列引數。

首先，使用此 [New-AzureRmContainerGroup][new-azurermcontainergroup] 命令，以預設組態啟動 [microsoft/aci-wordcount][aci-wordcount] 容器：

```azurepowershell-interactive
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image microsoft/aci-wordcount:latest
```

現在，執行下列 [New-AzureRmContainerGroup][new-azurermcontainergroup] 命令。 此命令會在填入陣列變數 `envVars` 後，指定 NumWords 和 MinLength 環境變數：

```azurepowershell-interactive
$envVars = @{NumWords=5;MinLength=8}
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image microsoft/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

一旦這兩個容器的狀態顯示為「已終止」 (使用 [Get-AzureRmContainerInstanceLog][azure-instance-log] 檢查狀態)，即可使用 [Get-AzureRmContainerInstanceLog][azure-instance-log] 命令提取容器的記錄。

```azurepowershell-interactive
Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

每個容器的輸出會顯示您如何藉由設定環境變數，來修改容器執行的指令碼。

```console
PS Azure:\> Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]

Azure:\
PS Azure:\> Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Azure 入口網站範例

若要在 Azure入口網站中啟動容器時設定環境變數，請在您建立容器時，於 [組態] 頁面上指定這些變數。

當您以入口網站進行部署時，您目前僅限使用三個變數，並且必須以此格式來輸入變數：`"variableName":"value"`

若要查看範例，請使用 NumWords 和 MinLength 變數啟動 [microsoft/aci-wordcount][aci-wordcount] 容器。

1. 在 [組態] 中，將 [重新啟動原則] 設定為 [失敗時]
2. 輸入 `"NumWords":"5"` 作為第一個變數，並在 [新增其他環境變數] 下方選取 [是]，然後輸入 `"MinLength":"8"` 作為第二個變數。 選取 [確定] 來加以確認並部署容器。

![顯示環境變數啟用按鈕和文字方塊的入口網站頁面][portal-env-vars-01]

若要檢視容器的記錄，請在 [設定] 下選取 [容器]，然後選取 [記錄]。 類似於前面 CLI 和 PowerShell 區段中所示的輸出，您可以看到環境變數如何修改指令碼行為。 僅顯示五個字組，每個字組都至少有八個字元長。

![顯示容器記錄輸出的入口網站][portal-env-vars-02]

## <a name="next-steps"></a>後續步驟

針對工作型案例，例如使用數個容器的大型資料集批次處理，可受益於執行階段上的自訂環境變數。 如需有關執行工作型容器的詳細資訊，請參閱[在 Azure 容器執行個體中執行容器化工作](container-instances-restart-policy.md)。

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/r/microsoft/aci-wordcount/

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/azurerm.containerinstance/get-azurermcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-azurerm-ps
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com