---
title: 從 Azure Container Service (ACS) 移轉至 Azure Kubernetes Service (AKS)
description: 從 Azure Container Service (ACS) 移轉到 Azure Kubernetes Service (AKS)。
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: dcee8da943603fb0978caf9992be76347ca197d6
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2019
ms.locfileid: "65977705"
---
# <a name="migrate-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>從 Azure Container Service (ACS) 移轉至 Azure Kubernetes Service (AKS)

這篇文章可協助您規劃及執行 Azure Container Service (ACS) 搭配 Kubernetes 和 Azure Kubernetes Service (AKS) 之間移轉成功。 為了協助您做出關鍵決定，本指南詳細說明 ACS 與 AKS 之間的差異，並提供移轉程序的概觀。

## <a name="differences-between-acs-and-aks"></a>ACS 與 AKS 之間的差異

ACS 和 AKS 的影響移轉的某些重要區域不同。 任何移轉前，您應該檢閱，並計劃解決下列差異：

* 使用 AKS 節點[受控磁碟](../virtual-machines/windows/managed-disks-overview.md)。
    * 必須先轉換非受控的磁碟，您可以將它們附加至 AKS 節點。
    * 自訂`StorageClass`物件必須從變更 Azure 磁碟`unmanaged`至`managed`。
    * 任何`PersistentVolumes`應該使用`kind: Managed`。
