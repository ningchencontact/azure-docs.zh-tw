---
title: 一般成本分析會在 Azure 成本管理中使用
description: 本文說明如何在 Azure 成本管理中取得一般成本分析工作的結果。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/16/2019
ms.topic: conceptual
ms.service: cost-management
manager: adwise
ms.custom: ''
ms.openlocfilehash: cc133a75506dcbe552d380e2a6de219a47aca3d8
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2019
ms.locfileid: "72600185"
---
# <a name="common-cost-analysis-uses"></a>一般成本分析使用

Azure 成本管理使用者通常會想要回答許多其他人所詢問的問題。 本文會逐步引導您取得成本管理中常見成本分析工作的結果。

## <a name="view-cost-breakdown-by-azure-service"></a>查看 Azure 服務的成本分解

藉由 Azure 服務來查看成本可協助您進一步瞭解基礎結構中最符合成本的部分。 例如，VM 計算成本可能很小。 不過，由於從 Vm 發出的資訊量，您可能會產生大量的網路成本。 瞭解 Azure 服務的主要成本驅動程式是不可或缺的，因此您可以視需要調整服務使用量。

1. 在 Azure 入口網站中，流覽至範圍的 成本分析。 例如：**成本管理 + 帳單** > **成本管理** > **成本分析**。
1. 選取 [**依服務的成本**]，然後依**服務層**分組。
1. 將 [視圖] 變更為 [**資料表**]。

![Azure 服務的成本分解](./media/cost-analysis-common-uses/breakdown-by-service.png)

## <a name="view-cost-breakdown-by-azure-resource"></a>依 Azure 資源查看成本明細

您的服務是以 Azure 資源建立。 根據資源來檢查成本可協助您快速找出您的主要成本參與者。 如果服務的資源太昂貴，請考慮進行變更以降低成本。

1. 在 Azure 入口網站中，流覽至範圍的 成本分析。 例如：**成本管理 + 帳單** > **成本管理** > **成本分析**。
1. 選取 [**依資源的成本**]。
1. 將 [視圖] 變更為 [**資料表**]。

![依 Azure 資源查看成本明細](./media/cost-analysis-common-uses/cost-by-resource.png)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>依選取的維度查看成本明細

維度可讓您根據費用中顯示的各種中繼資料值來組織成本。 例如，您可以依位置將成本分組。

1. 在 Azure 入口網站中，流覽至範圍的 成本分析。 例如：**成本管理 + 帳單** > **成本管理** > **成本分析**。
1. 選取 [**群組依據**] 篩選準則。  
    依專案 ![Select 群組 ](./media/cost-analysis-common-uses/group-by.png)
1. （選擇性）您可以儲存視圖以供稍後使用。
1. 按一下圖形下方的圓形圖，以查看更詳細的資料。  
    ![依選取的維度查看成本明細](./media/cost-analysis-common-uses/drill-down.png)

## <a name="view-costs-per-day-or-by-month"></a>每天或每月查看成本

查看每日和每月成本可協助您進一步瞭解您的成本是否在一周或一年。 如果您在假期期間擁有更多客戶流量，會導致 Azure 成本的對應增加嗎？ 星期五比星期一更昂貴的日子嗎？

1. 在 Azure 入口網站中，流覽至範圍的 成本分析。 例如：**成本管理 + 帳單** > **成本管理** > **成本分析**。
1. 將資料**細微性**設定為 [**每月**] 或 [**每日**]。

![每天查看成本](./media/cost-analysis-common-uses/daily-granularity.png)

## <a name="view-costs-for-a-specific-tag"></a>查看特定標記的成本

許多 Azure 使用者會將標籤套用至其資源，例如成本中心或開發環境（生產和測試），以更好的方式分類費用。 標記會在成本分析中顯示為維度。 您可以使用維度取得自訂標記分類的深入解析。

1. 在 Azure 入口網站中，流覽至範圍的 成本分析。 例如：**成本管理 + 帳單** > **成本管理** > **成本分析**。
1. 針對您的標記選取 [**群組依據**]。

![查看特定標記的成本](./media/cost-analysis-common-uses/tag.png)

## <a name="download-your-usage-details"></a>下載使用情況詳細資料

您的使用方式詳細資料包告檔案（CSV 格式）提供了應計給發票的所有費用明細。 您可以使用報表來比較，並進一步瞭解您的發票。 您發票上的每個計費費用都會對應到使用量報告中的細分費用。

1. 在 Azure 入口網站中，流覽至帳單帳戶或訂用帳戶的 **使用量和費用** 索引標籤。 例如：**成本管理 + 帳單** > **計費** > **使用量 + 費用**。
1. 選取要下載的行專案，然後按一下下載符號。  
    ![下載使用方式和費用](./media/cost-analysis-common-uses/download1.png)
1.  選取要下載的使用方式檔案。  
    ![選擇要下載的使用方式檔案](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>查看每月 EA 成本明細

您的 EA 註冊會為整個組織累算成本。 瞭解成本的累積和隨時間開票的方式可協助您參與適當的專案關係人，以確保成本是以合理的方式管理。

1. 在 Azure 入口網站中，流覽至 **成本管理 + 帳單**  > **總覽**。
1. 按一下 [目前月份的**明細**]，並查看您的承諾用量相應減少。  
    ![EA 成本總覽-細目摘要 ](./media/cost-analysis-common-uses/breakdown1.png)
1.  按一下 [**使用量和費用**] 索引標籤，並在所選的時間範圍內查看上個月的明細。  
    ![Usage 和費用 索引標籤 ](./media/cost-analysis-common-uses/breakdown2.png)

## <a name="view-enrollment-monthly-cost-by-term"></a>依詞彙查看註冊每月費用

使用您註冊的每月成本的圖形化視圖，以瞭解給定期間的成本趨勢和發票金額。

1. 在 Azure 入口網站中，流覽至範圍的 成本分析。 例如：**成本管理 + 帳單** > **成本管理** > **成本分析**。
1. 選取您的註冊並設定註冊期限。
1. 將資料細微性設定為 [每月]，然後將 [視圖] 設定為 [資料**行（堆疊）** ]。

您可以分組並篩選您的資料，以進行更詳細的分析。

![依詞彙的每月註冊成本](./media/cost-analysis-common-uses/enrollment-term1.png)

## <a name="view-ea-enrollment-accumulated-costs"></a>查看 EA 註冊累積成本

查看一段時間的淨累積費用，以瞭解貴組織在指定期間內的整體支出。

1. 在 Azure 入口網站中，流覽至範圍的 成本分析。 例如：**成本管理 + 帳單** > **成本管理** > **成本分析**。
1. 選取您的註冊，然後查看目前累積的成本。

![註冊累積成本](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)

## <a name="next-steps"></a>後續步驟
- 如果您尚未完成成本管理的第一個快速入門，請在[開始分析成本](quick-acm-cost-analysis.md)閱讀它。
- 閱讀[成本管理檔](index.yml)。
