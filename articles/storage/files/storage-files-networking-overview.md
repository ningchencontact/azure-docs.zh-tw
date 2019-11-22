---
title: Azure 檔案儲存體的網路功能考量 | Microsoft Docs
description: Azure 檔案儲存體的網路功能選項概觀。
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 596479652478bffb6d18a90fc53d5972b3839408
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2019
ms.locfileid: "73126534"
---
# <a name="azure-files-networking-considerations"></a>Azure 檔案儲存體的網路功能考量 
您可以透過兩種方式連線至 Azure 檔案共用：

- 直接透過 SMB 或 FileREST 通訊協定來存取共用。 此存取模式主要會在要盡可能減少所用的內部部署伺服器數目時使用。
- 使用 Azure 檔案同步在內部部署伺服器上建立 Azure 檔案共用的快取，並使用針對使用案例所選擇的通訊協定 (SMB、NFS、FTPS 等)，從內部部署伺服器存取檔案共用的資料。 此存取模式很方便，因為結合了內部部署效能與雲端級、無伺服器可連結服務 (例如 Azure 備份) 的最佳優勢。

本文會著重在說明當您的使用案例要直接存取 Azure 檔案共用而非使用 Azure 檔案同步時，該如何設定網路功能。如需 Azure 檔案同步部署的網路考量詳細資訊，請參閱[設定 Azure 檔案同步 Proxy 和防火牆設定](storage-sync-files-firewall-and-proxy.md)。

## <a name="storage-account-settings"></a>儲存體帳戶設定
儲存體帳戶是一種管理構造，所代表的是儲存體的共用集區，您可以在此集區中部署多個檔案共用，以及其他儲存體資源 (例如，Blob 容器或佇列)。 Azure 儲存體帳戶會公開兩組用來保護網路的基本設定，分別是傳輸中加密與防火牆和虛擬網路 (VNet)。

### <a name="encryption-in-transit"></a>傳輸中加密
根據預設，所有 Azure 儲存體帳戶都會啟用傳輸中加密。 這表示當您透過 SMB 掛接檔案共用或透過 FileREST 通訊協定 (例如，透過 Azure 入口網站、PowerShell/CLI 或 Azure SDK) 來存取檔案共用時，Azure 檔案儲存體只會在使用了 SMB 3.0 以上 (有加密功能) 或 HTTPS 來進行連線時，才會允許您建立連線。 不支援 SMB 3.0 的用戶端，或雖支援 SMB 3.0 但不支援 SMB 加密的用戶端，將無法在啟用了傳輸中加密的情況下掛接 Azure 檔案共用。 如需哪些作業系統支援 SMB 3.0 (有加密功能) 的詳細資訊，請參閱適用於 [Windows](storage-how-to-use-files-windows.md)、[macOS](storage-how-to-use-files-mac.md) 和 [Linux](storage-how-to-use-files-linux.md) 的詳細文件。 所有目前版本的 PowerShell、CLI 和 SDK 都支援 HTTPS。  

您可以為 Azure 儲存體帳戶停用傳輸中加密。 停用加密時，Azure 檔案儲存體也會允許 SMB 2.1、SMB 3.0 (沒有加密功能) 以及透過 HTTP 的未加密 FileREST API 呼叫。 會停用傳輸中加密的主要原因，是為了支援必須在舊版作業系統上執行的繼承應用程式，例如 Windows Server 2008 R2 或舊版 Linux 散發套件。 Azure 檔案儲存體只會在與 Azure 檔案共用相同的 Azure 區域內允許 SMB 2.1 連線；Azure 檔案共用所在的 Azure 區域外 (例如，內部部署或不同 Azure 區域) 的 SMB 2.1 用戶端，則無法存取檔案共用。

