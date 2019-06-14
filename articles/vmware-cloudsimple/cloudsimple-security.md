---
title: CloudSimple 服務的安全性
description: 描述 CloudSimple 服務安全性的共同的責任模型
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/27/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f5f3fe32e03a9a2bb0186854a83917f8918c6647
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "66358119"
---
# <a name="cloudsimple-security-overview"></a>CloudSimple 安全性概觀

這篇文章提供安全性 CloudSimple 服務、 基礎結構，以及資料中心上的實作方式的概觀。  您了解資料保護和安全性、 網路安全性及管理弱點和修補程式的方式。

## <a name="shared-responsibility"></a>共同責任

Azure 的 VMware 解決方案，由 CloudSimple 會使用共同的責任模型安全性。 雲端中受信任的安全性是透過客戶和 Microsoft 共同責任來達成，做為服務提供者。 這個矩陣的責任提供較高的安全性，並消除單一失敗點。 

## <a name="azure-infrastructure"></a>Azure 基礎結構 

Azure 基礎結構的安全性考量事項包括的資料中心和設備的位置。 

### <a name="datacenter-security"></a>資料中心安全性 

Microsoft 有專用於設計、 建置和操作支援 Azure 實體設備整個部門。 這個團隊致力於維護最新的實體安全性。 如需實體安全性的詳細資訊，請參閱[Azure 功能、 內部部署，以及實體設備安全性](https://docs.microsoft.com/azure/security/azure-physical-security)。

### <a name="equipment-location"></a>設備的位置

執行您的私用雲端的裸機硬體設備會裝載於 Azure 資料中心位置。  該設備的所在，cages 需要生物特徵辨識來存取基礎的雙因素驗證。

## <a name="dedicated-hardware"></a>專用的硬體

CloudSimple 服務的一部分，所有的 CloudSimple 客戶會有專用的裸機裸機主機，與本機連接的磁碟實際隔離的其他租用戶的硬體。 每個節點上，執行與 vSAN ESXi hypervisor。 節點是透過專用的客戶 VMware vCenter 和 NSX 管理。 不共用租用戶之間的硬體提供一層額外的隔離和安全性保護。

## <a name="data-security"></a>資料安全性

客戶將控制項和其資料的擁有權。 資料監管中心的客戶資料是客戶的責任。

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>待用資料和內部網路內的移動中資料的資料保護

對於私用雲端環境中的待用資料，您可以使用 vSAN 加密。 vSAN 加密搭配您自己的虛擬網路或內部部署中的 VMware 認證的外部金鑰管理伺服器 (KMS)。  您自行控制的資料加密金鑰。 對於私用雲端內的移動中的資料，vSphere 會針對所有 vmkernel 流量 （包括 vMotion 流量） 在網路上支援資料的加密。

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>資料保護，才能透過公用網路移動的資料

若要保護在公用網路移動的資料，您可以建立 IPsec 和 SSL VPN 私用雲端的通道。 支援常見的加密方法，包括 128 位元和 256 位元 AES。 使用標準的加密機制 （SSH、 TLS 1.2 和保護 RDP） 進行加密 （包括驗證、 系統管理存取權，以及客戶資料） 的傳輸中的資料。 傳輸機密資訊的通訊會使用標準的加密機制。

### <a name="secure-disposal"></a>安全處置 

如果您 CloudSimple 服務過期或終止，您必須負責移除或刪除您的資料。 CloudSimple 會配合您刪除或合約中提供客戶，但範圍內 CloudSimple 所適用的法律保留部分或所有的個人資料會傳回所有客戶資料。 如果需要保留任何個人資料，CloudSimple 就會將資料封存，並實作合理的量值，以防止任何進一步處理客戶資料。

### <a name="data-location"></a>資料位置

設定您的私用雲端，當您選擇部署所在的 Azure 區域。 不，從該實體的資料中心來移動 VMware 虛擬機器資料，除非您執行資料移轉或異地資料的備份。 您也可以裝載的工作負載，並儲存在多個 Azure 區域內的資料，如果適用於您的需求。

私用雲端超融合節點中的客戶資料不會周遊不需要租用戶系統管理員明確動作的位置。 您必須負責實作您的工作負載高可用性的方式。

### <a name="data-backups"></a>資料備份
CloudSimple 不備份或封存的客戶資料。 CloudSimple 的確會執行定期備份 vCenter 和 NSX 資料，以提供高可用性管理伺服器。 再進行備份，所有資料會都加密在 vCenter 來源使用 VMware Api。 加密的資料會傳輸，並儲存在 Azure blob。 備份加密金鑰會儲存在高度安全的 CloudSimple 受管理保存庫，並在 Azure 中的 CloudSimple 虛擬網路中執行。

## <a name="network-security"></a>網路安全性

CloudSimple 方案相依於層級的網路安全性。

### <a name="azure-edge-security"></a>Azure 的邊緣安全性

CloudSimple 服務是建置 Azure 所提供的基底的網路安全性。 Azure 適用於深度防禦技巧來偵測異常的輸入或輸出流量模式與分散式的阻斷服務 (DDoS) 攻擊相關聯的網路型攻擊的及時回應。 此安全性控制套用至私用雲端環境和開發的 CloudSimple 控制平面軟體。

### <a name="segmentation"></a>分割

CloudSimple 服務已經以邏輯方式分開限制存取您自己的私用網路私用雲端環境中的層級 2 網路。 您可以進一步保護您使用防火牆的私用雲端網路。 CloudSimple 入口網站可讓您定義的新功能和 NS 網路流量控制規則的所有網路流量，包括內部私用雲端流量、 間私用雲端流量、 一般的資料傳輸到網際網路，以及網路流量傳送到內部部署透過 IPsec VPN 或ExpressRoute 連線。

## <a name="vulnerability-and-patch-management"></a>弱點和修補程式管理 

CloudSimple 負責定期的安全性修補的受管理的 VMware 軟體 （ESXi、 vCenter 和 NSX）。

## <a name="identity-and-access-management"></a>Identity and Access Management

客戶可以驗證他們的 Azure 帳戶 （在 Azure AD) 使用多重要素驗證或 SSO 做為慣用。 從 Azure 入口網站中，您可以啟動 CloudSimple 入口網站，無需重新輸入認證。

CloudSimple 支援私用雲端 vCenter 的身分識別來源的選擇性設定。 您可以使用[內部部署身分識別來源](https://docs.azure.cloudsimple.com/set-vcenter-identity)，新的身分識別來源為私用雲端，或[Azure AD](https://docs.azure.cloudsimple.com/azure-ad)。

根據預設，客戶可以在私用雲端內的 vCenter 的日常作業所需的權限。 此權限層級不包含到 vCenter 的系統管理存取權。 如果暫時需要系統管理存取權，您可以[提升您的權限](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges)在有限期間內完成的系統管理工作。

## <a name="next-steps"></a>後續步驟

* 了解如何[在 Azure 上建立 CloudSimple 服務](quickstart-create-cloudsimple-service.md)
* 了解如何[建立私人雲端](https://docs.azure.cloudsimple.com/create-private-cloud/)
* 了解如何[設定私用雲端環境](quickstart-create-private-cloud.md)
