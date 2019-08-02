---
title: Azure 資訊安全中心搜尋 | Microsoft Docs
description: 瞭解 Azure 資訊安全中心如何使用 Azure 監視器記錄搜尋來取出和分析您的安全性資料。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: rkarlin
ms.openlocfilehash: c88e2025444aa2fd83e05fdff97ea640ceefaa04
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662918"
---
# <a name="azure-security-center-search-retired"></a>Azure 資訊安全中心搜尋 (已淘汰)

> [!NOTE]
> 資訊安全中心的搜尋儀表板已于2019年7月31日淘汰。 如需詳細資訊和替代服務, 請參閱[淘汰資訊安全中心功能 (2019 年7月)](security-center-features-retirement-july2019.md#menu_search)。

Azure 資訊安全中心會使用[Azure 監視器記錄搜尋](../log-analytics/log-analytics-log-searches.md)來抓取和分析您的安全性資料。 Azure 監視器記錄檔包含查詢語言, 可快速取得和合併資料。 從資訊安全中心, 您可以利用 Azure 監視器記錄搜尋來建立查詢, 並分析收集的資料。

資訊安全中心的免費層和標準層皆可使用搜尋。  記錄搜尋中的可用資料取決於套用至您工作區的層級。  如需詳細資訊，請參閱資訊安全中心[價格頁面](../security-center/security-center-pricing.md)。


> [!NOTE]
> 資訊安全中心不會儲存免費層下工作區的安全性資料。 您可以將各種不同的記錄傳送到在免費層下的工作區，並在該資料上搜尋，但是搜尋結果不會包含資訊安全中心的資料。 資訊安全中心只會儲存標準層下工作區的資料。
>
>

## <a name="access-search"></a>存取搜尋
1. 在資訊安全中心主功能表下，選取 [搜尋]。

   ![選取記錄搜尋][1]

2. 資訊安全中心會列出您 Azure 訂用帳戶下的所有工作區。 選取工作區。 (如果您只有一個工作區，這個工作區選取器不會出現。)

   ![選取工作區][2]

3. [記錄搜尋] 隨即開啟。 若要查詢所選取工作區下的詳細資料，請輸入此範例查詢：

   SecurityEvent | 其中 EventID = = 4625 | summarize count() by TargetAccount

   [結果] 會顯示所有無法登入的帳戶 (事件 4625)。

   ![搜尋結果][3]

如需如何在選取的工作區下查詢資料的詳細資訊, 請參閱[Kusto 查詢語言](../log-analytics/log-analytics-search-reference.md)。

## <a name="next-steps"></a>後續步驟
您會在本文中了解如何存取資訊安全中心的搜尋。 資訊安全中心使用 Azure 監視器記錄搜尋。 若要深入瞭解 Azure 監視器記錄搜尋, 請參閱:

- [什麼是 Azure 監視器記錄？](../log-analytics/log-analytics-overview.md) – Azure 監視器記錄的總覽
- [瞭解 Azure 監視器記錄中的記錄搜尋](../log-analytics/log-analytics-log-search-new.md)-描述記錄檔搜尋在 Azure 監視器記錄中的使用方式, 並提供在建立記錄搜尋之前應瞭解的概念
- [在 Azure 監視器記錄中使用記錄搜尋尋找資料](../log-analytics/log-analytics-log-searches.md)–使用記錄搜尋的教學課程
- [Kusto 搜尋參考](../log-analytics/log-analytics-search-reference.md)–說明 Azure 監視器記錄中的查詢語言

若要深入了解資訊安全中心，請參閱：

- [資訊安全中心概觀](security-center-intro.md) – 說明資訊安全中心的主要功能

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
