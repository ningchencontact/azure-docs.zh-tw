---
title: Azure 與 Dynamcis 365 諮詢服務供應項目 - 輸入店面詳細資料 | Microsoft Docs
description: 在 Cloud Partner 入口網站的 Azure 或 Dynamcis 365 諮詢服務供應項目中定義電子店面詳細資料的指南。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: pbutlerm
ms.openlocfilehash: ac383c85938f5cb7dfe4f711714f5bacd0c96b89
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60865355"
---
# <a name="storefront-details-tab"></a>電子店面詳細資料索引標籤

本文說明如何輸入您店面的詳細資料。 [電子店面詳細資料] 索引標籤由下列區段組成：

-   **產品/服務詳細資料**
-   **發行者資訊**
-   **清單詳細資料**
-   **行銷成品**

![建立新的諮詢服務供應項目 - [電子店面詳細資料] 索引標籤](media/consultingoffer-storefront-details.png)

## <a name="offer-details"></a>供應項目詳細資料

[產品/服務詳細資料] 區段包含下列方塊：

-   **供應項目摘要**
-   **供應項目描述**

### <a name="offer-summary"></a>供應項目摘要

[供應項目摘要] 是顯示在供應項目名稱底下的供應項目簡短描述。 使用純文字來輸入供應項目摘要，其中不含任何分行符號。 下列是良好的供應項目摘要及其對應之供應項目名稱的範例。

*範例 1*

-   **供應項目名稱**：雲端分析：3 天的工作坊
-   **供應項目摘要**：Microsoft Azure 和 Power BI 的概觀、目前環境的評量，以及迷你 POC。

*範例 2*

-   **供應項目名稱**：工業 Azure IoT：30 天的概念證明
-   **供應項目摘要**：建立工業連線的產品試驗，以安全地將現場的設備連線到 Azure IoT 中樞解決方案 (含儀表板、報表和通知)。

*範例 3*

-   **供應項目名稱**：專業服務：1 小時的簡報
-   **供應項目摘要**：預先設定且已擴充之 Dynamics 365 for Operations 解決方案的概觀和示範，該解決方案可針對專業服務提供專案、計費和資源的增強管理。

*範例 4*

-   **供應項目名稱**：您世界中的 Power BI：4 小時的工作坊
-   **供應項目摘要**：啟動並執行您的第一個儀表板，並了解最佳做法。 最多 12 個學生 (現場指導)。

*範例 5*

-   **供應項目名稱**：Dynamics 和專案：3 天的評量
-   **供應項目摘要**：專為專業服務公司及專案導向業務所設計之 ERP 解決方案的需求收集與評量。

### <a name="offer-description"></a>供應項目描述

在 [供應項目描述] 方塊中輸入諮詢服務供應項目的描述。 良好的供應項目描述涵蓋業務開發實際情況的確切詳細資料，以及客戶獲得的最終交付項目。 它應該清楚地協助客戶了解他們能獲得的項目。 包括您的供應項目與您提供諮詢服務的 Microsoft 產品有何關聯。

請勿在您的供應項目描述中包含您的電子郵件地址或電話號碼。 您的供應項目隨附 [與我連絡] 按鈕，可將潛在客戶上傳到您針對供應項目所識別的潛在客戶管理目標。

