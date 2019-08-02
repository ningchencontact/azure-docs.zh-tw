---
title: Azure 入口網站 Url 的安全Microsoft Docs
description: 將這些 Url 新增至 proxy 伺服器略過, 以與 Azure 入口網站及其服務進行通訊
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 3747ca7504e1a8a6bbeb6237c1b3cb2e5e4afb5b
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667479"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>將防火牆或 proxy 伺服器上的 Azure 入口網站 Url

若要在您的本機或廣域網路與 Azure 雲端之間取得良好的效能和連線, 請設定內部部署安全性裝置略過 Azure 入口網站 Url 的安全性限制。 網路系統管理員通常會部署 proxy 伺服器、防火牆或其他裝置, 以協助保護及控制使用者存取網際網路的方式。 不過, 設計來保護使用者的規則有時可以封鎖或減緩合法的商務相關網際網路流量, 包括您與 Azure 之間的通訊。 若要優化您的網路與 Azure 入口網站及其服務之間的連線, 建議您將 Azure 入口網站的 Url 新增至您的安全。

## <a name="azure-portal-urls-for-proxy-bypass"></a>Azure 入口網站 proxy 略過的 Url

將下列 Url 清單新增至您的 proxy 伺服器或防火牆, 以允許這些端點的網路流量略過限制:

* *.aadcdn.microsoftonline-p.com
* *.aimon.applicationinsights.io
* *.azure.com
* *.azuredatalakestore.net
* *.azureedge.net
* *.exp.azure.com
* *.ext.azure.com
* *.gfx.ms
* *.account.microsoft.com
* *.hosting.portal.azure.net
* *.marketplaceapi.microsoft.com
* *.microsoftonline.com
* *.msauth.net
* *.msftauth.net
* *.portal.azure.com
* *.portalext.visualstudio.com
* *.sts.microsoft.com
* *.vortex.data.microsoft.com
* *.vscommerce.visualstudio.com
* *.vssps.visualstudio.com
* *.windows.net
* *.wpc.azureedge.net

> [!NOTE]
> 這些端點的流量會使用適用于 HTTP (80) 和 HTTPS (443) 的標準 TCP 埠。
>
>
## <a name="next-steps"></a>後續步驟

* 需要將 IP 位址加入至安全的名稱嗎？ 下載[Microsoft Azure DATACENTER IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)清單。
* 其他 Microsoft 服務會使用額外的 Url 和 IP 位址來進行連線。 若要將 Microsoft 365 服務的網路連線優化, 請參閱[設定您的網路以進行 Office 365](/office365/enterprise/set-up-network-for-office-365)。
