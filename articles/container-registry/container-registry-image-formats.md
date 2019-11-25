---
title: Supported content formats
description: Learn about content formats supported by Azure Container Registry, including Docker-compatible container images, Helm charts, OCI images, and OCI artifacts.
ms.topic: article
ms.date: 08/30/2019
ms.openlocfilehash: ab915385f46f83c7b655acd1a48d66df84b50653
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455012"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Azure Container Registry 中支援的內容格式

使用 Azure Container Registry 中的私人存放庫，可管理其下列中一種內容格式。 

## <a name="docker-compatible-container-images"></a>與 Docker 相容的容器映像

The following Docker container image formats are supported:

* [Docker 映像資訊清單 V2，結構描述 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker 映像資訊清單 V2，結構描述 2](https://docs.docker.com/registry/spec/manifest-v2-2/) -包含允許登錄將多平台映像儲存在單一 "image:tag" 參考下的資訊清單

## <a name="oci-images"></a>OCI images

Azure Container Registry supports images that meet the [Open Container Initiative (OCI) Image Format Specification](https://github.com/opencontainers/image-spec/blob/master/spec.md). Packaging formats include [Singularity Image Format (SIF)](https://github.com/sylabs/sif).

## <a name="oci-artifacts"></a>OCI artifacts

Azure Container Registry supports the [OCI Distribution Specification](https://github.com/opencontainers/distribution-spec), a vendor-neutral, cloud-agnostic spec to store, share, secure, and deploy container images and other content types (artifacts). The specification allows a registry to store a wide range of artifacts in addition to container images. You use tooling appropriate to the artifact to push and pull artifacts. For an example, see [Push and pull an OCI artifact using an Azure container registry](container-registry-oci-artifacts.md).

To learn more about OCI artifacts, see the [OCI Registry as Storage (ORAS)](https://github.com/deislabs/oras) repo and the [OCI Artifacts](https://github.com/opencontainers/artifacts) repo on GitHub.

## <a name="helm-charts"></a>Helm 圖表

Azure Container Registry can host repositories for [Helm charts](https://helm.sh/), a packaging format used to quickly manage and deploy applications for Kubernetes. [Helm client](https://docs.helm.sh/using_helm/#installing-helm) version 2 (2.11.0 or later) is supported.

## <a name="next-steps"></a>後續步驟

* 了解如何使用 Azure Container Registry 來[推送和提取](container-registry-get-started-docker-cli.md)映像。

* 使用 [ACR 工作](container-registry-tasks-overview.md)建置和測試容器映像。 

* 使用 [Moby BuildKit](https://github.com/moby/buildkit) 建置和封裝 OCI 格式的容器。

* 設定裝載在 Azure Container Registry 中的 [Helm 存放庫](container-registry-helm-repos.md)。 


