---
title: 將公司網際網路網域指向 Azure 流量管理員網域名稱
description: 本文將協助您將公司網域名稱指向流量管理員網域名稱。
services: traffic-manager
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
ms.openlocfilehash: 77a5fbab6ecda910750ab2b8bae987e77607223a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "57903485"
---
# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>將公司網際網路網域指向 Azure 流量管理員網域

當您建立流量管理員設定檔時，Azure 會自動為該設定檔指派 DNS 名稱。 若要使用來自 DNS 區域的名稱，建立對應至流量管理員設定檔網域名稱的 CNAME DNS 記錄。 您可以在流量管理員設定檔的 [組態] 頁面上的 [一般]  區段找到流量管理員網域名稱。

例如，若要將名稱 `www.contoso.com` 指向流量管理員 DNS 名稱 `contoso.trafficmanager.net`，您要建立下列 DNS 資源記錄：

    www.contoso.com IN CNAME contoso.trafficmanager.net

所有流量都要求*www\.contoso.com*導向*contoso.trafficmanager.net*。

> [!IMPORTANT]
> 您無法將第二層網域 (例如 *contoso.com*) 指向流量管理員網域。 DNS 通訊協定標準不允許第二層網域名稱的 CNAME 記錄。

## <a name="next-steps"></a>後續步驟

* [流量管理員路由方法](traffic-manager-routing-methods.md)
* [流量管理器 - 禁用、启用或删除配置文件](disable-enable-or-delete-a-profile.md)
* [流量管理員 - 停用或啟用端點](disable-or-enable-an-endpoint.md)
