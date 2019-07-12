---
title: CloudSimple 服務的安全性
description: 描述 CloudSimple 服務安全性的共同的責任模型
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/27/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 325915aae43c905236910382f650730a6daa127a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595332"
---
# <a name="cloudsimple-security-overview"></a>CloudSimple 安全性概觀

這篇文章提供安全性實作 on Azure VMware 解決方案 CloudSimple 服務、 基礎結構及資料中心所方式的概觀。 您了解資料保護和安全性、 網路安全性及管理弱點和修補程式的方式。

## <a name="shared-responsibility"></a>共同責任

Azure 的 VMware 解決方案，由 CloudSimple 會使用共同的責任模型安全性。 雲端中受信任的安全性是透過客戶和 Microsoft 共同責任來達成，做為服務提供者。 這個矩陣的責任提供較高的安全性，並消除單一失敗點。

## <a name="azure-infrastructure"></a>Azure 基礎結構 

Azure 基礎結構的安全性考量事項包括的資料中心和設備的位置。

### <a name="datacenter-security"></a>資料中心安全性 

Microsoft 有專用於設計、 建置和操作支援 Azure 實體設備整個部門。 這個團隊致力於維護最新的實體安全性。 如需有關實體安全性的詳細資訊，請參閱 < [Azure 功能、 內部部署，以及實體設備安全性](https://docs.microsoft.com/azure/security/azure-physical-security)。

### <a name="equipment-location"></a>設備的位置

執行您的私人雲端的裸機硬體設備會裝載於 Azure 資料中心位置。 生物特徵辨識為基礎的雙因素驗證，才能存取該設備的所在位置的機殼。

## <a name="dedicated-hardware"></a>專用的硬體

CloudSimple 服務的一部分，所有的 CloudSimple 客戶會有專用的裸機主機，與本機連接的磁碟實際隔離的其他租用戶的硬體。 每個節點上，執行與 vSAN ESXi hypervisor。 節點是透過客戶專屬 VMware vCenter 中，且 NSX 管理。 不共用租用戶之間的硬體提供一層額外的隔離和安全性保護。

## <a name="data-security"></a>資料安全性

客戶將控制項和其資料的擁有權。 資料監管中心的客戶資料是客戶的責任。

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>待用資料和內部網路內的移動中資料的資料保護

對於私用雲端環境中的待用資料，您可以使用 vSAN 加密。 vSAN 加密搭配您自己的虛擬網路或內部部署中的 VMware 認證的外部金鑰管理伺服器 (KMS)。 您自行控制的資料加密金鑰。 針對私人雲端內的移動中的資料，vSphere 會針對所有的 VMkernel 流量，包括 vMotion 流量在網路上支援資料的加密。

### <a name="data-protection-for-data-thats-required-to-move-through-public-networks"></a>如需透過公用網路移動的資料的資料保護

若要保護在公用網路移動的資料，您可以建立 IPsec 和 SSL VPN 通道私人雲端。 支援常見的加密方法，包括 128 位元和 256 位元 AES。 傳輸中的資料，包括驗證、 系統管理存取權，以及客戶資料，是以標準的加密機制，例如 SSH、 TLS 1.2 和安全的 RDP 加密。 傳輸機密資訊的通訊會使用標準的加密機制。

### <a name="secure-disposal"></a>安全處置 

如果您 CloudSimple 服務過期或終止，您負責移除或刪除您的資料。 CloudSimple 會合作與您要刪除或合約中提供客戶，但範圍內 CloudSimple 所適用的法律保留部分或所有的個人資料會傳回所有客戶資料。 如果需要保留任何個人資料，CloudSimple 封存資料，並實作合理的量值，以防止任何進一步處理客戶資料。

### <a name="data-location"></a>資料位置

當您設定私人雲端時，您會選擇部署所在的 Azure 區域。 除非您執行資料移轉或異地資料的備份，不會從該實體的資料中心移 VMware 虛擬機器資料。 您也可以裝載的工作負載，並儲存在多個 Azure 區域內的資料，如果適用於您的需求。

私用雲端超融合節點中的客戶資料不會周遊不需要租用戶系統管理員明確動作的位置。 您必須負責實作您的工作負載高可用性的方式。

### <a name="data-backups"></a>資料備份
CloudSimple 不備份或封存的客戶資料。 CloudSimple 的確會執行定期備份 vCenter 和 NSX 資料，以提供高可用性管理伺服器。 再進行備份，所有資料會加密在 vCenter 來源使用 VMware Api。 加密的資料會傳輸，並儲存在 Azure blob。 備份加密金鑰會儲存在高度安全 CloudSimple 受管理保存庫在 Azure 中的 CloudSimple 虛擬網路中執行。

## <a name="network-security"></a>網路安全性

CloudSimple 方案相依於層級的網路安全性。

### <a name="azure-edge-security"></a>Azure 的邊緣安全性

CloudSimple 服務是建置 Azure 所提供的基底的網路安全性。 Azure 適用於深度防禦技巧來偵測異常的輸入或輸出流量模式與分散式的阻斷服務 (DDoS) 攻擊相關聯的網路型攻擊的及時回應。 此安全性控制項適用於私人雲端環境和開發的 CloudSimple 控制平面軟體。

### <a name="segmentation"></a>分割

CloudSimple 服務已經以邏輯方式分開限制存取您自己的私用網路私用雲端環境中的層級 2 網路。 您可以使用防火牆，進一步保護您的私用雲端網路。 在 CloudSimple 入口網站中，您定義所有網路流量，其中包含內部私用雲端流量、 間私用雲端流量、 一般流量至網際網路，以及在內部部署環境的網路流量的東-西和北-南網路流量控制的規則透過 IPsec VPN 或 Azure ExpressRoute 連線。

## <a name="vulnerability-and-patch-management"></a>弱點可能會與修補程式管理 

CloudSimple 負責定期的安全性修補的受管理的 VMware 軟體，ESXi、 vCenter 和 NSX 等。

## <a name="identity-and-access-management"></a>身分識別和存取管理

客戶可以使用做為慣用的多重要素驗證或 SSO 驗證他們的 Azure 帳戶 （在 Azure Active Directory)。 從 Azure 入口網站中，您可以啟動 CloudSimple 入口網站，無需重新輸入認證。

CloudSimple 支援私用雲端 vCenter 的身分識別來源的選擇性設定。 您可以使用[內部部署身分識別來源](https://docs.azure.cloudsimple.com/set-vcenter-identity)，新的身分識別來源的私人雲端，或[Azure Active Directory](https://docs.azure.cloudsimple.com/azure-ad)。

根據預設，客戶會提供私人雲端內的 vCenter 的日常作業所需的權限。 此權限層級不包含到 vCenter 的系統管理存取權。 如果暫時需要系統管理存取權，您可以[提升您的權限](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges)在有限期間內完成的系統管理工作。

## <a name="next-steps"></a>後續步驟

* 了解如何[在 Azure 上建立 CloudSimple 服務](quickstart-create-cloudsimple-service.md)。
* 了解如何[建立私人雲端](https://docs.azure.cloudsimple.com/create-private-cloud/)。
* 了解如何[設定私用雲端環境](quickstart-create-private-cloud.md)。
