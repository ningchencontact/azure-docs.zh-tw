---
title: 使用 Azure Container Instances 中的 正在啟動的命令列
description: 覆寫設定的容器映像中，當您部署 Azure 容器執行個體進入點
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 78136a081e52ef3f12d672d01449ce616534462e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60537644"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>設定命令列中的容器執行個體，以覆寫預設命令列作業

當您建立容器執行個體時，選擇性地指定要覆寫內建到容器映像的預設命令列指示的命令。 此行為會類似於`--entrypoint`命令列引數`docker run`。

要設定[環境變數](container-instances-environment-variables.md)容器執行個體，指定起始的命令列可用於批次作業是在您要準備每個容器，以動態方式與特定工作的組態。

## <a name="command-line-guidelines"></a>命令列的指導方針

* 根據預設，指定命令列*單一程序，而不需要殼層啟動*容器中。 例如，在命令列可能會執行 Python 指令碼或可執行檔。 

* 若要執行多個命令，開始您的命令列設定容器作業系統中的 shell 環境 (範例： `bin/sh`， `/bin/bash`， `cmd`)。 請遵循的慣例，來結合多個序列中執行的命令殼層。

* 根據容器組態中，您可能需要設定命令列可執行檔的完整路徑或引數。

* 設定適當[重新啟動原則](container-instances-restart-policy.md)的容器執行個體，取決於是否命令列指定長時間執行工作或執行一次工作。 例如，重新啟動原則的`Never`或`OnFailure`建議執行一次工作。 

* 如果您需要在容器映像中設定的預設端點的相關資訊，請使用[docker 映像檢查](https://docs.docker.com/engine/reference/commandline/image_inspect/)命令。

## <a name="command-line-syntax"></a>命令列語法

命令列語法是根據 Azure API 或用來建立執行個體的工具而有所不同。 如果您指定的 shell 環境，也會發現殼層的命令語法慣例。

* [az 容器建立][ az-container-create]命令：傳遞的字串`--command-line`參數。 範例： `--command-line "python myscript.py arg1 arg2"`)。

* [新 AzureRmContainerGroup] [ new-azurermcontainergroup] Azure PowerShell cmdlet:傳遞的字串`-Command`參數。 範例： `-Command "echo hello"`.

* Azure 入口網站：在 **命令覆寫**容器組態中，屬性提供以逗號分隔的清單不含引號的字串。 範例： `python, myscript.py, arg1, arg2`)。 

* Resource Manager 範本或 YAML 檔案，或其中一種 Azure Sdk:指定的字串陣列形式的命令列屬性。 範例： JSON 陣列`["python", "myscript.py", "arg1", "arg2"]`Resource Manager 範本中。 

  如果您熟悉[Dockerfile](https://docs.docker.com/engine/reference/builder/)語法，此格式會類似於*exec* CMD 指令的表單。

### <a name="examples"></a>範例

|    |  Azure CLI   | 入口網站 | 範本 | 
| ---- | ---- | --- | --- |
| 單一命令 | `--command-line "python myscript.py arg1 arg2"` | **命令覆寫**: `python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| 多個命令 | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**命令覆寫**: `/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Azure CLI 的範例

例如，修改的行為[microsoft/aci-wordcount] [ aci-wordcount]容器映像，會分析 Shakespeare 的文字*Hamlet*來尋找最常出現的文字。 而不是分析*Hamlet*，您可以設定會指向不同的文字來源的命令列。

若要查看的輸出[microsoft/aci-wordcount] [ aci-wordcount]容器時，它會分析預設文字，執行下列[az 容器建立][az-container-create]命令。 指定沒有啟動命令列，因此預設的 [容器] 命令會執行。 為了方便說明，此範例會設定[環境變數](container-instances-environment-variables.md)尋找至少五個字母的長時間的前 3 個字：

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

一旦容器狀態顯示為*Terminated* (使用[az container show] [ az-container-show]若要檢查狀態)，顯示記錄檔與[az 容器記錄] [ az-container-logs]以查看輸出。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

輸出：

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

現在設定第二個範例容器藉由指定不同的命令列分析不同的文字。 容器，所執行的 Python 指令碼*wordcount.py*、 接受 URL 作為引數，和處理該頁面的內容，而不是預設值。

比方說，若要判斷前 3 個字詞是至少五個字母長時間在*Romeo and Juliet*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

同樣地，一旦容器是「已終止」，藉由顯示容器的記錄來檢視輸出：

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

輸出：

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>後續步驟

以工作為基礎的情況下，batch 處理大型資料集與數個容器，例如可以受益於在執行階段的自訂命令列。 如需執行工作為基礎的容器的詳細資訊，請參閱[重新啟動原則執行容器化的工作](container-instances-restart-policy.md)。

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
