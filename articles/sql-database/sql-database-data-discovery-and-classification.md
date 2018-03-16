---
title: "Azure SQL Database 的資料探索與分類 | Microsoft Docs"
description: "Azure SQL Database 的資料探索與分類"
services: sql-database
documentationcenter: 
author: giladm
manager: shaik
ms.reviewer: carlrab
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: security
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: giladm
ms.openlocfilehash: da4f72e61607dcad7314a2fe65324da4635752c5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2018
---
# <a name="azure-sql-database-data-discovery-and-classification"></a>Azure SQL Database 的資料探索與分類
資料探索與分類 (目前處於預覽階段) 提供內建於 Azure SQL Database 的進階功能，可用於**探索**、**分類**、**標記** & **保護**資料庫中的敏感性資料。
對於最具敏感性的資料 (商業/財務、醫療保健及 PII 等) 進行探索與分類，可在組織的資訊保護方面扮演著關鍵角色。 它可以作為以下的基礎結構：
* 協助符合資料隱私標準和法規合規性需求，例如 GDPR。
* 各種安全性案例，例如針對敏感性資料異常存取的監視 (稽核) 及警示。
* 控制對包含高度敏感性資料之資料庫的存取，並強化安全性。

## <a id="subheading-1"></a>概觀
資料探索與分類導入一組進階服務和新的 SQL 功能，構成目標是保護資料而不只是資料庫的全新 SQL 資訊保護典範：
* **探索與建議**：分類引擎會掃描您的資料庫，並識別包含可能是敏感性資料的資料行。 接著，它能提供輕鬆的方式，讓您透過 Azure 入口網站檢閱並套用適當的分類建議。
* **標記**：使用導入 SQL 引擎的全新分類中繼資料屬性，可以在資料行上持續標記敏感性分類標籤。 此中繼資料還可利用在進階的敏感性稽核和保護案例上。
* **查詢結果集敏感度**：為執行稽核，查詢結果集的敏感度是即時計算的。
* **可見性**：資料庫分類狀態可以在入口網站的詳細儀表板中檢視。 此外，您可以下載 Excel 格式的報表以用於合規性、稽核用途及其他需求。

## <a id="subheading-2"></a>探索、分類和標記敏感性資料行
下一節描述關於探索、分類及標記資料庫中包含敏感性資料之資料行，以及檢視資料庫目前的分類狀態和匯出報告的步驟。

分類包含兩個中繼資料屬性：
* 標籤：主要分類屬性，用來定義儲存在資料行中的資料敏感度等級。  
* 資訊類型：為儲存在資料行中的資料類型提供額外的細微性。

<br>
**將您的 SQL Database 分類：**

1. 移至 [Azure 入口網站](https://portal.azure.com)。

2. 在您的 SQL Database 中，瀏覽至 [資料探索與分類 (預覽)] 設定。

    ![瀏覽窗格][1]

3. [概觀] 索引標籤包含資料庫目前分類狀態的摘要，包括所有已分類資料行的詳細清單，您也可以篩選這些資料行來只檢視特定的結構描述組件、資訊類型與標籤。 如果您尚未分類任何資料行，請[跳至步驟 5](#step-5)。

    ![瀏覽窗格][2]

4. 若要下載 Excel 格式的報表，請按一下視窗頂端功能表中的 [匯出] 選項。

    ![瀏覽窗格][3]

5.  <a id="step-5"></a>若要開始分類資料，請按一下視窗頂端的 [分類] 索引標籤。

    ![瀏覽窗格][4]

6. 分類引擎會掃描您的資料庫，以尋找包含可能是敏感性資料的資料行，並提供 [建議的資料行分類] 清單。 檢視並套用分類建議：

    * 若要檢視建議的資料行分類清單，請按一下視窗底部的建議面板：

        ![瀏覽窗格][5]

    * 檢閱建議清單：若要接受針對特定資料行的建議，請選取相關資料列左側資料行中的核取方塊。 您也可以選取建議資料表標頭中的核取方塊，將「所有建議」標記為接受。

        ![瀏覽窗格][6]

    * 若要套用選取的建議，請按一下藍色的 [接受選取的建議] 按鈕。

        ![瀏覽窗格][7]

7. 您也可以選擇將資料行「手動分類」，或同時採用手動分類和建議分類：

    * 按一下視窗頂端功能表中的 [新增分類]。

        ![瀏覽窗格][8]

    * 在開啟的內容視窗中，選取您想分類的 [結構描述] > [資料表] > [資料行]，以及資訊類型和敏感度標籤。 然後按一下內容視窗底部的藍色 [新增分類] 按鈕。

        ![瀏覽窗格][9]

8. 若要完成分類，並持續以新的分類中繼資料標記資料庫資料行，請按一下視窗頂端功能表中的 [儲存]。

    ![瀏覽窗格][10]

## <a id="subheading-3"></a>對敏感性資料的存取進行稽核

資訊保護範例的重要層面是能夠監視對敏感性資料的存取。

我們已強化 [Azure SQL Database 稽核](https://docs.microsoft.com/azure/sql-database/sql-database-auditing)，並在稽核記錄中包含新欄位 *data_sensitivity_information*，其中會記錄查詢所傳回之實際資料的敏感度分類 (標籤)。

![瀏覽窗格][11]

## <a id="subheading-4"></a>後續步驟
請考慮設定 [Azure SQL Database 稽核](https://docs.microsoft.com/azure/sql-database/sql-database-auditing)，以監視和稽核對已分類敏感性資料的存取。

<!--Anchors-->
[SQL Data Discovery & Classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Next Steps]: #subheading-4

<!--Image references-->
[1]: ./media/sql-data-discovery-and-classification/1_data_classification_settings_menu.png
[2]: ./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png
[3]: ./media/sql-data-discovery-and-classification/3_data_classification_export_report.png
[4]: ./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png
[5]: ./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png
[6]: ./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png
[7]: ./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png
[8]: ./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png
[9]: ./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png
[10]: ./media/sql-data-discovery-and-classification/10_data_classification_save.png
[11]: ./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png
