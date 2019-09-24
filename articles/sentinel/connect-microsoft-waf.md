---
title: 將 Microsoft web 應用程式防火牆資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Microsoft web 應用程式防火牆資料連線到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 9d85b5a72c2e37719348d61250d167eb9a5688a1
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240031"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>從 Microsoft web 應用程式防火牆連接資料



您可以從 Azure 應用程式閘道的 Microsoft web 應用程式防火牆（WAF）串流記錄。 這 WAF 可保護您的應用程式免于遭受常見的 web 弱點，例如 SQL 插入式攻擊和跨網站腳本，並可讓您自訂規則以減少誤報。 遵循這些指示，將您的 Microsoft Web 應用程式防火牆記錄串流至 Azure Sentinel。


## <a name="prerequisites"></a>必要條件

- 現有的應用程式閘道資源

## <a name="connect-to-microsoft-web-application-firewall"></a>連接到 Microsoft web 應用程式防火牆

如果您已經有 Microsoft web 應用程式防火牆，請確定您有現有的閘道資源。
一旦部署 Microsoft web 應用程式防火牆並取得資料之後，就可以輕鬆地將警示資料串流至 Azure Sentinel。
    
1. 在 Azure Sentinel 入口網站中，選取 [**資料連線器**]。
1. 在 [資料連線器] 頁面中，選取 [ **WAF** ] 圖格。
1. 移至 [[應用程式閘道資源](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) ]，然後選擇您的 WAF。
    1. 選取 [ **診斷設定**]。
    1. 選取資料表底下的 [ **+ 新增診斷設定**]。
    1. 在 [ **診斷設定**] 頁面中，輸入 **名稱**，然後選取 [ **傳送至 Log Analytics**]。
    1. 在 [ **Log Analytics 工作區**] 底下，選取 [Azure Sentinel] 工作區。
    1. 選取您想要分析的記錄類型。 我們建議：ApplicationGatewayAccessLog 和 ApplicationGatewayFirewallLog。
1. 若要在 Log Analytics 中使用適用于 Microsoft web 應用程式防火牆警示的相關架構，請搜尋**AzureDiagnostics**。

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Microsoft web 應用程式防火牆連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 瞭解如何[查看您的資料和潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
