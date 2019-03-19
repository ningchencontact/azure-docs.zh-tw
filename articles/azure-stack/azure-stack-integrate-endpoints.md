---
title: Azure Stack 資料中心整合 - 發佈端點 | Microsoft Docs
description: 了解如何在您的資料中心內發佈 Azure Stack 端點
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/06/2019
ms.author: jeffgilb
ms.reviewer: wamota
ms.lastreviewed: 02/06/2019
ms.openlocfilehash: 016ec9c1d215cc8fe04d0c7cedebe904221c6557
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121633"
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure Stack 資料中心整合 - 發佈端點

Azure Stack 會為其基礎結構角色設定虛擬 IP 位址 (VIP)。 這些 VIP 是從公用 IP 位址集區配置的。 針對每個 VIP，都會藉由軟體定義網路層中的存取控制清單 (ACL) 來提供保護。 ACL 也用於各個實體交換器 (TOR 和 BMC) 來進一步強化解決方案。 系統會針對在部署階段所指定外部 DNS 區域中的每個端點，都建立一個 DNS 項目。


以下架構圖顯示各種不同的網路層和 ACL：

![結構化圖片](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>連接埠和通訊協定 (輸入)

將 Azure Stack 端點發佈到外部網路時需有一組基礎結構 VIP。 「端點 (VIP)」資料表會顯示每個端點、所需的連接埠以及通訊協定。 請參閱特定資源提供者部署文件，了解需要其他資源提供者 (例如 SQL 資源提供者等) 的端點。

此處並未列出內部基礎結構 VIP，因為它們並非發佈 Azure Stack 所需的 VIP。

> [!Note]  
> 使用者 VIP 是動態的，由使用者自己定義，並不受 Azure Stack 操作員控制。

> [!Note]
> 在 1811 更新之後，由於新增了[延伸模組主機](azure-stack-extension-host-prepare.md)，不需要再開啟 12495-30015 範圍中的連接埠了。

|端點 (VIP)|DNS 主機 A 記錄|通訊協定|連接埠|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|入口網站 (系統管理員)|Adminportal.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Adminhosting | *.adminhosting.\<region>.\<fqdn> | HTTPS | 443 |
|Azure Resource Manager (系統管理員)|Adminmanagement.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|入口網站 (使用者)|Portal.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Azure Resource Manager (使用者)|Management.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|圖形|Graph.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|憑證撤銷清單|Crl.*&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;.*&lt;region>.&lt;fqdn>*|TCP 和 UDP|53|
|裝載 | *.hosting.\<region>.\<fqdn> | HTTPS | 443 |
|Key Vault (使用者)|&#42;.vault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Key Vault (系統管理員)|&#42;.adminvault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|儲存體佇列|&#42;.queue.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|儲存體資料表|&#42;.table.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|儲存體 Blob|&#42;.blob.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|SQL 資源提供者|sqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|MySQL 資源提供者|mysqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|App Service 方案|&#42;.appservice.*&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300 (Azure Resource Manager)|
|  |ftp.appservice.*&lt;region>.&lt;fqdn>*|TCP、UDP|21, 1021, 10001-10100 (FTP)<br>990 (FTPS)|
|VPN 閘道|     |     |[請參閱 VPN 閘道常見問題集](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability)。|
|     |     |     |     |

## <a name="ports-and-urls-outbound"></a>連接埠和 URL (輸出)

Azure Stack 僅支援 Transparent Proxy 伺服器。 在 Transparent Proxy 上行連結至傳統 Proxy 伺服器的部署中，您必須允許下列連接埠和 URL 才能進行連出通訊：

> [!Note]  
> Azure Stack 不支援使用 ExpressRoute 來觸達下表中列出的 Azure 服務。

|目的|目的地 URL|通訊協定|連接埠|來源網路|
|---------|---------|---------|---------|---------|
|身分識別|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br><https://secure.aadcdn.microsoftonline-p.com><br>office.com|HTTP<br>HTTPS|80<br>443|公用 VIP - /27<br>公用基礎結構網路|
|Marketplace 摘要整合|<https://management.azure.com><br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|公用 VIP - /27|
|修補程式和更新|https://&#42;.azureedge.net|HTTPS|443|公用 VIP - /27|
|註冊|<https://management.azure.com>|HTTPS|443|公用 VIP - /27|
|使用量|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.net |HTTPS|443|公用 VIP - /27|
|Windows Defender|.wdcp.microsoft.com<br>.wdcpalt.microsoft.com<br>*.updates.microsoft.com<br>*.download.microsoft.com<br><https://msdl.microsoft.com/download/symbols><br>`https://www.microsoft.com/pkiops/crl`<br>`https://www.microsoft.com/pkiops/certs`<br>`https://crl.microsoft.com/pki/crl/products`<br>`https://www.microsoft.com/pki/certs`<br><https://secure.aadcdn.microsoftonline-p.com><br>|HTTPS|80<br>443|公用 VIP - /27<br>公用基礎結構網路|
|NTP|(可供部署的 NTP 伺服器 IP)|UDP|123|公用 VIP - /27|
|DNS|(可供部署的 DNS 伺服器 IP)|TCP<br>UDP|53|公用 VIP - /27|
|CRL|(您的憑證上 CRL 發佈點之下的 URL)|HTTP|80|公用 VIP - /27|
|LDAP|針對 Graph 整合提供的 Active Directory 樹系|TCP<br>UDP|389|公用 VIP - /27|
|LDAP SSL|針對 Graph 整合提供的 Active Directory 樹系|TCP|636|公用 VIP - /27|
|LDAP GC|針對 Graph 整合提供的 Active Directory 樹系|TCP|3268|公用 VIP - /27|
|LDAP GC SSL|針對 Graph 整合提供的 Active Directory 樹系|TCP|3269|公用 VIP - /27|
|AD FS|針對 AD FS 整合提供的 AD FS 中繼資料端點|TCP|443|公用 VIP - /27|
|     |     |     |     |     |

> [!Note]  
> 輸出 URL 會使用 Azure 流量管理員進行負載平衡，以根據地理位置提供可能的最佳連線能力。 利用負載平衡的 URL，Microsoft 可以更新和變更後端端點，但不會影響客戶。 Microsoft 不會共用已負載平衡 URL 的 IP 位址清單。 您應該使用可支援依照 URL (而非依照 IP) 篩選的裝置。

## <a name="next-steps"></a>後續步驟

[Azure Stack PKI 需求](azure-stack-pki-certs.md)
