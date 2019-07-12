---
title: 連接到 Azure Sentinel 預覽版的 威脅情報資料 |Microsoft Docs
description: 深入了解如何連接到 Azure Sentinel 的 威脅情報資料。
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 56412543-5664-44c1-b026-2dbaf78a9a50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 266e487a7c345f75e966afbde567c5bc4683b5c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233757"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>威脅情報提供者從連線資料 

> [!IMPORTANT]
> Azure Sentinel 目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您將您的資料串流至 Azure 的 Sentinel 之後，您可以擴充它使用威脅情報摘要，您使用您的組織。 

若要可讓您跨洽詢您的警示和規則，則為 true 的威脅情報，例如，如果您收到警示來自特定 IP 位址，您的威脅情報提供者整合都能夠讓您知道是否最近找到該 IP 位址為惡意Azure 的 Sentinel 可整合[威脅情報提供者](https://aka.ms/graphsecuritytips)。 

可以從 威脅情報提供者的記錄檔串流至 Azure 的 Sentinel，只要按一下。 此連線可讓您將指標包含各種類型的可預見值，例如 IP 位址、 網域、 URL 和檔案雜湊，以搜尋，並建立自訂警示規則，在 Azure 的 Sentinel。  
> [!NOTE]
> 您可以輸入自訂的威脅指標 Azure Sentinel 警示規則、 儀表板和追捕案例中使用藉由整合[Microsoft Graph Security tiIndicator](https://aka.ms/graphsecuritytiindicators)實體或使用[Microsoft圖形安全性整合威脅智慧平台](https://aka.ms/graphsecuritytips)。

## <a name="prerequisites"></a>先決條件  

- 以全域系統管理員或安全性系統管理員權限的使用者 

- 威脅智慧應用程式與 Microsoft Intelligent Security Graph 整合 

## <a name="connect-to-threat-intelligence"></a>連接到的威脅情報 

1. 如果您已經使用威脅情報提供者，請務必瀏覽至應用程式提示，並將指標傳送給 Microsoft，並為 Azure Sentinel 指定服務的權限授與。  

2. 在 Azure Sentinel，選取**資料連接器**，然後按一下**威脅情報**圖格。

3. 按一下 **[連接]** 。 

4. 若要使用 Log Analytics 中的相關的結構描述，如威脅情報摘要，搜尋**ThreatIntelligenceIndicator**。 

 
## <a name="next-steps"></a>後續步驟

本文件中，您已了解如何在您的威脅情報提供者連線到 Azure 的 Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章。

- 若要開始使用 Azure Sentinel，您需要 Microsoft Azure 訂用帳戶。 如果您沒有訂用帳戶，可以註冊[免費試用](https://azure.microsoft.com/free/)。
- 了解如何[將資料上架到 Azure Sentinel](quickstart-onboard.md)，並[掌握您的資料和潛在威脅](quickstart-get-visibility.md)。
