---
title: 在 Azure 容器執行個體中設定環境變數
description: 了解如何在 Azure 容器執行個體中設定環境變數
services: container-instances
author: david-stanford
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/13/2018
ms.author: dastanfo
ms.openlocfilehash: 37fde41b6dc2ea0a4d3b4b38a0e3df81a297c125
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
---
# <a name="set-environment-variables"></a>設定環境變數

在您的容器執行個體中設定環境變數，可讓您提供由容器執行之應用程式或指令碼的動態設定。

目前您能夠從 CLI 和 PowerShell 設定環境變數。 在這兩種情況下，您會在命令中使用旗標來設定環境變數。 在 ACI 中設定環境變數類似於 `docker run` 的命令列引數 `--env`。 例如，如果您使用 microsoft/aci-wordcount 容器映像，可以藉由指定下列環境變數來修改行為：

*NumWords*：傳送至 STDOUT 的字詞數。

*MinLength*：列入計算之字詞中的字元數上限。 較高的數目會略過常用字詞，例如 "of" 和 "the"。

## <a name="azure-cli-example"></a>Azure CLI 的範例

若要查看容器的預設輸出，請執行下列命令：

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

藉由為容器的環境變數指定 `NumWords=5` 和 `MinLength=8`，容器記錄應該會顯示不同的輸出。

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

一旦容器狀態顯示為「已終止」 (使用 [az container show][az-container-show] 檢查其狀態)，顯示其記錄以查看輸出。  若要檢視不含環境變數的容器輸出，請將 --name 設為 mycontainer1 而非 mycontainer2。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

## <a name="azure-powershell-example"></a>Azure PowerShell 的範例

若要查看容器的預設輸出，請執行下列命令：

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

藉由為容器的環境變數指定 `NumWords=5` 和 `MinLength=8`，容器記錄應該會顯示不同的輸出。

```azurepowershell-interactive
$envVars = @{NumWord=5;MinLength=8}
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image microsoft/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

一旦容器狀態變為「已終止」 (使用 [Get-AzureRmContainerInstanceLog][azure-instance-log] 檢查其狀態)，顯示其記錄以查看輸出。 若要檢視不含環境變數的容器記錄，請將 ContainerGroupName 設為 mycontainer1 而非 mycontainer2。

```azurepowershell-interactive
Get-AzureRmContainerInstanceLog `
    -ResourceGroupName myResourceGroup `
    -ContainerGroupName mycontainer2
```

## <a name="example-output-without-environment-variables"></a>無環境變數的輸出範例

```bash
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
```

## <a name="example-output-with-environment-variables"></a>有環境變數的輸出範例

```bash
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="next-steps"></a>後續步驟

您已經知道如何自訂容器的輸入，接下來請了解如何保存執行完成之容器的輸出。
> [!div class="nextstepaction"]
> [使用 Azure 容器執行個體來掛接 Azure 檔案共用](container-instances-mounting-azure-files-volume.md)

<!-- LINKS Internal -->
[azure-cloud-shell]: ../cloud-shell/overview.md
[azure-cli-install]: /cli/azure/
[azure-powershell-install]: /powershell/azure/install-azurerm-ps
[azure-instance-log]: /powershell/module/azurerm.containerinstance/get-azurermcontainerinstancelog
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show