如需傳輸中加密的詳細資訊，請參閱[在 Azure 儲存體中需要安全傳輸](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

### <a name="firewalls-and-virtual-networks"></a>防火牆與虛擬網路 
防火牆是一種網路原則，此原則可讓要求存取您儲存體帳戶中的 Azure 檔案共用和其他儲存體資源。 使用預設網路功能設定所建立的儲存體帳戶不會受限於特定網路，因此可存取網際網路。 這並不表示網際網路上的任何人都可以存取儲存體帳戶中所裝載的 Azure 檔案共用上儲存的資料，儲存體帳戶只會接受來自任何網路、已獲得授權的要求。 若要授權要求，您可以使用儲存體帳戶金鑰、共用存取簽章 (SAS) 權杖 (僅限 FileREST) 或 Active Directory 使用者主體。 

儲存體帳戶的防火牆原則可用來限制針對特定 IP 位址或範圍或針對虛擬網路的存取。 一般而言，大部分的儲存體帳戶防火牆原則都會限制針對虛擬網路的網路存取。 

[虛擬網路](../../virtual-network/virtual-networks-overview.md) (或 VNet) 類似於您在自己的資料中心內運作的傳統網路。 其可讓您為 Azure 資源 (例如，Azure 檔案共用、VM、SQL Database 等) 建立安全通道，以便讓這些資源彼此通訊。 和 Azure 儲存體帳戶或 Azure VM 一樣，VNet 也是部署在資源群組中的 Azure 資源。 只要進行額外的網路設定，Azure VNet 也可連線至內部部署網路。

將 Azure VM 等資源新增至虛擬網路時，連結至虛擬機器的虛擬網路介面 (NIC) 會具體受限在該 VNet。 之所以有這種可能，是因為 Azure VM 是虛擬化電腦，因此當然會有 NIC。 虛擬機器會以 Azure 的基礎結構即服務 (IaaS) 產品系列的一部分來提供。 因為 Azure 檔案共用是無伺服器的檔案共用，所以不會有可供您新增至 VNet 的 NIC。 換句話說，Azure 檔案儲存體會作為 Azure 的平台即服務 (PaaS) 產品系列一部分來提供。 為了讓儲存體帳戶能夠成為 VNet 的一部分，Azure 支援稱為「服務端點」的 PaaS 服務概念。 服務端點可讓 PaaS 服務成為虛擬網路的一部分。 若要深入了解服務端點，請參閱[虛擬網路服務端點](../../virtual-network/virtual-network-service-endpoints-overview.md)。

一個儲存體帳戶可以新增至一或多個虛擬網路。 若要深入了解如何將儲存體帳戶新增至虛擬網路或設定其他防火牆設定，請參閱[設定 Azure 儲存體防火牆和虛擬網路](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

## <a name="azure-networking"></a>Azure 網路
根據預設，您可以透過網際網路存取包含 Azure 檔案儲存體在內的 Azure 服務。 由於流向儲存體帳戶的流量預設會進行加密 (而且 Azure 區域外永遠不會允許 SMB 2.1 掛接)，因此透過網際網路來存取 Azure 檔案共用本來就不會有所謂不安全的情況。 根據組織的原則或獨特的法規需求，您與 Azure 的通訊可能需要受到更多約束，因此 Azure 提供了數種方式來限制如何讓 Azure 外部的流量流向 Azure 檔案儲存體。 在存取 Azure 檔案共用時，您可以使用下列服務供應項目來進一步保護網路功能：

- [Azure VPN 閘道](../../vpn-gateway/vpn-gateway-about-vpngateways.md)：VPN 閘道是特定的虛擬網路閘道類型，可透過網際網路在 Azure 虛擬網路與替代位置 (例如，內部部署環境) 之間傳送加密流量。 Azure VPN 閘道是一種 Azure 資源，可供部署在儲存體帳戶或其他 Azure 資源的資源群組中。 VPN 閘道會公開兩種不同的連線類型：
    - [點對站 (P2S) VPN](../../vpn-gateway/point-to-site-about.md) 閘道連線，這是 Azure 與個別用戶端之間的 VPN 連線。 此解決方案主要適用於不屬於組織內部部署網路的裝置，例如，想要能夠從家裡、咖啡廳或外出時所住的飯店掛接其 Azure 檔案共用的遠距工作者。 若要搭配 Azure 檔案儲存體使用 P2S VPN 連線，則必須為每個想要連線的用戶端設定 P2S VPN 連線。 
    - [站對站 (S2S) VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)，這是 Azure 與您組織內網路之間的 VPN 連線。 S2S VPN 連線可讓您一次就為組織網路上裝載的 VPN 伺服器或裝置設定 VPN 連線，而不必針對需要存取 Azure 檔案共用的每個用戶端裝置進行設定。
- [ExpressRoute](../../expressroute/expressroute-introduction.md)，可讓您在 Azure 與不會周遊網際網路的內部部署網路之間建立已定義好的路由。 由於 ExpressRoute 會在您的內部部署資料中心與 Azure 之間提供專用路徑，因此若要將網路效能納入考量時，ExpressRoute 可能會很有用。 當組織的原則或法規需求要求您的雲端資源必須有確定的路徑時，ExpressRoute 也會是不錯的選擇。

## <a name="securing-access-from-on-premises"></a>保護來自內部部署網路的存取 
當您將一般用途的檔案共用 (例如，Office 文件、PDF、CAD 文件等等) 遷移到 Azure 檔案儲存體時，您的使用者通常還是需要從內部部署裝置 (例如，其工作站、筆記型電腦和平板電腦) 存取檔案。 針對一般用途的檔案共用，主要考量是內部部署使用者要如何才能安全地透過網際網路或 WAN 存取其檔案共用。

若要從內部部署網路存取 Azure 檔案共用，最簡單的方式就是對連接埠 445 (SMB 所用的連接埠) 開放內部部署網路，並掛接 Azure 入口網站所提供的 UNC 路徑。 這不需要任何特殊的網路功能。 因為 SMB 1.0 有已過時的安全性指引 (Microsoft 認為 SMB 1.0 不是擁有網際網路安全性的通訊協定)，導致許多客戶不太願意開放連接埠 445。 Azure 檔案儲存體並未實作 SMB 1.0。 

SMB 3.0 在設計時就有明確需求，那就是成為擁有網際網路安全性的檔案共用通訊協定。 因此，從電腦網路的觀點來看，在使用 SMB 3.0 以上時，開放連接埠 445 和開放連接埠 443 (用於 HTTPS 連線的連接埠) 並無不同。 若要防止不安全的 SMB 1.0 流量，Microsoft 會建議您使用下列步驟，而不是封鎖連接埠 445：

> [!Important]  
> 即使您決定仍不對輸出流量開放連接埠 445，Microsoft 還是建議您遵循下列步驟以從環境中移除 SMB 1.0。

1. 確定組織的裝置上已移除或停用 SMB 1.0。 目前支援的 Windows 和 Windows Server 版本全都支援移除或停用 SMB 1.0，而從 Windows 10 版本 1709 開始，Windows 上就已預設不會安裝 SMB 1.0。 若要深入了解如何停用 SMB 1.0，請參閱我們的作業系統專屬頁面：
    - [保護 Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [保護 Linux](storage-how-to-use-files-linux.md#securing-linux)
1. 確定組織中沒有任何產品需要 SMB 1.0，並移除有此需要的產品。 我們會維護 [SMB1 Product Clearinghouse](https://aka.ms/stillneedssmb1)，裡面會有 Microsoft 已知需要 SMB 1.0 的所有第一方和第三方產品。 
1. (選擇性) 對組織的內部部署網路使用第三方防火牆，以防止 SMB 1.0 流量。

如果組織需要依每一原則或規定來封鎖連接埠 445，則您可以使用 Azure VPN 閘道或 ExpressRoute，建立通道讓流量通過連接埠 443。 若要深入了解如何進行這些部署的具體步驟，請參閱具體的操作說明頁面：
- [設定站對站 (S2S) VPN 以便與 Azure 檔案儲存體搭配使用](storage-files-configure-s2s-vpn.md)
- [在 Windows 上設定點對站 (P2S) VPN 以便與 Azure 檔案儲存體搭配使用](storage-files-configure-p2s-vpn-windows.md)
- [在 Linux 上設定點對站 (P2S) VPN 以便與 Azure 檔案儲存體搭配使用](storage-files-configure-p2s-vpn-linux.md)

組織可能會有其他需求，而規定從內部部署網站輸出的流量必須遵循您雲端資源的確定路徑。 若是如此，ExpressRoute 能滿足這項需求。

## <a name="securing-access-from-cloud-resources"></a>保護來自雲端資源的存取
一般而言，當內部部署應用程式隨即轉移至雲端時，應用程式和應用程式的資料也會同時跟著移動。 這表示隨即轉移移轉的主要考慮，是將 Azure 檔案共用的存取權鎖定在需要存取檔案共用才能運作的特定虛擬機器或 Azure 服務上。 

您可以使用 VNet 來限制哪些 VM 或其他 Azure 資源可以建立網路連線 (對 Azure 檔案共用進行 SMB 掛接或 REST API呼叫)。 如果您允許未加密的流量進入儲存體帳戶，我們會建議您一律將 Azure 檔案共用放在 VNet 中。 否則，您應該根據商務需求和組織政策來決定是否要使用 VNet。

會允許未加密流量進入 Azure 檔案共用，主要原因是為了支援 Windows Server 2008 R2、Windows 7 或其他較舊的作業系統使用 SMB 2.1 (在某些 Linux 散發套件中，則是使用沒有加密功能的 SMB 3.0) 來存取 Azure 檔案共用。 如果作業系統支援有加密功能的 SMB 3.0 以上版本，則不建議您使用沒有加密功能的 SMB 2.1 或 SMB 3.0。

## <a name="see-also"></a>另請參閱
- [Azure 檔案儲存體概觀](storage-files-introduction.md)
- [規劃 Azure 檔案部署](storage-files-planning.md)