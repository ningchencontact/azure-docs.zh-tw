---
title: 擷取 Azure CDN 目前的 Verizon POP 清單 | Microsoft Docs
description: 了解如何使用 REST API 擷取目前的 Verizon POP 清單。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: c8316b994dac6b859f019bea1aac6b6a5c2c5b2d
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593569"
---
# <a name="retrieve-the-current-verizon-pop-list-for-azure-cdn"></a>擷取 Azure CDN 目前的 Verizon POP 清單

您可以使用 REST API 來為 Verizon 的存在點 (PoP) 伺服器擷取一組 IP。 這些 POP 伺服器會將要求發給與 Verizon 設定檔 (**來自 Verizon 的 Azure CDN 標準**或**來自 Verizon 的 Azure CDN 進階**) 上的 Azure 內容傳遞網路 (CDN) 端點相關聯的原始伺服器。 請注意，這組 IP 與用戶端在將要求發給 POP 時所會看到的 IP 不同。 

如需用於擷取 POP 清單的 REST API 作業語法，請參閱[邊緣節點 - 清單](https://docs.microsoft.com/rest/api/cdn/edgenodes/list)。

## <a name="typical-use-case"></a>典型的使用案例

為確保安全，您可以使用此 IP 清單，強制規定只有有效的 Verizon POP 才能將要求發給原始伺服器。 例如，如果有使用者探索到 CDN 端點原始伺服器的主機名稱或 IP 位址，該使用者就可以直接將要求發給原始伺服器，因而繞過 Azure CDN 所提供的調整和安全性功能。 只要將所傳回清單中的 IP 設定為原始伺服器上唯一允許的 IP，就能避免此情形。 為了確保您擁有最新的 POP 清單，請至少每天擷取一次該清單。 

## <a name="next-steps"></a>後續步驟

如需 REST API 的相關資訊，請參閱 [Azure CDN REST API](https://docs.microsoft.com/rest/api/cdn/)。
