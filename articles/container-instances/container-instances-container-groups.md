---
title: Azure Container Instances 容器群組
description: 了解如何在多個容器群組在 Azure Container Instances 中的工作
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/20/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 8724bd7e13b0d8607ad5a6814b27c8c06681f331
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202005"
---
# <a name="container-groups-in-azure-container-instances"></a>Azure Container Instances 中的容器群組

在 Azure Container Instances 中，最上層的資源就是容器群組。 本文說明容器群組為何，以及這些群組能夠實現的案例類型。

## <a name="how-a-container-group-works"></a>容器群組的運作方式

容器群組是容器的集合，這些容器會排程在相同的主機電腦上。 容器群組中的容器會共用生命週期、 資源、 區域網路和儲存體磁碟區。 類似的概念*pod*中[Kubernetes][kubernetes-pod]。

下圖舉例說明包含多個容器的容器群組：

![容器群組圖表][container-groups-example]

此範例群組容器：

* 已排程在單一主機電腦上。
* 受指派 DNS 名稱標籤。
* 會公開單一的公用 IP 位址，以及一個公開的連接埠。
* 包括兩個容器。 一個容器接聽連接埠 80，另一個容器接聽連接埠 5000。
* 包含兩個 Azure 檔案共用作為磁碟區掛接，且每個容器會在本機掛接其中一個共用。

> [!NOTE]
> 多個容器群組目前支援只有 Linux 容器。 適用於 Windows 容器 Azure 容器執行個體僅支援單一執行個體的部署。 雖然我們正努力帶入 Windows 容器中的所有功能，您可以找到在服務中目前的平台差異[概觀](container-instances-overview.md#linux-and-windows-containers)。

## <a name="deployment"></a>部署

以下是兩個常見的方式來部署多容器群組： 使用[Resource Manager 範本][ resource-manager template]或是[YAML 檔案][yaml-file]。 使用 Resource Manager 範本，當您需要部署額外的 Azure 服務資源 (例如[Azure 檔案共用][azure-files]) 時，您部署的容器執行個體。 由於 YAML 格式的更簡潔的本質，YAML 檔案時，建議您的部署包含只有容器執行個體。

## <a name="resource-allocation"></a>資源配置

Azure 容器執行個體配置的資源，例如 Cpu、 記憶體，並選擇性地[Gpu] [ gpus] （預覽） 的容器群組加上[資源要求][resource-requests]群組中的執行個體。 例如，佔用 CPU 資源，如果您有兩個執行個體，每個要求的 1 建立的容器群組配置 2 個 Cpu 的 CPU，則容器群組。

容器群組的可用的最大資源取決於[Azure 區域][ region-availability]用於部署。

### <a name="container-resource-requests-and-limits"></a>容器資源的要求和限制

* 根據預設，群組中的容器執行個體共用群組的要求的資源。 在具有兩個群組執行個體的每個要求的 1 的 CPU，整個群組可存取 2 個 Cpu。 每個執行個體可使用最多 2 個 Cpu，並在執行時，競爭 CPU 資源的執行個體，可能會。

* 若要在群組中的執行個體所限制資源使用量，選擇性地設定[資源限制][ resource-limits]執行個體。 在具有兩個執行個體的群組要求 1 CPU，其中您容器可能需要比其他更多的 Cpu。

  在此案例中，您可以設定資源限制為 0.5 個 CPU 的一個執行個體與第二個的 2 個 Cpu 的限制。 此設定會限制為 0.5 的第一個容器的資源使用狀況允許第二個容器，如果有的話，用光完整 2 個 cpu 的 CPU。

如需詳細資訊，請參閱 < [ResourceRequirements] [ resource-requirements]容器中的屬性群組的 REST API。

### <a name="minimum-and-maximum-allocation"></a>最小值和最大的配置

* 配置**最小**1 的 CPU 和 1 GB 記憶體給容器群組。 群組內的個別容器執行個體，可以使用小於 1 佈建 CPU 和 1 GB 的記憶體。 

* 針對**最大**容器群組中的資源，請參閱 [資源可用性] [aci-區域-可用性] 部署區域中的 Azure 容器執行個體。

## <a name="networking"></a>網路功能

容器群組會共用 IP 位址以及該 IP 位址上的連接埠命名空間。 若要讓外部用戶端能夠連線到該群組內的容器，您必須從該容器公開該 IP 位址上的連接埠。 群組內的容器會共用連接埠的命名空間，因為不支援連接埠對應。 群組內的容器可以觸達彼此透過 localhost 上的連接埠，它們已公開的即使這些連接埠不群組的 IP 位址上對外公開。

選擇性地部署到容器群組[Azure 虛擬網路][ virtual-network] （預覽），以便安全地進行通訊的虛擬網路中的其他資源的容器。

## <a name="storage"></a>儲存體

您可以指定要在容器群組內掛接的外部磁碟區。 您可以將這些磁碟區對應到群組之個別容器內的特定路徑。

## <a name="common-scenarios"></a>常見案例

如果您想要將單一功能性工作分割成少量的容器映像，多個容器的群組會很實用。 然後，這些映像就可以由不同小組傳遞，且具有個別的資源需求。

使用範例可能包括：

* 一個容器提供 Web 應用程式，一個容器從原始檔控制提取最新內容。
* 一個應用程式容器和一個記錄容器。 記錄容器收集主應用程式所輸出的記錄和計量，並將這些資料寫入到長期存放區。
* 一個應用程式容器和一個監視容器。 監視容器會定期向應用程式發出要求，以確保它會保持執行狀態並正確回應，如果沒有正確回應則引發警示。
* 前端容器和一個後端容器。 前端可提供 web 應用程式與後端執行服務來擷取資料。 

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
