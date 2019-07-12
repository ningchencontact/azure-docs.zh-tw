---
title: 關於儲存機制和 Azure Container Registry 中的映像
description: Azure container registry 存放庫及容器映像的重要概念簡介。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/01/2019
ms.author: danlep
ms.openlocfilehash: a14f0a2a86c5e4922fcddf3c92d48c6dfb1497a3
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800324"
---
# <a name="about-registries-repositories-and-images"></a>關於登錄、 儲存機制和映像

這篇文章介紹容器登錄庫，存放庫中，容器映像和相關的成品的重要概念。 

## <a name="registry"></a>登錄

容器「登錄」  是一項服務，可儲存及散發容器映像。 Docker Hub 是公用的容器登錄庫支援開放原始碼社群，並做為影像的一般類別目錄。 Azure Container Registry 提供直接控制其映像，使用整合式驗證的使用者[異地複寫](container-registry-geo-replication.md)網路接近部署，支援全域散發和可靠性[虛擬網路和防火牆設定](container-registry-vnet.md)，[標記鎖定](container-registry-image-lock.md)，以及許多其他增強功能。 

Azure Container Registry 支援相關的 Docker 容器映像，除了[內容成品](container-registry-image-formats.md)包括開放容器計劃 (OCI) 影像格式。

## <a name="content-addressable-elements-of-an-artifact"></a>成品的內容可定址的項目

Azure container registry 中的成品的位址包括下列項目。 

```
[loginUrl]/[namespace]/[artifact:][tag]
```

* **loginUrl** -登錄主機的完整的名稱。 在 Azure container registry 中的登錄主機的格式*myregistry*。 azurecr.io （全部小寫）。 使用 Azure container registry 的 Docker 或 pull 或 push 成品的其他用戶端工具時，您必須指定 loginUrl。 
* **命名空間**-斜線分隔的邏輯群組相關的映像或成品-例如，針對工作群組或應用程式
* **成品**-特定的映像或成品儲存機制名稱
* **標記**-特定版本的映像或儲存在儲存機制的構件


例如，Azure container registry 中的映像的完整名稱可能如下：

```
myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2
```

請參閱下列各節，如需這些項目詳細資料。

## <a name="repository-name"></a>儲存機制名稱

管理容器登錄*存放庫*，容器映像或其他成品具有相同的名稱，但不同的標記集合。 例如，下列三個映像位於 "acr-helloworld" 存放庫中：

```
acr-helloworld:latest
acr-helloworld:v1
acr-helloworld:v2
```

存放庫名稱也可以包含[命名空間](container-registry-best-practices.md#repository-namespaces)。 命名空間可讓您使用正斜線分隔的存放庫名稱，例如群組映像：

```
marketing/campaign10-18/web:v2
marketing/campaign10-18/api:v3
marketing/campaign10-18/email-sender:v2
product-returns/web-submission:20180604
product-returns/legacy-integrator:20180715
```

## <a name="image"></a>Image

容器映像或內登錄其他成品與一個或多個標記相關聯、 有一或多個圖層，以及由資訊清單。 了解這些元件如何互相關聯性，可協助您有效地管理您的登錄。

### <a name="tag"></a>Tag

*標記*映像或其他成品指定其版本。 存放庫中的單一成品可以指派一或多個標記，而且可以 「 標記 」。 也就是說，您可以刪除所有標記從映像，而映像的資料 （其為圖層） 維持在登錄中。

存放庫 (或存放庫和命名空間) 以及標記可定義映像的名稱。 您可以在推送或提取作業中指定映像名稱，以推送和提取映像。

標記容器映像的方式會引導您開發或部署這些案例。 比方說，建議使用穩定的標記來維護您基底映像和部署映像的唯一標籤。 如需詳細資訊，請參閱 <<c0> [ 標記和版本控制的容器映像的建議](container-registry-image-tag-version.md)。

### <a name="layer"></a>層次

容器映像會組成一或多個*層*，各自對應到定義映像的 Dockerfile 中的資料行。 登錄中的映像會共用常見層次，以提高儲存效率。 例如，不同存放庫中的數個映像可能會共用相同的 Alpine Linux 基底層次，但是該層次只有一個複本會儲存在登錄中。

層次共用也可將層次分布最佳化，使其分布於有多個映像共用常見層次的節點。 例如，如果節點上現有的映像包含 Alpine Linux 層次作為其基底，則參考同一層的不同映像在後續提取中不會將層移轉到節點。 相反地，它會參考已存在於節點上的層次。

若要提供安全的隔離和保護從潛在層操作，層級不會共用跨登錄。

### <a name="manifest"></a>資訊清單

每個容器映像或推送到容器登錄的成品相關聯*資訊清單*。 在推送映像時，由登錄所產生的資訊清單，可唯一識別該映像並指定其層次。 您可以列出使用 Azure CLI 命令的存放庫的資訊清單[az acr 存放庫顯示為資訊清單][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

例如，清單"acr helloworld"存放庫摘要的資訊清單：

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
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp": "2018-07-11T21:38:35.9170967Z"
  }
]
```

### <a name="manifest-digest"></a>資訊清單摘要

資訊清單是由唯一的 SHA-256 雜湊或「資訊清單摘要」  識別。 識別每個映像或成品，標記與否，是否會依其摘要。 即使映像的層次資料與另一個映像的層次資料相同完全，摘要值也會是唯一的。 這個機制可讓您重複將標記相同的映像推送至登錄。 例如，您可以將 `myimage:latest` 重複推送至您的登錄，而不會發生錯誤，因為每個映像都是由其唯一摘要識別。

您可以在推送作業中指定其摘要，以從摘要中提取映像。 有些系統可能會設定為依照摘要提取，因為這可保證所提取的映像版本，即使標記相同的映像隨後推送至登錄亦然。

依資訊清單的摘要，比方說，從"acr helloworld"存放庫提取映像：

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> 如果您重複推送具有相同標記且已修改的映像，可以建立孤立映像 (也就是已取消標記的映像)，但仍會耗用您登錄中的空間。 當您依照標記列出或檢視映像時，已取消標記的映像不會顯示在 Azure CLI 或 Azure 入口網站中。 不過，其層次仍然存在，而且會耗用您登錄中的空間。 釋出空間供未標記的映像的相關資訊，請參閱[刪除在 Azure Container Registry 的容器映像](container-registry-delete.md)。


## <a name="next-steps"></a>後續步驟

深入了解[映像儲存體](container-registry-storage.md)並[支援的內容格式](container-registry-image-formats.md)Azure Container Registry 中。

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


