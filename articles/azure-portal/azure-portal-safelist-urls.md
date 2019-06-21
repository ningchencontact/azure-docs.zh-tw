---
title: Azure 入口網站的安全清單 Url |Microsoft Docs
description: 將這些 Url 新增至與 Azure 入口網站和服務進行通訊的 proxy 伺服器略過
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 06/13/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 87ec600a2f6c4a560ec7cbb064b561fa76e2b615
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305005"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Azure 入口網站的安全清單的防火牆或 proxy 伺服器上的 Url

良好的效能和您的本機或整個區域網路與 Azure 的雲端之間的連線，設定內部部署安全性裝置略過安全性限制，在 Azure 入口網站 Url。 網路系統管理員通常會部署 proxy 伺服器、 防火牆或其他裝置，協助保護和讓控制使用者如何存取網際網路。 不過，設計用來保護使用者的規則有時候可以封鎖或合法商務相關的網際網路流量，包括您與 Azure 之間的通訊變慢。 若要最佳化您的網路與 Azure 入口網站和服務之間的連線能力，我們建議您加入 Azure 入口網站您的安全清單的 Url。

## <a name="azure-portal-urls-for-proxy-bypass"></a>Azure 入口網站 Url 的 proxy 略過

下列清單的 Url 新增到您的 proxy 伺服器或防火牆以允許至這些端點，以略過限制的網路流量：

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
* *.wpc.azureedge.net

> [!NOTE]
> 這些端點的流量會使用 HTTP (80) 和 HTTPS (443) 標準的 TCP 連接埠。
>
>
## <a name="next-steps"></a>後續步驟

* 需要安全清單的 IP 位址嗎？ 下載的完整清單[Microsoft Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。
* 其他 Microsoft 服務中使用其他的 Url 和 IP 位址的連線。 若要最佳化 Microsoft 365 服務的網路連線，請參閱[設定網路，適用於 Office 365](/office365/enterprise/set-up-network-for-office-365)。
