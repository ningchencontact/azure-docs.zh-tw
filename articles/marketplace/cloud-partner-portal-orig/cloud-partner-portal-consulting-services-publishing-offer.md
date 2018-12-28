---
title: Azure 與 Dynamcis 365 諮詢服務供應項目 | Microsoft Docs
description: 在「Cloud Partner 入口網站」中定義和發佈 Azure 或 Dynamcis 365 諮詢服務供應項目的指南。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 7ea4a58f072071f60cf87d1e8817ae19658be5fb
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310414"
---
# <a name="azure-and-dynamics-365-consulting-service-offer"></a>Azure 與 Dynamcis 365 諮詢服務供應項目

<table> <tr> <td>本節說明如何將諮詢服務發佈到 Microsoft <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a> 或 <a href="https://appsource.microsoft.com">AppSource Marketplace</a>。 以 Microsoft <a href="https://dynamics.microsoft.com">Dynamics 365</a>、<a href="https://products.office.com">Office 365</a>、<a href="https://powerbi.microsoft.com">Power BI</a> 及 <a href="https://powerapps.microsoft.com">PowerApps</a> 為基礎的解決方案有資格在 AppSource 上列出。 以其他 Microsoft <a href="https://azure.microsoft.com/services">Azure 服務</a>為基礎的其他供應項目則有資格在 Azure Marketplace 上列出。 </td> <td><img src="./media/consulting-services-publishing-offer/consulting-icon1.png"  alt="Microsoft consulting icon" /></td> </tr> </table>


## <a name="publishing-benefits"></a>發佈的優點

發佈到任一 Microsoft 市集具有相當多的優點：

- 利用 Microsoft 品牌來推廣您的公司。
- 透過 AppSource 潛在地接觸超過 1 億個 Office 365 和 Dynamics 365 使用者；透過 Azure Marketplace 接觸超過 20 萬個組織。
- 從這些市集取得高品質潛在客戶。
- 讓 Microsoft 現場和電話銷售團隊推廣您的服務。


## <a name="define-your-consulting-services-offer"></a>定義您的諮詢服務供應項目

定義您的套裝式諮詢服務供應項目。 專注於固定範圍、固定期間、估計的、固定的價格 (或免費) 和適用於單一客戶的售前導向供應項目。 選取受歡迎且有效為您帶來新業務的可重複套裝式參與活動。

## <a name="publish-a-consulting-service-offer"></a>發佈諮詢服務供應項目

下列各節描述發佈諮詢服務供應項目的程序

1.  建立新的供應項目
2.  定義供應項目設定
3.  輸入電子店面詳細資料，以及要在 Azure Marketplace 或 AppSource 中發佈。
4.  發佈您的供應項目

### <a name="create-a-new-offer"></a>建立新的供應項目

若要建立新的供應項目，請完成下列步驟：

1.  在 Cloud Partner 入口網站的主功能表上，選取 [新增供應項目]。
2.  在 [新增供應項目] 功能表中，選取 [諮詢服務]。

    ![建立新的諮詢服務供應項目](media/consulting-services-publishing-offer/cppselectnewconsultingoffer.png)

### <a name="define-offer-settings"></a>定義供應項目設定

在 [新增供應項目] 畫面上，第一步是建立供應項目身分識別。  供應項目身分識別由三個部分所組成：**供應項目識別碼**、**發行者識別碼**以及**名稱**。  下列各節中涵蓋每個這些部分。

#### <a name="offer-id"></a>供應項目識別碼

這個識別碼是您第一次提交供應項目時，所建立的唯一名稱。 它只能包含小寫英數字元、連字號或底線。 供應項目識別碼會顯示在 URL 中，並影響搜尋引擎結果。 例如，*yourcompanyname\_exampleservice*

如範例中所示，供應項目識別碼會附加至您的發行者識別碼，以建立唯一識別碼。 這會公開為永久連結，且可由搜尋引擎預定和編製索引。 

*供應項目上架之後，即無法更新其識別碼*

#### <a name="publisher-id"></a>發行者識別碼

此識別碼與您的帳戶相關。 當您登入您的組織帳戶時，您的發行者識別碼會顯示在下拉式功能表中。

#### <a name="name"></a>Name

此字串是將在 AppSource 或 Azure Marketplace 上顯示為供應項目名稱的字串。

**重要事項：** 只輸入實際的服務名稱。 請勿包含持續時間和服務類型。

Edgewater Fullscope 提供的下列範例顯示供應項目名稱的組合方式。 供應項目名稱會顯示為：

![建立新的諮詢服務供應項目](media/consulting-services-publishing-offer/cppsampleconsultingoffer.png)

供應項目名稱由四個部分組成：

-   **持續時間：** 在編輯器的 [電子店面詳細資料] 索引標籤中定義。 持續時間可以用小時、天或週來表示。
-   **服務類型：** 在編輯器的 [電子店面詳細資料] 索引標籤中定義。 服務類型為 `Assessment`、`Briefing`、`Implementation`、`Proof of concept` 及 `Workshop`。
-   **前置詞：** 由檢閱者插入
-   **名稱：** 在 [供應項目設定] 頁面中定義。

