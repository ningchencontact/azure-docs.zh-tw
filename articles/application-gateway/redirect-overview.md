---
title: Azure 應用程式閘道的重新導向概觀
description: 了解 Azure 應用程式閘道的重新導向功能
services: application-gateway
documentationcenter: na
author: amsriva
manager: jpconnock
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 03/19/2018
ms.date: 04/17/2019
ms.author: v-junlch
ms.openlocfilehash: 8e88e0e11b3ccab7cc2c68b2617df2d588680780
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60715790"
---
# <a name="application-gateway-redirect-overview"></a>應用程式閘道重新導向概觀

可以使用应用程序网关来重定向流量。  它有一种泛型重定向机制，用于将一个侦听器上接收到的流量重定向到另一个侦听器或外部站点。 這可簡化應用程式組態、將資源使用量最佳化，並支援新的重新導向案例，包括全域和路徑式重新導向。

为确保应用程序及其用户之间的所有通信都通过加密路径进行，适用于许多 Web 应用的常见重定向方案是支持 HTTP 到 HTTPS 自动重定向。 在過去，客戶會使用一些技巧 (例如建立專屬後端集區)，其唯一目的是要將其在 HTTP 上接收的要求重新導向至 HTTPS。 由于应用程序网关提供重定向支持，因此你可以很容易地完成此操作，只需向路由规则添加一个新的重定向配置，然后将使用 HTTPS 协议的另一个侦听器指定为目标侦听器即可。

支持以下类型的重定向：

- 301 永久性重定向
- 302 已找到
- 303 参见其他
- 307 临时重定向

應用程式閘道重新導向提供下列功能：

-  **全域重新導向**

   從閘道上的一個接聽程式到另一個接聽程式的重新導向。 這允許在網站上進行 HTTP 至 HTTPS 重新導向。
- **路徑式重新導向**

   這類型的重新導向只允許在特定網站區域上進行 HTTP 至 HTTPS 重新導向，例如以 /cart/* 表示的購物車區域。
- **重新導向至外部網站**

![重新導向](./media/redirect-overview/redirect.png)

有了這項變更，客戶必須建立新的重新導向組態物件，以指定重新導向所需的目標接聽程式或外部網站。 此組態元素也支援能將 URI 路徑和查詢字串附加至重新導向之 URL 的選項。 也可选择重定向的类型。 一旦建立，此重新導向組態會透過新規則附加至來源接聽程式。 若使用時基本規則，重新導向組態會與來源接聽程式相關聯並為全域重新導向。 使用路徑式規則時，就會在 URL 路徑對應上定義重新導向組態。 因此它只適用於網站的特定路徑區域。

### <a name="next-steps"></a>後續步驟

[在應用程式閘道上設定 URL 重新導向](tutorial-url-redirect-powershell.md)

<!-- Update_Description: wording update -->