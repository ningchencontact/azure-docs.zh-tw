---
title: Azure Kubernetes Service 中 Windows Server 節點集區的限制 (AKS)
description: 瞭解在 Azure Kubernetes Service 中執行 Windows Server 節點集區和應用程式工作負載時的已知限制 (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: c2c9e3d29ced5f75873656e253ecdbab5efe7df8
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114413"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service 中 Windows Server 節點集區和應用程式工作負載目前的限制 (AKS)

在 Azure Kubernetes Service (AKS) 中, 您可以建立執行 Windows Server 的節點集區, 做為節點上的客體作業系統。 這些節點可以執行原生 Windows 容器應用程式, 例如建立在 .NET Framework 上的。 因為 Linux 和 Windows OS 如何提供容器支援的主要差異, 所以某些常見的 Kubernetes 和 pod 相關功能目前不適用於 Windows 節點集區。

本文概述 AKS 中 Windows Server 節點的一些限制和 OS 概念。 適用于 Windows Server 的節點集區目前為預覽狀態。

> [!IMPORTANT]
> AKS 預覽功能是自助加入宣告。 預覽會以「原樣」和「可用」的方式提供, 並從服務等級協定中排除, 並享有有限擔保。 AKS 預覽的部分是由客戶支援, 以最大的方式來涵蓋。 因此, 這些功能並不適用于生產環境使用。 如需其他資訊, 請參閱下列支援文章:
>
> * [AKS 支援原則][aks-support-policies]
> * [Azure 支援常見問題集][aks-faq]

## <a name="limitations-for-windows-server-in-kubernetes"></a>Kubernetes 中 Windows Server 的限制

Windows Server 容器必須在以 Windows 為基礎的容器主機上執行。 若要在 AKS 中執行 Windows Server 容器, 您可以[建立執行 Windows server 的節點集][windows-node-cli]區做為虛擬作業系統。 Windows Server 節點集區支援包含在 Kubernetes 專案中屬於上游 Windows Server 的一些限制。 這些限制並不是 AKS 特有的。 如需有關此 Kubernetes 中 Windows Server 上游支援的詳細資訊, 請參閱[Windows server 容器中的 Kubernetes 限制](https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations)。

Kubernetes 中 Windows Server 容器的下列上游限制與 AKS 相關:

- Windows Server 容器只能使用符合基礎 Windows Server 節點 OS 的 Windows Server 2019。
    - 不支援使用 Windows Server 2016 建立的容器映射作為基底 OS。
- 無法使用具特殊許可權的容器。
- Linux 特有的功能 (例如 RunAsUser、SELinux、AppArmor 或 POSIX 功能) 無法在 Windows Server 容器中使用。
    - Windows Server 容器中也不提供 Linux 特定的檔案系統限制, 例如 UUI/GUID、每個使用者的許可權。
- Azure 磁片和 Azure 檔案儲存體是支援的磁片區類型, 以 Windows Server 容器中的 NTFS 磁片區來存取。
    - 不支援以 NFS 為基礎的儲存體/磁片區。

## <a name="aks-limitations-for-windows-server-node-pools"></a>Windows Server 節點集區的 AKS 限制

下列其他限制適用于 AKS 中的 Windows Server 節點集區支援:

- AKS 叢集一律會包含 Linux 節點集區作為第一個節點集區。 除非 AKS 叢集本身已刪除, 否則無法刪除這個第一個以 Linux 為基礎的節點集區。
- AKS 叢集必須使用 Azure CNI (advanced) 網路模型。
    - 不支援 Kubenet (基本) 網路功能。 您無法建立使用 kubenet 的 AKS 叢集。 如需網路模型差異的詳細資訊, 請參閱[AKS 中應用程式的網路概念][azure-network-models]。
    - Azure CNI 網路模型需要額外的 IP 位址管理規劃和考慮。 如需如何規劃和執行 Azure CNI 的詳細資訊, 請參閱[在 AKS 中設定 AZURE CNI 網路][configure-azure-cni]。
- AKS 中的 windows Server 節點必須*升級*至最新的 windows server 2019 版本, 以維護最新的修補程式修正和更新。 AKS 中的基底節點映射未啟用 Windows 更新。 依照 Windows Update 發行週期和您自己的驗證程式的定期排程, 您應該在 AKS 叢集中的 Windows Server 節點集區上執行升級。 如需有關升級 Windows Server 節點集區的詳細資訊, 請參閱[升級 AKS 中的節點集][nodepool-upgrade]區。
    - 在移除舊的節點之前, 這些 Windows Server 節點升級會暫時使用虛擬網路子網中的其他 IP 位址, 做為部署新節點的位置。
    - 當部署新節點時, vCPU 配額也會暫時在訂用帳戶中取用, 然後移除舊的節點。
    - 您無法使用`kured` as 搭配 AKS 中的 Linux 節點來自動更新及管理重新開機。
- AKS 叢集最多可以有八個節點集區。
    - 在這八個節點集區中, 您最多可以有400個節點。
- Windows Server 節點集區名稱的限制為6個字元。
- AKS 中的預覽功能 (例如網路原則和叢集自動調整程式) 不會背書 Windows Server 節點。
- 輸入控制器只應在使用 NodeSelector 的 Linux 節點上排程。
- Azure Dev Spaces 目前僅適用于以 Linux 為基礎的節點集區。
- 群組受管理的服務帳戶 (gMSA) 支援: 當 Windows Server 節點未加入 Active Directory 網域時, AKS 目前無法使用。
    - 如果您需要使用這項功能, 開放原始碼的上游[aks 引擎][aks-engine]專案目前提供了 gMSA 支援。

## <a name="os-concepts-that-are-different"></a>不同的作業系統概念

Kubernetes 在過去是以 Linux 為重點。 上游[Kubernetes.io][kubernetes]網站中使用的許多範例都是供 Linux 節點使用。 當您建立使用 Windows Server 容器的部署時, 適用于 OS 層級的下列考慮事項:

- 身分**識別**-Linux 使用 USERID (UID) 和 GROUPID (GID), 以整數類型表示。 使用者和組名不是標準的-它們只是 */etc/groups*中的別名, 或 */etc/passwd*回 UID + GID。
    - Windows Server 使用較大的二進位安全識別碼 (SID), 儲存在 Windows 安全性存取管理員 (SAM) 資料庫中。 此資料庫不會在主機與容器之間共用, 或在容器之間共用。
- 檔案**許可權**-Windows Server 使用以 sid 為基礎的存取控制清單, 而不是許可權和 UID + GID 的位元遮罩
- 檔案**路徑**-Windows Server 上的慣例是使用 \, 而不是/。
    - 在 pod 規格中, 載入磁片區, 為 Windows Server 容器指定正確的路徑。 例如, 不是 Linux 容器中 */mnt/volume*的掛接點, 而是指定磁碟機號和位置 (例如 */K/Volume* ) 來掛接為*K:* 磁片磁碟機。

## <a name="next-steps"></a>後續步驟

若要開始在 AKS 中使用 Windows Server 容器, 請[在 AKS 中建立執行 Windows server 的節點集][windows-node-cli]區。

<!-- LINKS - external -->
[upstream-limitations]: https://kubernetes.io/docs/setup/windows/#limitations
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