下列清單提供數個妥善命名的供應項目名稱：

-   專業服務的基本資訊：1 小時簡報
-   雲端移轉平台：1 小時簡報
-   PowerApps 和 Microsoft Flow：1 天研討會
-   Azure Machine Learning 服務：3 週概念證明
-   Brick and Click 零售解決方案：1 小時簡報
-   使用您自己的資料：1 週研討會
-   雲端分析：3 天研討會
-   Power BI 訓練：3 天研討會
-   銷售管理解決方案：1 週實作
-   CRM 快速入門：1 天研討會
-   Dynamics 365 for Sales：2 天評量

完成 [供應項目設定] 索引標籤之後，您可以儲存您的提交。 供應項目名稱現在會顯示在編輯器上方，您可以在 [所有供應項目] 中找到它。

### <a name="enter-storefront-details"></a>輸入電子店面詳細資料

接下來，您需要輸入店面的詳細資料。 [電子店面詳細資料] 由下列區段組成：

-   產品/服務詳細資料
-   發行者資訊
-   清單詳細資料
-   行銷成品

#### <a name="offer-details"></a>產品/服務詳細資料

[產品/服務詳細資料] 區段包含下列欄位：

-   供應項目摘要
-   供應項目描述

##### <a name="offer-summary"></a>供應項目摘要

[供應項目摘要] 是顯示在供應項目名稱底下的供應項目簡短描述。 輸入供應項目摘要時，請使用純文字，且不應包含分行符號。 下列是供應項目摘要及其對應之供應項目名稱的良好範例：

*範例 1*

-   **供應項目名稱：** 雲端分析：3 天研討會
-   **供應項目摘要：** Microsoft Azure 和 Power BI 的概觀、環境的評定，以及迷你 POC。

*範例 2*

-   **供應項目名稱：** 工業 Azure IoT：30 天概念證明
-   **供應項目摘要：** 建立工業連線的產品項試驗，以安全地將現場的設備連線到 Azure IoT 中樞解決方案 (含儀表板、報表和通知)。

*範例 3*

-   **供應項目名稱：** 專業服務：1 小時簡報
-   **供應項目摘要：** 預先設定、擴充的 Dynamics 365 for Operations 解決方案 (提供適用於專業服務的專案、計費和資源管理) 的概觀和示範。

*範例 4*

-   **供應項目名稱：** 您世界中的 Power BI：4 小時研討會
-   **供應項目摘要：** 啟動並執行您的第一個儀表板，並了解最佳做法。 最多 12 個學生 (現場指導)。

*範例 5*

-   **供應項目名稱：** Dynamics 和專案：3 天評量
-   **供應項目摘要：** 專為專業服務公司及專案導向業務設計之 ERP 解決方案的需求收集與評定。

##### <a name="offer-description"></a>供應項目描述

「諮詢服務」供應項目的描述。 良好的供應項目描述會涵蓋參與活動的確切詳細資料，包括其活動情況和客戶最後可獲得的資訊。 它應該清楚地協助客戶了解他們能獲得的項目。

請勿在您的 [供應項目描述] 中包含連絡您的電子郵件連結或電話號碼。 您的供應項目將會包含 [與我連絡] 按鈕，它會將潛在客戶上傳到您可以在供應項目上識別出的潛在客戶管理目標。

