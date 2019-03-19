---
title: 鎖定 Azure Container Registry 中的映像
description: 設定屬性的容器映像或儲存機制，因此無法刪除或覆寫中的 Azure container registry。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 02/19/2019
ms.author: danlep
ms.openlocfilehash: cdf457eefc88edcc22f1fbaab4859fbcf3b69bca
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2019
ms.locfileid: "56654621"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>鎖定在 Azure container registry 的容器映像

在 Azure container registry，您可以鎖定映像版本或存放庫，讓它不能刪除或更新。 鎖定映像或存放庫，請更新其屬性，使用 Azure CLI 命令[az acr 存放庫更新][az-acr-repository-update]。 

本文會要求您執行 Azure CLI 在 Azure Cloud Shell 或在本機 (2.0.55 版本或更新版本的建議)。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli]。

## <a name="scenarios"></a>案例

根據預設，Azure Container Registry 中標記的影像是*可變*，因此具有適當權限可以重複更新並使用相同的標籤將映像推送至登錄。 容器映像也可以[刪除](container-registry-delete.md)視。 當您開發映像，並需要維護您的登錄的大小，則此行為會有所幫助。

不過，當您將容器映像部署到生產環境時，您可能需要*不可變*容器映像。 不可變的影像是指您無法意外刪除或覆寫。 使用[az acr 存放庫更新][ az-acr-repository-update]命令來設定 repository 的屬性，讓您可以：

* 鎖定的映像版本或整個存放庫

* 無法刪除，保護存放庫的映像版本，但允許更新

* 防止讀取 （提取） 上的映像版本或整個存放庫

請參閱下列各節的範例。

## <a name="lock-an-image-or-repository"></a>鎖定存放庫的映像 

### <a name="lock-an-image-by-tag"></a>鎖定所標記的映像

鎖定*myrepo / myimage:tag*映像中*myregistry*，執行下列命令[az acr 存放庫更新][ az-acr-repository-update]命令：

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>資訊清單的摘要來鎖定映像

鎖定*myrepo/myimage*資訊清單的摘要所識別的映像 (SHA-256 雜湊，以表示`sha256:...`)，執行下列命令。 (若要尋找一或多個映像標記相關聯的摘要資訊清單，請執行[az acr 存放庫顯示為資訊清單][ az-acr-repository-show-manifests]命令。)

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>鎖定存放庫

鎖定*myrepo/myimage*存放庫和所有的映像中，執行下列命令：

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>保護以防止刪除的映像或存放庫

### <a name="protect-an-image-from-deletion"></a>保護以防止刪除的映像

若要允許*myrepo / myimage:tag*映像來更新，但不是會刪除，執行下列命令：

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>保護以防止刪除的儲存機制

下列命令*myrepo/myimage*存放庫，因此無法刪除。 仍要更新或刪除個別的映像。

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>防止讀取的作業上的映像儲存機制

若要防止讀取 （提取） 作業上*myrepo / myimage:tag*映像，執行下列命令：

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

若要防止讀取的作業中的所有映像*myrepo/myimage*存放庫中，執行下列命令：

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>解除鎖定影像或存放庫

若要還原預設行為*myrepo / myimage:tag*映像，這樣可以刪除和更新，執行下列命令：

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

若要還原預設行為*myrepo/myimage*存放庫和所有映像，這樣可以刪除和更新，執行下列命令：

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>後續步驟

在本文中，您已了解使用[az acr 存放庫更新][ az-acr-repository-update]命令，以避免刪除或更新存放庫中的映像版本。 若要設定其他屬性，請參閱[az acr 存放庫更新][ az-acr-repository-update]命令參考。

若要查看映像版本或存放庫設定的屬性，請使用[az acr 存放庫 show] [ az-acr-repository-show]命令。

如需有關刪除作業的詳細資訊，請參閱 <<c0> [ 刪除在 Azure Container Registry 的容器映像][container-registry-delete]。

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md

