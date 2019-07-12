---
title: 刪除 Azure Container Registry 中的映像資源
description: 有關如何藉由刪除容器映像資料來有效地管理登錄大小的詳細資料。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/17/2019
ms.author: danlep
ms.openlocfilehash: c603afa61499a615a0882cef06f14fd3d080a9ef
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797759"
---
# <a name="delete-container-images-in-azure-container-registry"></a>刪除 Azure Container Registry 中的容器映像

若要維護 Azure Container Registry 的大小，您應該定期刪除過時的映像資料。 雖然有些部署至生產環境的容器映像可能需要較長時間的儲存，但其他容器映像通常可以更快速地刪除。 例如，在自動化建置和測試案例中，您的登錄可以快速地填入可能永遠不會部署的映像，並可在完成建置和測試行程後立刻清除。

您可以用數種不同的方式刪除映像資料，因此務必了解每項刪除作業對於儲存體使用量有何影響。 本文章涵蓋了幾種方法刪除映像資料：

* 刪除[存放庫](#delete-repository)：刪除存放庫內的所有映像和所有唯一層次。
* 依[標記](#delete-by-tag)刪除：刪除映像、標記、映像參考的所有唯一層次，以及與映像相關聯的所有其他標記。
* 依[資訊清單摘要](#delete-by-manifest-digest)刪除：刪除映像、映像參考的所有唯一層次，以及與映像相關聯的所有標記。

範例指令碼可供協助自動進行刪除作業。

如需這些概念的簡介，請參閱[關於登錄、 儲存機制和映像](container-registry-concepts.md)。

## <a name="delete-repository"></a>刪除存放庫

刪除存放庫即可刪除存放庫中的所有映像，包括所有標籤、唯一層次及資訊清單。 當您刪除儲存機制時，您就會復原由參考唯一的圖層，該存放庫中的映像的儲存空間。

下列 Azure CLI 命令可刪除 "acr-helloworld" 存放庫以及該存放庫內的所有標記和資訊清單。 如果在登錄中的任何其他映像不會參考已刪除的資訊清單所參考的圖層，其圖層資料會一併刪除，復原儲存空間。

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>依標記刪除

您可以在刪除作業中指定存放庫名稱和標記，以從存放庫中刪除個別映像。 當您依標記刪除時，會復原映像中任何唯一層次 (並未由登錄中任何其他映像共用的層次) 所使用的儲存空間。

若要刪除標記，請使用[az acr 存放庫刪除][az-acr-repository-delete]映像名稱，在指定`--image`參數。 映像獨有的所有層次，與該映像相關聯的任何其他標記都會遭到刪除。

例如，從 "myregistry" 登錄中刪除 "acr-helloworld:latest" 映像：

```azurecli
$ az acr repository delete --name myregistry --image acr-helloworld:latest
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n): y
```

> [!TIP]
> 「依標記」  刪除不應該與刪除標記 (取消標記) 混淆。 您可以刪除標記，以使用 Azure CLI 命令[az acr 存放庫解除][az-acr-repository-untag]。 不含空格會釋放，當您解除標記的映像，因為其[資訊清單](container-registry-concepts.md#manifest)和圖層資料保持在登錄中。 只有標記參考本身會被刪除。

## <a name="delete-by-manifest-digest"></a>依資訊清單摘要刪除

[資訊清單摘要](container-registry-concepts.md#manifest-digest)可以與一個、零個或多個標記產生關聯。 當您依摘要刪除時，由資訊清單參考的所有標記都會遭到刪除，因為任何層次的層次資料對映像而言都是唯一的。 共用的層次資料不會遭到刪除。

若要依摘要刪除，首先針對包含所要刪除映像的存放庫列出資訊清單摘要。 例如:

```console
$ az acr repository show-manifests --name myregistry --repository acr-helloworld
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  }
]
```

接下來，指定您想要在刪除摘要式[az acr 存放庫刪除][az-acr-repository-delete]命令。 此命令的格式為：

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

例如，若要刪除前面輸出中所列的最後一個資訊清單 (含標記 "v2")：

```console
$ az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): y
```

`acr-helloworld:v2`從登錄中，會刪除映像，因為是任何唯一至該映像的圖層資料。 如果資訊清單與多個標記相關聯，也會一併刪除所有相關聯的標記。

## <a name="delete-digests-by-timestamp"></a>刪除摘要依時間戳記

若要維持存放庫或登錄的大小，您可能需要定期刪除早於在特定日期的資訊清單摘要。

下列 Azure CLI 命令會列出超過指定的時間戳記，以遞增順序的存放庫中的所有資訊清單摘要。 以適合您環境的值取代 `<acrName>` 和 `<repositoryName>`。 時間戳記可能是完整的日期時間運算式或日期，如此範例所示。

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

找出過時資訊清單的摘要之後, 您可以執行下列 Bash 指令碼，刪除早於指定的時間戳記資訊清單的摘要。 它需要 Azure CLI 和 **xargs**。 根據預設，此指令碼不會執行任何刪除。 將 `ENABLE_DELETE` 值變更為 `true`，以啟用映像刪除。

> [!WARNING]
> 使用下列的範例指令碼，請謹慎-已刪除的映像資料是 UNRECOVERABLE。 如果您有提取映像的資訊清單的摘要 （相對於映像名稱） 的系統，您應該不會執行這些指令碼。 刪除資訊清單的摘要，會造成這些系統無法從登錄提取映像。 而不是提取資訊清單，請考慮採用*唯一標記*配置[建議的最佳做法][tagging-best-practices]。 

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
# TIMESTAMP can be a date-time string such as 2019-03-15T17:55:00.
REGISTRY=myregistry
REPOSITORY=myrepository
TIMESTAMP=2019-04-05  

# Delete all images older than specified timestamp.

if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
    --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
   --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].[digest, timestamp]" -o tsv
fi
```

## <a name="delete-untagged-images"></a>刪除已取消標記的映像

如[資訊清單摘要](container-registry-concepts.md#manifest-digest)一節所述，使用現有標記推送已修改的映像會**取消標記**先前推送的映像，因而產生孤立 (或「懸盪」) 的映像。 先前推送映像的資訊清單以及其層次資料都會保留在登錄中。 考量以下事件順序：

1. 推送具有 **latest** 標記的映像 *acr-helloworld*：`docker push myregistry.azurecr.io/acr-helloworld:latest`
1. 檢查 *acr-helloworld* 存放庫的資訊清單：

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

1. 修改 *acr-helloworld* Dockerfile
1. 推送具有 **latest** 標記的映像 *acr-helloworld*：`docker push myregistry.azurecr.io/acr-helloworld:latest`
1. 檢查 *acr-helloworld* 存放庫的資訊清單：

   ```console
   $ az acr repository show-manifests --name myregistry --repository acr-helloworld
   [
     {
       "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:38:35.9170967Z"
     },
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

如您所見的輸出序列中的最後一個步驟中，現在會被遺棄資訊清單的`"tags"`屬性是空的清單。 此資訊清單以及它所參考的任何唯一層次資料，仍存在於登錄中。 **若要刪除這類孤立映像及其層次資料，您必須依資訊清單摘要刪除**。

## <a name="delete-all-untagged-images"></a>刪除所有已取消標記的映像

您可以使用下列 Azure CLI 命令，列出存放庫中所有已取消標記的映像。 以適合您環境的值取代 `<acrName>` 和 `<repositoryName>`。

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

您可以使用這個命令的指令碼中，刪除存放庫中所有未標記的映像。

> [!WARNING]
> 請小心使用下列範例指令碼--無法復原已刪除的映像資料。 如果您有提取映像的資訊清單的摘要 （相對於映像名稱） 的系統，您應該不會執行這些指令碼。 刪除已取消標記的映像，會導致這些系統無法從登錄中提取映像。 而不是提取資訊清單，請考慮採用*唯一標記*配置[建議的最佳做法][tagging-best-practices]。

**Bash 中的 Azure CLI**

下列 Bash 指令碼會從存放庫中刪除所有已取消標記的映像。 它需要 Azure CLI 和 **xargs**。 根據預設，此指令碼不會執行任何刪除。 將 `ENABLE_DELETE` 值變更為 `true`，以啟用映像刪除。

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
REGISTRY=myregistry
REPOSITORY=myrepository

# Delete all untagged (orphaned) images
if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY  --query "[?tags[0]==null].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable image deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY --query "[?tags[0]==null]" -o tsv
fi
```

**PowerShell 中的 Azure CLI**

下列 PowerShell 指令碼會從存放庫中刪除所有已取消標記的映像。 它需要 PowerShell 和 Azure CLI。 根據預設，此指令碼不會執行任何刪除。 將 `$enableDelete` 值變更為 `$TRUE`，以啟用映像刪除。

```powershell
# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to '$TRUE' to enable image delete
$enableDelete = $FALSE

# Modify for your environment
$registry = "myregistry"
$repository = "myrepository"

if ($enableDelete) {
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null].digest" -o tsv `
    | %{ az acr repository delete --name $registry --image $repository@$_ --yes }
} else {
    Write-Host "No data deleted."
    Write-Host "Set `$enableDelete = `$TRUE to enable image deletion."
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null]" -o tsv
}
```

## <a name="next-steps"></a>後續步驟

如需 Azure Container Registry 中映像儲存體的詳細資訊，請參閱 [Azure Container Registry 中的容器映像儲存體](container-registry-storage.md)。

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com
[tagging-best-practices]: https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
