---
title: "Azure Container Instances 容器群組"
description: "了解容器群組在 Azure Container Instances 中的運作方式"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 12/19/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: a42c01917926a4297c97cf9c5dfd1333dbef6793
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2018
---
# <a name="container-groups-in-azure-container-instances"></a>Azure 容器執行個體中的容器群組

Azure 容器執行個體的最上層資源*容器群組*。 本文說明的案例，可以讓型別與容器群組為何。

## <a name="how-a-container-group-works"></a>容器群組的運作方式

容器群組是取得排定在同一部主機機器的容器的集合。 容器群組中的容器共用的生命週期、 區域網路和存放磁碟區。 類似於的概念*pod*中[Kubernetes] [ kubernetes-pod]和[DC/OS][dcos-pod]。

下圖舉例說明包含多個容器的容器群組。

![容器群組圖表][container-groups-example]

此群組的範例容器：

* 會排定在單一主機上。
* 會公開單一公用 IP 位址，與一個公開的連接埠。
* 包含兩個容器。 一個容器接聽連接埠 80，另一個容器接聽連接埠 5000。
* 包含兩個 Azure 檔案共用做為磁碟區掛接和每個容器裝載其中一個在本機的共用資料夾。

> [!NOTE]
> 多個容器群組僅限於目前 Linux 容器。 雖然我們正在將 Windows 容器中的所有功能，您可以找到目前平台差異[配額和 Azure 容器執行個體的區域可用性](container-instances-quotas.md)。

### <a name="networking"></a>網路

容器群組會共用 IP 位址以及該 IP 位址上的連接埠命名空間。 若要讓外部用戶端能夠連線到該群組內的容器，您必須從該容器公開該 IP 位址上的連接埠。 群組內的容器會共用連接埠命名空間，因此不支援連接埠對應。 群組內的容器可以在它們已公開的連接埠上透過 localhost 彼此連線，即使這些連接埠並未在群組的 IP 位址上對外公開也是如此。

### <a name="storage"></a>儲存體

您可以指定要在容器群組內掛接的外部磁碟區。 您可以將這些磁碟區對應到群組之個別容器內的特定路徑。

## <a name="common-scenarios"></a>常見案例

如果您想要將單一功能的工作分割成少量的容器映像，以便由不同小組來提供並讓這些映像具有不同的資源需求，則多個容器的群組會很實用。

使用範例可能包括：

* 一個應用程式容器和一個記錄容器。 記錄容器收集主應用程式所輸出的記錄和計量，並將這些資料寫入到長期存放區。
* 一個應用程式和一個監視容器。 定期監視容器會以確保它正在執行，並正確地回應，並引發警示，如果不是應用程式提出要求。
* 一個容器提供 Web 應用程式，一個容器從原始檔控制提取最新內容。

## <a name="next-steps"></a>後續步驟

了解如何使用 Azure Resource Manager 範本來[部署多個容器的群組](container-instances-multi-container-group.md)。

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/
