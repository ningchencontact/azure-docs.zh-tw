---
title: 適用於 Azure Stack 整合式系統的 Azure Stack 防火牆規劃 | Microsoft Docs
description: 說明多節點 Azure Stack Azure 連線部署的 Azure Stack 防火牆考量。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: wfayed
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 023201d221ee5d7ec884c6a760407e8da8340d3f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2019
ms.locfileid: "56207109"
---
# <a name="azure-stack-firewall-integration"></a>Azure Stack 防火牆整合
建議您使用防火牆裝置來協助保護 Azure Stack 安全。 防火牆可協助抵禦分散式阻斷服務 (DDOS) 攻擊、入侵偵測及內容檢查等作業。 不過，它們也會成為 Azure 儲存體服務 (例如 Blob、資料表和佇列) 的輸送量瓶頸。

 如果使用中斷連線的部署模式，您就必須發佈 AD FS 端點。 如需詳細資訊，請參閱[資料中心整合身分識別](azure-stack-integrate-identity.md)一文。

Azure Resource Manager (系統管理員)、系統管理員入口網站及 Key Vault (系統管理員) 端點並不一定需要外部發佈。 例如，作為服務提供者，您可能只從網路內部 (而不從網際網路) 管理 Azure Stack，藉以限制攻擊面。

就企業組織而言，外部網路可能是現有的公司網路。 在此案例中，您必須發佈端點，以從公司網路操作 Azure Stack。

### <a name="network-address-translation"></a>網路位址轉譯
若要在部署期間允許部署虛擬機器 (DVM) 存取外部資源和網際網路，以及在註冊和移難排解期間允許緊急復原主控台 (ERCS) VM 或具有特殊權限的端點 (PEP) 存取外部資源和網際網路，建議使用網路位址轉譯 (NAT)。

NAT 也可以是外部網路上公用 IP 位址或公用 VIP 的替代方案。 不過，並不建議這麼做，因為它會限制租用戶使用者體驗並增加複雜性。 其中一個選項是一對一的 NAT，其仍然需要在集區上為每個使用者 IP 提供一個公用 IP。 另一個選項是多對一的 NAT，其需要針對使用者可能使用的所有連接埠，為每個使用者 VIP 提供一個 NAT 規則。

將 NAT 用於公用 VIP 的一些缺點包括：
- NAT 會增加管理防火牆規則時額外負荷，因為使用者會在軟體定義網路 (SDN) 堆疊中控制自己的端點和自己的發佈規則。 使用者必須連絡 Azure Stack 操作員，才能發佈其 VIP 及更新連接埠清單。
- 雖然使用 NAT 會限制使用者體驗，但它可讓操作員完全控制發佈要求。
- 針對與 Azure 搭配的混合式雲端案例，請考量 Azure 不支援使用 NAT 來設定端點 VPN 通道。

### <a name="ssl-decryption"></a>SSL 解密
目前建議在所有 Azure Stack 流量上停用 SSL 解密。 如果未來更新支援該功能，指引中將會提供如何為 Azure Stack 啟用 SSL 解密的相關資訊。

## <a name="edge-firewall-scenario"></a>邊緣防火牆案例
在邊緣部署中，Azure Stack 會直接部署在邊緣路由器或防火牆後面。 在這些情況下，它支援防火牆在邊界之上 (案例 1)，此時它支援「主動-主動」和「被動-主動」防火牆設定，或者當作邊界裝置 (案例 2)，此時它僅支援「主動-主動」防火牆設定，且依賴等價多重路徑 (ECMP) 與 BGP 或靜態路由進行容錯移轉。

在部署期間，會從外部網路為公用 VIP 集區指定可路由傳送的公用 IP 位址。 在邊緣案例中，不建議在任何其他網路上，針對安全性目的使用公用可路由傳送 IP 位址。 此案例會讓使用者能夠享有像在 Azure 這類公用雲端一樣的完全自我控制雲端體驗。  

![Azure Stack 邊緣防火牆範例](./media/azure-stack-firewall/firewallScenarios.png)

## <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>企業內部網路或周邊網路防火牆案例
在企業內部網路或周邊部署中，Azure Stack 會部署在多區域防火牆上，或部署在邊緣防火牆與內部公司網路防火牆中間。 然後其流量會分散在安全的周邊網路 (或 DMZ) 與不安全的區域之間，如下所述：

- **安全區域**：這是使用內部或公司可路由傳送 IP 位址的內部網路。 安全網路是可分割的網路、可透過 NAT 在防火牆上享有網際網路輸出存取權，而且通常透過內部網路，即可從您資料中心內的任何位置存取安全網路。 除了外部網路的公用 VIP 集區之外，所有 Azure Stack 網路都應該位於安全區域中。
- **周邊區域**。 周邊網路是通常用來部署外部或網際網路對應應用程式 (例如 Web 伺服器) 的位置。 它通常會受防火牆監視來避免遭受攻擊 (例如 DDoS) 和入侵 (駭客)，但仍允許來自網際網路的指定輸入流量。 只有 Azure Stack 的外部網路公用 VIP 集區應該位於 DMZ 區域。
- **不安全區域**。 這是外部網路，即網際網路。 **不**建議將 Azure Stack 部署在不安全區域中。

![Azure Stack 周邊網路範例](./media/azure-stack-firewall/perimeter-network-scenario.png)

## <a name="learn-more"></a>深入了解
深入了解 [Azure Stack 端點所使用的連接埠和通訊協定](azure-stack-integrate-endpoints.md)。

## <a name="next-steps"></a>後續步驟
[Azure Stack PKI 需求](azure-stack-pki-certs.md)