您會以 Markdown 格式輸入供應項目描述。 如果您不熟悉如何使用 Markdown 或 HTML 格式設定，您可以在 [Microsoft Docs 網站](https://docs.microsoft.com/contribute/how-to-write-use-markdown)上檢閱資源。

使用這些格式可確保您的供應項目對於客戶有最大的可讀性。

請讓供應項目描述保持簡短且符合字元數限制，因為使用者並不喜歡閱讀冗長的文字。 您仍然可以選擇上傳詳細描述您供應項目的行銷摺頁冊、資料表和其他文件。

下列範例示範撰寫良好的 [供應項目描述]，以及其相關名稱和摘要：

**供應項目名稱：** 雲端分析：3 天研討會

**供應項目摘要：** Microsoft Azure 和 Power BI 的概觀、環境的評定，以及迷你 POC。

**供應項目描述：** 這個 3 天的工作坊適用於技術和業務領導者，並且會在客戶的場地現場舉辦。

***議程***

第 1 天

-   著重於如何在 Microsoft 雲端內使用 Azure Data Lake、HDInsight 或 Azure SQL 資料倉儲來保護、調整，以及組織資料。

第 2 天

-   涵蓋如何使用 Microsoft R 和 Azure Machine Learning 來設定和部署進階分析解決方案。

第 3 天

-   涵蓋如何使用 Power BI 繪製可採取動作的見解和作業化的分析，包含建置 Power BI 儀表板的共同作業課程。

在工作坊結束時，客戶將能夠定義在 Microsoft 雲端中的資料和分析解決方案的概略計劃和藍圖。

格式如下列之供應項目的範例 Markdown 檔案：

    This 3-day workshop is for technical and business leaders and is held on-site at the client’s facility.

      ### Agenda

      **Day 1**

      * Focuses on how to secure, scale, and organize data within the Microsoft cloud using Azure Data Lake, HDInsight, or Azure SQL Data Warehouse

      **Day 2**

      * Covers how to configure and deploy advanced analytics solutions with Microsoft R and Azure Machine Learning

      **Day 3**

      * Covers how to draw actionable insights and operationalize analytics with Power BI, including a collaborative session to co-build a Power BI dashboard.


      ### Deliverables
      By the end of the workshop, the client will be able to define a high-level plan and an implementation roadmap for data and analytics solutions in the Microsoft cloud.


#### <a name="publisher-information"></a>**發行者資訊**

**MPN 識別碼**

您的 9 位數 Microsoft 合作夥伴網路 (MPN) 識別碼。 如果您沒有 MPN 識別碼，可以移至 Microsoft 合作夥伴中心取得。

**合作夥伴中心識別碼**

新增合作夥伴中心識別碼 (若有識別碼)。

**MPN 識別碼**

輸入祕密金鑰，以在上線之前先在 AppSource 上預覽您的供應項目。
此識別碼不是密碼。

#### <a name="listing-details"></a>清單詳細資料

**諮詢服務類型**

Microsoft 是著重在固定的範圍、固定的期間、估計或固定的價格 (或免費)，主要是提供給一客戶的售前導向諮詢服務，以及在現場或虛擬方式進行的評定、實作、概念證明、簡報或工作坊供應項目 (簡報必須是現場進行)。

下列五種類型的供應項目包含：

-   **評量：** 對客戶環境的評估，以判斷解決方案的適用性，並提供估計的成本和時間。
-   **簡報：** 使用架構、示範和客戶範例引起客戶興趣的解決方案或諮詢服務介紹。 簡報必須是現場進行。
-   **實作：** 可進行完整安裝以產生完整有效的解決方案。 針對此試驗，Microsoft 建議您將解決方案限制為可在一週 (或更少) 內實作。
-   **概念證明：** 可以判斷解決方案是否會符合客戶需求的有限範圍實作。
-   **研討會：** 在客戶場地舉行的互動參與活動，根據客戶的資料或環境而量身打造訓練課程、簡報、評估或示範。

**國家/地區可用性**

選取可取得此諮詢服務供應項目的國家/地區和區域。 單一供應項目不能在多個國家/地區或區域發佈。 必須針對每個國家/地區或區域都建立新的供應項目。

**產業**

選擇您的諮詢服務供應項目最適用的產業。

**Duration**

在 [持續時間] 底下選擇數字 (如 3、4 等)，然後選取 [小時]、[天]、或 [週]。

**主要產品**

若要發佈到 Azure Marketplace，請選取 [Azure] 作為主要產品，然後選取相關的 [解決方案區域]。

若要發佈至 AppSource，請選取 [Dynamics 365]、[Power BI] 或 [PowerApps] 作為您主要的產品。 您也可以選取其他相關的「適用產品」，然後您的諮詢服務供應項目在 AppSource 上，就會在每個這些產品相關聯的清單中都顯示。

**相關專長認證**

選取與此供應項目相關專長認證，以和產品/服務詳細資料一起顯示。

#### <a name="marketing-artifacts"></a>行銷成品

**公司標誌 (.png 格式，48 x 48 像素)**

上傳在供應項目資源庫檢視頁面中，會出現在您供應項目磚上的影像。 該影像必須為 .png 影像且解析度為 48 x 48 像素。

**公司標誌公司標誌 (.png 格式，216 x 216 像素)**

上傳會出現在供應項目詳細資料頁面上的影像。 該影像必須為 .png 影像且解析度為 216 x 216 像素。

**影片 (最多 4 個)**

上傳最多四個客戶案例研究影片，或客戶參考影片。 如果沒有這些影片，可以上傳說明與供應項目相關的公司專業知識。 如果您有 Power BI 或 PowerApps 解決方案展示工具，請在此上傳展示工具影片。 影片連結必須是 YouTube 或 Vimeo。

**文件 (最多 3 個)**

上傳詳細描述您的諮詢服務供應項目的行銷摺頁冊。 或者，您也可以上傳公司概觀、資料表或案例研究。

**螢幕擷取畫面 (最多 5 個)**

上傳最多五個影像，提供有關供應項目、供應項目交付項目或公司的更多資訊。 例如，行銷摺頁冊的片段，簡報的相關投影片，或是顯示公司趨勢或專業知識的影像。

### <a name="publish-your-offer"></a>發佈您的供應項目

您完成 [供應項目設定]、[電子店面詳細資料] 和 [連絡人] 之後，請選取 [發佈]，並提供電子郵件地址。 當 Microsoft 準備發佈您的供應項目時，您會收到一封電子郵件，讓您在上線之前先預覽。 您隨時可以返回入口網站來檢查供應項目的狀態。

在發佈程序期間，供應項目可能會顯示為 [發行已取消] 或 [發佈失敗] 狀態。 此狀態是程序的正常部分，並可讓 Microsoft 對您的供應項目進行編輯。 如果您看到供應項目處於 [發行已取消] 狀態，請讓它保持該狀態。
