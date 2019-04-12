---
title: Microsoft web 應用程式防火牆資料連線至 Azure 的 Sentinel Preview |Microsoft Docs
description: 了解如何將 Microsoft web 應用程式防火牆資料連接至 Azure 的 Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 5316fa7e3aa4465349b762b99bec9171f821062f
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/11/2019
ms.locfileid: "59491025"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>將資料連接，從 Microsoft web 應用程式防火牆

> [!IMPORTANT]
> Azure Sentinel 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您可以串流處理來自 Azure 應用程式閘道的 Microsoft web 應用程式防火牆 (WAF) 的記錄。 此 WAF 保護您的應用程式免於常見 web 弱點，例如 SQL 插入式攻擊和跨網站指令碼，並可讓您自訂規則，以減少誤判。 請遵循下列指示，在您的 Microsoft Web 應用程式防火牆記錄檔串流至 Azure 的 Sentinel。


## <a name="prerequisites"></a>必要條件

- 現有的應用程式閘道資源

## <a name="connect-to-microsoft-web-application-firewall"></a>連接到 Microsoft 的 web 應用程式防火牆

如果您已經有 Microsoft web 應用程式防火牆，請確定您有現有的閘道資源。
一旦部署您的 Microsoft web 應用程式防火牆，並取得資料，請參閱警示的資料可以輕鬆地串流到 Azure 的 Sentinel。
    
1. 在 Azure Sentinel 入口網站中，選取**資料連接器**。
1. 在 [資料連接器] 頁面中，選取**WAF**圖格。
1. 移至[應用程式閘道資源](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) 選擇 WAF。
    1. 選取  **診斷設定**。
    1. 選取  **+ 新增診斷設定**資料表下。
    1. 在  **診斷設定**頁面上，輸入 **名稱**，然後選取 **傳送至 Log Analytics**。
    1. 底下**Log Analytics 工作區**選取 [Azure Sentinel] 工作區。
    1. 選取您想要分析的記錄類型。 我們建議使用：ApplicationGatewayAccessLog 和 ApplicationGatewayFirewallLog。
1. 若要使用 Log Analytics 中 Microsoft 的 web 應用程式防火牆警示相關的結構描述，搜尋**AzureDiagnostics**。

## <a name="next-steps"></a>後續步驟
在本文件中，您已了解如何連接至 Azure 的 Sentinel 的 Microsoft web 應用程式防火牆。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 了解如何[了解您的資料，與潛在的威脅](quickstart-get-visibility.md)。
- 開始[偵測威脅與 Azure Sentinel](tutorial-detect-threats.md)。
