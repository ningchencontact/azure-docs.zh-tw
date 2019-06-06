---
title: 設定 web 應用程式防火牆的 bot 保護與 Azure 前端 （預覽）
description: 了解 web 應用程式防火牆 (WAF)。
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: tyao;kumud
ms.openlocfilehash: af92f3b9049862fc19c69965f979b7dfe8c62526
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481620"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>設定 web 應用程式防火牆 （預覽） 的 bot 保護
這篇文章會示範如何使用 Azure CLI、 Azure PowerShell 或 Azure Resource Manager 範本在 Azure web 應用程式防火牆 (WAF) 中設定 bot 保護規則的大門。

WAF 擔任來自已知惡意 IP 位址的要求中的自訂動作時，可以啟用受管理的 Bot 保護規則集。 IP 位址被來自 Microsoft 威脅情報摘要。 [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence)提供 Microsoft 威脅情報，並由多個服務，包括 Azure 資訊安全中心。

> [!IMPORTANT]
> Bot 保護規則集目前處於公開預覽狀態，並提供預覽服務等級協定。 可能不支援特定功能，或可能已經限制功能。  如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必要條件

建立基本的 WAF 原則中所述的指示大門[使用 Azure 入口網站建立 WAF 原則的 Azure 大門](waf-front-door-create-portal.md)。

## <a name="enable-bot-protection-rule-set"></a>啟用 bot 保護規則集

1. 您在上一節中，建立基本的原則頁面**設定**，按一下**規則**。
2. 在 詳細資料 頁面中下,**管理規則**區段中，從下拉式選單中，選取核取方塊，此規則前面**BotProtection-preview-0.1**，然後選取 **儲存**上方。
    
   ![Bot 保護規則](./media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>後續步驟

- 了解如何[監視 WAF](waf-front-door-monitor.md)。
