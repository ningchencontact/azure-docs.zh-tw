---
title: 從 Microsoft 針對 Azure CDN 的 HTTP 標頭進行偵錯工具 |Microsoft Docs
description: 「快取」快取要求標頭會提供已套用至所要求資產之快取原則的其他相關資訊。 這些標頭專屬於 Microsoft 的 Azure CDN 所特有。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: magattus
ms.openlocfilehash: 297c65c1cd89163b8663819f844dc6c2a83fd1bf
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814066"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>來自 Microsoft 的 Azure CDN 的 Debug HTTP 標頭
「偵錯工具回應標`X-Cache`頭」 () 提供詳細資料, 說明內容的服務來源 CDN 堆疊層。 此標頭專屬於 Microsoft 的 Azure CDN 所特有。

### <a name="response-header-format"></a>回應標頭格式

標頭 | 描述
-------|------------
X-快取:TCP_HIT | 從 CDN 邊緣快取提供內容時, 會傳回此標頭。 
X-快取:TCP_REMOTE_HIT | 從 CDN 區域快取 (原始盾牌層) 提供內容時, 會傳回此標頭
X-快取:TCP_MISS | 當快取遺漏時, 會傳回此標頭, 並從來源提供內容。 


