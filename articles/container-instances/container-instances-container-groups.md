---
title: Azure Container Instances 容器群組
description: 瞭解多容器群組在 Azure 容器實例中的工作方式
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 03/20/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: cc9b11ba5fe0cd015d0879f28b9e85fb46b11955
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178587"
---
# <a name="container-groups-in-azure-container-instances"></a>Azure Container Instances 中的容器群組

在 Azure Container Instances 中，最上層的資源就是容器群組。 本文說明容器群組為何，以及這些群組能夠實現的案例類型。

## <a name="how-a-container-group-works"></a>容器群組的運作方式

容器群組是容器的集合，這些容器會排程在相同的主機電腦上。 容器群組中的容器會共用生命週期、資源、區域網路和存放磁片區。 它在概念上類似于[Kubernetes][kubernetes-pod]中的*pod* 。

下圖舉例說明包含多個容器的容器群組：

![容器群組圖表][container-groups-example]

此範例群組容器：

* 已排程在單一主機電腦上。
* 受指派 DNS 名稱標籤。
* 會公開單一的公用 IP 位址，以及一個公開的連接埠。
* 包括兩個容器。 一個容器接聽連接埠 80，另一個容器接聽連接埠 5000。
* 包含兩個 Azure 檔案共用作為磁碟區掛接，且每個容器會在本機掛接其中一個共用。

> [!NOTE]
> 多容器群組目前僅支援 Linux 容器。 針對 Windows 容器, Azure 容器實例僅支援部署單一實例。 雖然我們正致力於將所有功能帶入 Windows 容器, 但是您可以在服務[總覽](container-instances-overview.md#linux-and-windows-containers)中找到目前的平臺差異。

## <a name="deployment"></a>部署

以下是兩種部署多容器群組的常見方式：使用[Resource Manager 範本][resource-manager template]或[YAML][yaml-file]檔案。 當您部署容器實例時，如果需要部署其他 Azure 服務資源（例如， [Azure 檔案儲存體共用][azure-files]），建議使用 Resource Manager 範本。 由於 YAML 格式的本質較簡潔, 當您的部署只包含容器實例時, 建議使用 YAML 檔案。 如需您可以設定之屬性的詳細資訊，請參閱[Resource Manager 範本參考](/azure/templates/microsoft.containerinstance/containergroups)或[YAML 參考](container-instances-reference-yaml.md)檔。

若要保留容器群組的設定, 您可以使用 Azure CLI 命令[az container export][az-container-export], 將設定匯出至 YAML 檔案。 匯出可讓您將容器群組設定儲存在版本控制中, 以取得「設定即程式碼」。 或者，在 YAML 中開發新組態時，您可以使用匯出的檔案作為起點。



## <a name="resource-allocation"></a>資源配置

Azure 容器實例會藉由新增群組中實例的[資源要求][resource-requests]，將 cpu、記憶體和選擇性[gpu][gpus] （預覽）等資源配置給容器群組。 以 CPU 資源為例, 如果您建立具有兩個實例的容器群組, 每個都要求1個 CPU, 則容器群組會配置2個 cpu。

容器群組可用的資源上限取決於用於部署的[Azure 區域][region-availability]。

### <a name="container-resource-requests-and-limits"></a>容器資源要求和限制

* 根據預設, 群組中的容器實例會共用所要求的群組資源。 在具有兩個實例 (每個都要求1個 CPU) 的群組中, 整組群組可以存取2個 cpu。 每個實例最多可以使用2個 Cpu, 而實例在執行時可能會競爭 CPU 資源。

* 若要依據群組中的實例來限制資源使用量, 請選擇性地設定實例的[資源限制][resource-limits]。 在有兩個實例要求1個 CPU 的群組中, 其中一個容器可能需要更多的 Cpu 才能執行。

  在此案例中, 您可以為一個實例設定 0.5 CPU 的資源限制, 並將第二個 cpu 的限制設為2個。 此設定會將第一個容器的資源使用量限制為 0.5 CPU, 讓第二個容器可以使用最多一個完整的2個 Cpu (如果有的話)。

如需詳細資訊, 請參閱容器群組 REST API 中的[ResourceRequirements][resource-requirements]屬性。

### <a name="minimum-and-maximum-allocation"></a>配置的最小和最大值

* 為容器群組配置**最少**1 個 CPU 和 1 GB 的記憶體。 群組內的個別容器實例可以布建少於1個 CPU 和 1 GB 的記憶體。 

* 如需容器群組中的資源**上限**, 請參閱部署區域中 Azure 容器實例的[資源可用性][region-availability]。

## <a name="networking"></a>網路功能

容器群組會共用 IP 位址以及該 IP 位址上的連接埠命名空間。 若要讓外部用戶端能夠連線到該群組內的容器，您必須從該容器公開該 IP 位址上的連接埠。 因為群組中的容器會共用埠命名空間, 所以不支援埠對應。 群組內的容器可以透過 localhost 在其公開的埠上透過 localhost 彼此連接, 即使這些埠不會在群組的 IP 位址外部公開。

選擇性地將容器群組部署至[Azure 虛擬網路][virtual-network](預覽), 讓容器能夠安全地與虛擬網路中的其他資源進行通訊。

## <a name="storage"></a>儲存體

您可以指定要在容器群組內掛接的外部磁碟區。 您可以將這些磁碟區對應到群組之個別容器內的特定路徑。

## <a name="common-scenarios"></a>常見案例

如果您想要將單一功能性工作分割成少量的容器映像，多個容器的群組會很實用。 然後，這些映像就可以由不同小組傳遞，且具有個別的資源需求。

使用範例可能包括：

* 一個容器提供 Web 應用程式，一個容器從原始檔控制提取最新內容。
* 一個應用程式容器和一個記錄容器。 記錄容器收集主應用程式所輸出的記錄和計量，並將這些資料寫入到長期存放區。
* 一個應用程式容器和一個監視容器。 監視容器會定期向應用程式發出要求，以確保它會保持執行狀態並正確回應，如果沒有正確回應則引發警示。
* 前端容器和後端容器。 前端可能會提供 web 應用程式, 後端會執行服務來抓取資料。 

## <a name="next-steps"></a>後續步驟

了解如何使用 Azure Resource Manager 範本來部署多個容器的容器群組：

> [!div class="nextstepaction"]
> [部署容器群組][resource-manager template]

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/

<!-- LINKS - Internal -->
[resource-manager template]: container-instances-multi-container-group.md
[yaml-file]: container-instances-multi-container-yaml.md
[region-availability]: container-instances-region-availability.md
[resource-requests]: /rest/api/container-instances/containergroups/createorupdate#resourcerequests
[resource-limits]: /rest/api/container-instances/containergroups/createorupdate#resourcelimits
[resource-requirements]: /rest/api/container-instances/containergroups/createorupdate#resourcerequirements
[azure-files]: container-instances-volume-azure-files.md
[virtual-network]: container-instances-vnet.md
[gpus]: container-instances-gpu.md
[az-container-export]: /cli/azure/container#az-container-export