以 Markdown 格式輸入供應項目描述。 如果您不熟悉如何使用 Markdown 或 HTML 格式，請參閱[如何使用 Markdown 來撰寫 Docs](https://docs.microsoft.com/contribute/how-to-write-use-markdown)。

使用這些格式以確保客戶能輕鬆地閱讀您的供應項目。

請讓供應項目描述保持簡短且符合字元數限制，因為使用者並不喜歡閱讀冗長的文字。 您也可以上傳詳細描述您供應項目的行銷摺頁冊、資料表和其他文件。

下列範例示範撰寫良好的供應項目描述，以及其相關名稱和摘要：

**供應項目名稱**：雲端分析：3 天的工作坊

**供應項目摘要**：Microsoft Azure 和 Power BI 的概觀、目前環境的評量，以及迷你 POC。

**供應項目描述**：這個為期 3 天的工作坊適用於技術和業務負責人，並且會在客戶的場地現場舉辦。

議程

第 1 天

-   著重於如何在 Microsoft 雲端內使用 Azure Data Lake、Azure HDInsight 或 Azure SQL 資料倉儲來保護、調整，以及組織資料。

第 2 天

-   涵蓋如何使用 Microsoft R 和 Azure Machine Learning 來設定和部署進階分析解決方案。

第 3 天

-   涵蓋如何使用 Power BI 繪製可採取動作的見解並讓分析能夠運作，且包含共同建置 Power BI 儀表板的共同作業課程。

交付項目

在工作坊結束時，客戶將能定義在 Microsoft 雲端中的資料和分析解決方案的概略計劃和實作藍圖。

下列範例是先前供應項目的 Markdown 檔案：

    This 3-day workshop is for technical and business leaders and is held on-site at the client’s facility.

      ### Agenda

      **Day 1**

      * Focuses on how to secure, scale, and organize data within the Microsoft cloud by using Azure Data Lake, Azure HDInsight, or Azure SQL Data Warehouse.

      **Day 2**

      * Covers how to configure and deploy advanced analytics solutions with Microsoft R and Azure Machine Learning.

      **Day 3**

      * Covers how to draw actionable insights and operationalize analytics with Power BI and includes a collaborative session to cobuild a Power BI dashboard.


      ### Deliverables
      By the end of the workshop, the client will be able to define a high-level plan and an implementation roadmap for data and analytics solutions in the Microsoft cloud.


## <a name="publisher-information"></a>發行者資訊

**MPN 識別碼**

輸入您的九位數 Microsoft 合作夥伴網路 (MPN) 識別碼。 如果您沒有 MPN 識別碼，您可以在 Microsoft 合作夥伴中心取得一個。

**合作夥伴中心識別碼**

如果有的話，請輸入新的合作夥伴中心識別碼。

**MPN 識別碼**

輸入祕密金鑰，以在上線之前先在 AppSource 上預覽您的供應項目。
此識別碼不是密碼。

## <a name="listing-details"></a>清單詳細資料

**諮詢服務類型**

Microsoft 僅著重於固定範圍、固定期間、估計或固定價格 (或免費)，且主要為適用於單一客戶的發行前導向諮詢服務供應項目。 服務類型有評估、簡報、實作、證明概念，以及進行現場或虛擬研討會供應項目。 AppSource 諮詢服務市集不支援適用於受控或訂閱服務的清單。

>[!Note]
>AppSource 諮詢服務不是適合用於訂閱或依需求訓練的市集。

下列五種類型的供應項目包含：

-   **評估**：對客戶環境的評估，以判斷解決方案的適用性，並提供估計的成本和時間。
-   **簡報**：可以介紹解決方案或諮詢服務，使用架構、示範和客戶範例引起客戶的興趣。 簡報必須是現場進行。
-   **實作**：完整安裝以產生完全可行的解決方案。 針對此試驗，Microsoft 建議您將解決方案限制為可在一週 (或更少) 內實作。
-   **概念證明**：可以判斷解決方案是否符合客戶需求的有限範圍實作。
-   **研討會**：在客戶場地舉行的互動式參與活動，可以根據客戶的資料或環境量身打造訓練課程、簡報、評量或示範。

**國家/地區可用性**

選取可取得此諮詢服務供應項目的國家/地區和區域。 單一供應項目不能在多個國家/地區或區域發佈。 必須針對每個國家/地區或區域都建立新的供應項目。

>[!Note]
>AppSource 諮詢服務目前已在美國、英國和加拿大上線。 您可以針對尚未上線的國家/地區提交供應項目，而它將經過審核並準備好上線。 需要達到已準備好上線的供應項目數下限，才會開放新的國家/地區，因此，我們鼓勵針對尚未上線的國家/地區提交供應項目。

**產業**

選擇您的諮詢服務供應項目最適用的產業。

**Duration**

在 [持續時間] 底下選取數字 (如 3 或 4)，然後選取 [小時]、[天]、或 [週]。

**主要產品**

若要發佈至 Azure Marketplace，請選取 [Azure] 作為主要產品。 然後選取相關的 [解決方案區域]。

若要發佈至 AppSource，請選取 [Dynamics 365]、[Power BI] 或 [PowerApps] 作為您主要的產品。 您也可以選取其他相關的**適用產品**。 然後您的諮詢服務供應項目就會於 AppSource 上，在與每個這些產品相關聯的清單中顯示。

**相關專長認證**

選取與此供應項目相關專長認證，以和產品/服務詳細資料一起顯示。

## <a name="marketing-artifacts"></a>行銷成品

**公司標誌 (.png 格式，48 x 48 像素)**

上傳在供應項目資源庫檢視頁面中，出現在您供應項目磚上的影像。 該影像必須為 .png 影像且解析度為 48 x 48 像素。

**公司標誌 (.png 格式，216 x 216 像素)**

上傳會在您的供應項目詳細資料頁面顯示的影像。 該影像必須為 .png 影像且解析度為 216 x 216 像素。

**影片 (限四個)**

上傳最多四個客戶案例研究影片，或客戶參考影片。 如果沒有這些影片，可以上傳說明與供應項目相關的公司專業知識。 如果您有 Power BI 或 PowerApps 解決方案展示工具，請在此上傳展示工具影片。 影片連結必須是 YouTube 或 Vimeo。

**文件 (限三個)**

上傳詳細描述您的諮詢服務供應項目的行銷摺頁冊。 您也可以上傳公司概觀、資料表或個案研究。 請確定您的文件會使用推薦產品目前的名稱，而且不會推薦 Microsoft 競爭產品。

**螢幕擷取畫面 (限五個)**

上傳最多五個影像，提供有關您的供應項目、其交付項目或公司的更多資訊。 一些範例如，行銷摺頁冊的片段，簡報的相關投影片，或是顯示公司趨勢或專業知識的影像。

## <a name="next-steps"></a>後續步驟

您現在已經準備好[發佈您的諮詢服務](./cpp-consulting-service-publish-offer.md)供應項目。
