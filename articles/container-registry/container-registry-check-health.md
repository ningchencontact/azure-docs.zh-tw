---
title: Azure Container Registry 中的核取登錄健全狀況
description: 了解如何執行診斷的快速命令，以使用 Azure container registry，包括本機的 Docker 組態與登錄的連線能力時，找出一般問題
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: 3e5b5467f9fa25e23f6661c6630d346aa85e2205
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555094"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>檢查 Azure container registry 的健全狀況

當使用 Azure container registry，您可能偶爾會遇到的問題。 例如，您可能無法提取容器映像，因為使用 Docker 在本機環境中的問題。 或者，網路問題可能會阻止您連線到登錄。 

第一個診斷步驟中，執行[az acr 檢查健全狀況][az-acr-check-health] command to get information about the health of the environment and optionally access to a target registry. This command is available in Azure CLI version 2.0.67 or later. If you need to install or upgrade, see [Install Azure CLI][azure-cli]。

## <a name="run-az-acr-check-health"></a>執行 az acr 檢查健全狀況

下列範例顯示不同的方式執行`az acr check-health`命令。

> [!NOTE]
> 如果您在 Azure Cloud Shell 中執行命令，將不會檢查本機環境。 不過，您可以檢查目標登錄的存取權。

### <a name="check-the-environment-only"></a>檢查只有在環境

若要檢查本機的 Docker 精靈、 CLI 版本和 Helm 用戶端設定，執行未包含其他參數命令：

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>檢查在環境中，目標登錄

若要查看登錄的存取權，以及執行的本機環境檢查，傳遞目標登錄的名稱。 例如:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>錯誤報告

此命令會將資訊記錄至標準輸出。 如果偵測到問題時，它會提供錯誤碼和描述。 如需有關的代碼和可能的解決方案的詳細資訊，請參閱[作為錯誤參考](container-registry-health-error-reference.md)。

根據預設，此命令會停止時發現錯誤。 您也可以執行命令，使其提供輸出所有的健康情況檢查，即使發現錯誤。 新增`--ignore-errors`參數，如下列範例所示：

```azurecli
# Check environment only
az acr check-health --ignore-errors

# Check environment and target registry
az acr check-health --name myregistry --ignore-errors
```      

範例輸出：

```console
$ az acr check-health --name myregistry --ignore-errors --yes

Docker daemon status: available
Docker version: Docker version 18.09.2, build 6247962
Docker pull of 'mcr.microsoft.com/mcr/hello-world:latest' : OK
ACR CLI version: 2.2.9
Helm version:
Client: &version.Version{SemVer:"v2.14.1", GitCommit:"5270352a09c7e8b6e8c9593002a73535276507c0", GitTreeState:"clean"}
DNS lookup to myregistry.azurecr.io at IP 40.xxx.xxx.162 : OK
Challenge endpoint https://myregistry.azurecr.io/v2/ : OK
Fetch refresh token for registry 'myregistry.azurecr.io' : OK
Fetch access token for registry 'myregistry.azurecr.io' : OK
```  



## <a name="next-steps"></a>後續步驟

如需詳細資訊所傳回的錯誤碼[az acr 檢查健全狀況][az-acr-check-health]命令，請參閱[健全狀況檢查錯誤參照](container-registry-health-error-reference.md)。

請參閱[常見問題集](container-registry-faq.md)常見問題集和其他關於 Azure Container Registry 的已知的問題。





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
