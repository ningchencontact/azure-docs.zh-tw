---
title: 使用 Azure Front 來設定 Web 應用程式防火牆的 bot 保護（預覽）
description: 學習 Web 應用程式防火牆（WAF）。
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 9a8901e3763b10b7ee00c10f4eec08a8325d7dec
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512466"
---
# <a name="configure-bot-protection-for-web-application-firewall-preview"></a>設定 Web 應用程式防火牆的 bot 保護（預覽）
本文說明如何使用 Azure CLI、Azure PowerShell 或 Azure Resource Manager 範本，在 Azure Web 應用程式防火牆（WAF）中設定 bot 保護規則。

您可以為您的 WAF 啟用受管理的 Bot 保護規則集，以對來自已知惡意 IP 位址的要求採取自訂動作。 IP 位址源自 Microsoft 威脅情報摘要。 [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence)支援 Microsoft 威脅情報，並由多項服務所使用，包括 Azure 資訊安全中心。

> [!IMPORTANT]
> Bot 保護規則集目前處於公開預覽狀態，且提供預覽服務等級協定。 可能不支援特定功能，或可能已經限制功能。  如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必要條件

遵循[使用 Azure 入口網站建立 Azure front 的 WAF 原則](waf-front-door-create-portal.md)中所述的指示，建立 Front 的基本 WAF 原則。

## <a name="enable-bot-protection-rule-set"></a>啟用 bot 保護規則集

1. 在您于上一節建立的 [基本原則] 頁面中，按一下 [**設定**] 底下的 [**規則**]。
2. 在 [詳細資料] 頁面的 [**管理規則**] 區段下，從下拉式功能表中選取 [規則**BotProtection-preview-0.1**] 前面的核取方塊，然後選取 [**儲存**上述]。
    
   ![Bot 保護規則](.././media/waf-front-door-configure-bot-protection/botprotect2.png)

## <a name="next-steps"></a>後續步驟

- 瞭解如何[監視 WAF](waf-front-door-monitor.md)。