* AKS 支援[多個節點的集區](https://docs.microsoft.com/azure/aks/use-multiple-node-pools)（目前處於預覽狀態）。
* Windows Server 為基礎的節點是目前[AKS 中的預覽](https://azure.microsoft.com/blog/kubernetes-on-azure/)。
* AKS 支援有限的一組[區域](https://docs.microsoft.com/azure/aks/quotas-skus-regions)。
* AKS 是受控服務，其中包含託管的 Kubernetes 控制平面。 您可能需要修改您的應用程式，如果您之前曾修改您的 ACS 主機的組態。

## <a name="differences-between-kubernetes-versions"></a>Kubernetes 版本之間的差異

如果您要移轉至較新版的 Kubernetes （例如，從以 1.9.x 1.7.x)，請檢閱下列資源以了解 Kubernetes API 的一些變更：

* [將 ThirdPartyResource 移轉至 CustomResourceDefinition](https://kubernetes.io/docs/tasks/access-kubernetes-api/migrate-third-party-resource/)
* [1.8 和 1.9 版中的工作負載 API 變更](https://kubernetes.io/docs/reference/workloads-18-19/)

## <a name="migration-considerations"></a>移轉考量

### <a name="agent-pools"></a>代理程式集區

雖然 AKS 管理 Kubernetes 控制平面，您仍會定義要包含在新叢集中的節點數目與大小。 假設您想要以 1:1 的方式將 AKS 對應至 ACS，您需要擷取現有的 ACS 節點資訊。 當您建立新的 AKS 叢集時，請使用此資料。

範例：

| 名稱 | 計數 | VM 大小 | 作業系統 |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 |  Linux |
| agentpool1 | 1 | Standard_D2_v2 |  Windows |

由於在移轉期間會有額外的虛擬機器部署到訂用帳戶，因此您應確認您的配額與限制足以讓這些資源使用。 

如需詳細資訊，請參閱 < [Azure 訂用帳戶和服務限制](https://docs.microsoft.com/azure/azure-subscription-service-limits)。 若要檢查您目前的配額，在 Azure 入口網站中，移至[訂用帳戶 刀鋒視窗](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)，選取您的訂用帳戶，然後選取**使用量 + 配額**。

### <a name="networking"></a>網路功能

對於複雜的應用程式，您的遷移作業通常會進行一段時間，而非全部一次完成。 這表示，舊的和新的環境可能需要透過網路進行通訊。 先前使用的應用程式`ClusterIP`通訊的服務可能需要公開類型為`LoadBalancer`並適當地加以保護。

若要完成移轉，您會想要用戶端指向新的服務在 AKS 上執行。 我們建議您將重新導向流量，藉由更新 DNS 以指向位於您的 AKS 叢集前方負載平衡器。

### <a name="stateless-applications"></a>無狀態應用程式

無狀態應用程式移轉是最直接的案例。 將 YAML 定義套用到新叢集，請確定一切運作正常，和啟用您的新叢集的流量重新導向。

### <a name="stateful-applications"></a>具狀態應用程式

仔細規劃您的具狀態的應用程式，以避免資料遺失或未預期的停機時間的移轉。

#### <a name="highly-available-applications"></a>高可用性應用程式

您可以部署一些高可用性組態中的具狀態應用程式。 這些應用程式可以在複本之間複製資料。 如果您目前使用這種部署，您可能無法在新的 AKS 叢集上建立新的成員，然後再移轉與下游的呼叫端的影響降至最低。 一般而言，此案例的移轉步驟如下：

1. 在 AKS 上建立新的次要複本。
2. 等候要複寫的資料。
3. 無法超過將新的主要複本的次要複本。
4. 您可以將流量導向 AKS 叢集。

#### <a name="migrating-persistent-volumes"></a>永續性磁碟區移轉

如果您要移轉現有的永續性磁碟區到 AKS，您通常會遵循下列步驟：

1. 停止寫入應用程式。 （此步驟是選擇性的而且需要停機時間）。
2. 需要磁碟的快照集。
3. 從快照集建立新的受控的磁碟。
4. 在 AKS 建立永續性磁碟區。
5. 更新至的 pod 規格[使用現有的磁碟區](https://docs.microsoft.com/azure/aks/azure-disk-volume)而不是 PersistentVolumeClaims （靜態佈建）。
6. 部署至 AKS 應用程式。
7. 驗證。
8. 您可以將流量導向 AKS 叢集。

> [!IMPORTANT]
> 如果您選擇不要停止寫入，您必須將資料複寫到新的部署。 否則，您將會錯失您花了磁碟快照集之後寫入的資料。

某些開放原始碼工具可協助您建立的受控的磁碟和磁碟區，Kubernetes 叢集之間移轉：

* [Azure CLI 的磁碟複本擴充功能](https://github.com/noelbundick/azure-cli-disk-copy-extension)複製，並將於資源群組和 Azure 區域的磁碟。
* [Azure Kubernetes CLI 擴充功能](https://github.com/yaron2/azure-kube-cli)列舉 ACS Kubernetes 磁碟區，並將它們移轉至 AKS 叢集。

#### <a name="azure-files"></a>Azure 檔案

不同於磁碟，Azure 檔案服務可同時掛接到多部主機。 在 AKS 叢集中，Azure 和 Kubernetes 不會阻止您建立的 pod，仍會使用您的 ACS 叢集。 若要避免資料遺失和非預期的行為，請確定叢集不在此同時寫入相同的檔案。

如果您的應用程式可以裝載多個指向相同的檔案共用的複本，遵循無狀態的移轉步驟，並將您的 YAML 定義部署到新叢集。 如果不是，則可能的移轉方法包含下列步驟：

1. 應用程式部署至 AKS 複本計數為 0。
2. 在 ACS 可以擴充應用程式設為 0。 （此步驟需要停機時間）。
3. 小數位數最多 1 在 AKS 上應用程式。
4. 驗證。
5. 您可以將流量導向 AKS 叢集。

如果您想要開始使用空白的共用和讓來源資料的複本，您可以使用[ `az storage file copy` ](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest)命令來移轉您的資料。

### <a name="deployment-strategy"></a>部署策略

我們建議您將已知良好組態部署到 AKS 使用您現有的 CI/CD 管線。 複製現有的部署工作，並確定`kubeconfig`指向新的 AKS 叢集。

如果不可行，從 ACS 匯出資源定義，然後將它們套用至 AKS。 您可以使用 `kubectl` 匯出物件。

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

根據您的部署需要數個開放原始碼工具可協助：

* [Velero](https://github.com/heptio/ark) （這個工具需要 Kubernetes 1.7）。
* [Azure Kubernetes CLI 擴充功能](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>移轉步驟

1. [建立 AKS 叢集](https://docs.microsoft.com/azure/aks/create-cluster)透過 Azure 入口網站、 Azure CLI 或 Azure Resource Manager 範本。

   > [!NOTE]
   > 尋找在 AKS 中的範例 Azure Resource Manager 範本[Azure/AKS](https://github.com/Azure/AKS/tree/master/examples/vnet) GitHub 存放庫。

2. YAML 定義進行任何必要的變更。 比方說，取代`apps/v1beta1`具有`apps/v1`如`Deployments`。

3. [移轉磁碟區](#migrating-persistent-volumes)從您的 ACS 叢集的 （選擇性），若您的 AKS 叢集。

4. 您可以使用 CI/CD 系統應用程式部署到 AKS。 或使用 kubectl 來套用 YAML 定義。

5. 驗證。 請確定您的應用程式會在如預期般運作，而且已透過複製任何已移轉的資料。

6. 將流量重新導向。 將 DNS 更新為將用戶端指向您的 AKS 部署。

7. 完成移轉後工作。 如果您移轉的磁碟區，並選擇不停止寫入，請將該資料複製到新叢集。
