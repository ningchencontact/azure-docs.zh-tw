---
title: Windows Server 節點集區 Azure Kubernetes Service (AKS) 中的限制
description: 了解已知的限制，當您執行 Windows Server 的節點集區和應用程式工作負載在 Azure Kubernetes Service (AKS)
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: twhitney
ms.openlocfilehash: 12fb9dc67e8afae3dcb9ade97dd61ab438e0fac5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475413"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Windows Server 節點集區和應用程式工作負載在 Azure Kubernetes Service (AKS) 目前的限制

Azure Kubernetes Service (AKS) 中，您可以建立在節點執行客體 OS 的 Windows Server 的節點集區。 這些節點可以執行原生的 Windows 容器應用程式，例如.NET Framework 所建置的。 因為有在 Linux 和 Windows 作業系統提供的容器支援的主要差異，一些常用的 Kubernetes 和 pod 相關功能並非目前適用於 Windows 的節點集區。

這篇文章會說明一些限制和 AKS 中的 Windows 伺服器節點的作業系統概念。 適用於 Windows Server 的節點集區目前為預覽狀態。

> [!IMPORTANT]
> AKS 預覽功能包括自助、 選擇加入。 它們可供收集從我們的社群的意見及 bug。 在預覽中，這些功能不適用於實際執行環境。 在公開預覽功能底下 '盡力' 支援。 AKS 技術支援小組的協助時可使用營業時間太平洋 」 (PST) 僅限 timezone。 如需詳細資訊，請參閱下列支援文章：
>
> * [AKS 支援原則][aks-support-policies]
> * [Azure 支援常見問題集][aks-faq]

## <a name="limitations-for-windows-server-in-kubernetes"></a>適用於在 Kubernetes 中的 Windows Server 的限制

Windows Server 容器必須在以 Windows 為基礎的容器主機上執行。 若要在 AKS 中執行 Windows Server 容器，您可以[建立執行 Windows Server 的節點集區][ windows-node-cli]客體 OS。 視窗伺服器節點集區支援包括屬於上游 Kubernetes 專案中的 Windows Server 的一些限制。 這些限制不 AKS 所特有。 如需有關此上游的支援，適用於在 Kubernetes 中的 Windows Server 的詳細資訊，請參閱[Kubernetes 限制中的 Windows Server 容器][upstream-limitations]。

下列的上游限制適用於在 Kubernetes 中的 Windows Server 容器是與 AKS 相關：

- Windows Server 容器只能使用 Windows Server 2019，用來比對的基礎 Windows Server 節點 OS。
    - 建立使用 Windows Server 2016，因為基底 OS 不支援的容器映像。
- 無法使用特殊權限的容器。
- Linux 特定的功能，例如 RunAsUser、 SELinux、 AppArmor 或 POSIX 功能無法在 Windows Server 容器。
    - 檔案系統限制每個使用者的權限是 Linux 特有 UUI/GUID，例如，也無法在 Windows Server 容器。
- Azure 磁碟和 Azure 檔案是支援的磁碟區類型，存取 Windows Server 容器中的 NTFS 磁碟區。
    - NFS 型存放裝置磁碟區不支援。

## <a name="aks-limitations-for-windows-server-node-pools"></a>Windows Server 的節點集區的 AKS 限制

下列額外的限制適用於 AKS 中的 Windows Server 節點集區支援：

- AKS 叢集一律會包含作為第一個節點集區的 Linux 節點集區。 無法刪除此第一個以 Linux 為基礎的節點集區，除非刪除 AKS 叢集本身。
- 目前，AKS 僅支援基本負載平衡器，其只允許一個後端集區，預設的 Linux 節點集區。 如此一來，一律為輸出流量從 Windows pod[轉譯為 Azure 受控公用 IP 位址][azure-outbound-traffic]。 因為此 IP 位址不是可設定的所以不目前可能來自 Windows pod 的白名單流量。 
- AKS 叢集必須使用 Azure CNI （進階） 的網路模型。
    - 不支援 Kubenet （基本） 網路。 您無法建立使用 kubenet 的 AKS 叢集。 如需有關網路模型中的差異的詳細資訊，請參閱 <<c0> [ 網路 AKS 中的應用程式的概念][azure-network-models]。
    - Azure CNI 網路模型會需要額外的規劃和 IP 位址管理考量。 如需有關如何規劃及實作 Azure CNI 的詳細資訊，請參閱 < [AKS 中的設定 Azure CNI 網路][configure-azure-cni]。
- 必須是 Windows 伺服器節點，在 AKS*升級*更新及最新的 Windows Server 2019 版本維持最新的修補程式修正。 在 AKS 中的基底的節點映像，不會啟用 Windows 更新。 Windows Update 發行週期和您自己的驗證程序的定期排程，您應該在 AKS 叢集中執行 Windows Server 的節點集區上的升級。 如需有關如何升級 Windows Server 的節點集區的詳細資訊，請參閱 <<c0> [ 升級 AKS 中的節點集區][nodepool-upgrade]。
    - 這些 Windows Server 節點升級暫時會耗用額外的 IP 位址，虛擬網路子網路中部署新的節點時，移除舊的節點之前。
    - 部署新的節點時，也會暫時 vCPU 配額使用訂用帳戶，然後移除舊的節點。
    - 您無法自動更新，並管理使用的重新開機`kured`與 AKS 中的 Linux 節點。
- AKS 叢集中可以有最多八個節點的集區。
    - 您可以跨這八個節點的集區有最多 400 個節點。
- Windows Server 的節點集區名稱有 6 個字元的限制。
- 在 AKS 中的預覽功能，例如網路原則與叢集 autoscaler 未背書、 適用於 Windows Server 節點。
- 輸入控制器，始應排程在使用 NodeSelector 的 Linux 節點上。
- Azure 開發人員空間是目前僅適用於以 Linux 為基礎的節點集區。
- 群組受管理的服務帳戶 (gMSA) 支援的 Windows Server 節點未加入 Active Directory 網域時不 AKS 中目前可用。
    - 開放原始碼，上游[aks 引擎][ aks-engine]專案目前並未提供 gMSA 的支援，如果您要使用這項功能。

## <a name="os-concepts-that-are-different"></a>不同的 OS 概念

Kubernetes 是過去以 Linux 為主。 許多範例用於上游[Kubernetes.io] [ kubernetes]網站僅供在 Linux 節點上的使用。 當您建立使用 Windows Server 容器，在 OS 層級套用下列項目容器的部署：

- **身分識別**-Linux 會使用使用者識別碼 (UID) 和 groupID (GID)，表示為整數型別。 使用者和群組的名稱不是標準-這些都只是中的別名*eg /etc/ 群組*或是*eg /etc/ passwd*回到 UID + GID。
    - Windows Server 會使用較大二進位安全性識別碼 (SID 會儲存在 Windows 安全性存取管理員 (SAM) 資料庫)。 這個資料庫不會在主機與容器之間或容器之間共用。
- **檔案權限**-Windows Server 會使用存取控制清單為基礎的 Sid，而不是位元遮罩的權限與 UID + GID
- **檔案路徑**-Windows Server 上的慣例會使用 \ 而不是 /。
    - 在掛接磁碟區的 pod 規格，指定正確的 Windows Server 容器的路徑。 比方說，而不是掛接點的 */mnt 大量*在 Linux 容器中，指定磁碟機代號和位置這類*K/磁碟區*做為掛接*k:* 磁碟機。

## <a name="next-steps"></a>後續步驟

若要開始使用 AKS，在 Windows Server 容器[建立執行 Windows Server 在 AKS 中的節點集區][windows-node-cli]。

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
