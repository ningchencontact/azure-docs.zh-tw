---
title: Azure Application Insights 使用世代 | Microsoft Docs
description: 分析具有共通點的一組使用者、工作階段、事件或作業
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/10/2018
ms.author: mbullwin ; daviste
ms.openlocfilehash: 68453499cc6477cc079a342906614e6873938fc8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="application-insights-cohorts"></a>Application Insights 世代

世代是指具有共通點的一組使用者、工作階段、事件或作業。 在 Azure Application Insights 中，世代會由分析查詢所定義。 如果您發現自己正重複分析一組特定的使用者或事件，世代將可讓您有更多彈性來精準呈現您感興趣的集合。

![世代窗格](.\media\app-insights-usage-cohorts\001.png)

## <a name="cohorts-versus-basic-filters"></a>世代與基本篩選

雖然世代的使用方式與篩選類似，但世代的定義是從自訂的分析查詢所建置的，因此其可調適性和複雜性遠高於篩選。 不同於篩選，您可以儲存世代，讓其他小組成員可重複加以使用。

您可以為所有在您的應用程式中嘗試使用新功能的使用者定義世代。 透過這個在您的 Application Insights 資源中儲存的世代，後續您只需要點選一下，就可分析這個使用者群組。

> [!NOTE]
> 世代一經建立後，即可供使用者、工作階段、事件和使用者流程等工具使用。

## <a name="example-engaged-users"></a>範例：參與的使用者

您的小組將參與的使用者定義為在指定月份內使用過您的應用程式五次或更多次的任何人。 現在，我們將為這些參與的使用者定義世代。

1. 開啟 [世代] 工具。

2. 按一下 [範本庫] 索引標籤。在此處您會看到各種世代的範本集合。

3. 選擇 [參與的使用者 - 依使用天數]。

    此世代有三個參數：
      * **活動**：可讓您選擇哪些事件和頁面檢視應計為「使用」。
      * **期間**：月份的定義。
      * **UsedAtleastCustom**：使用者要被計為參與的使用者在一段期間內所需使用某項目的次數。

4. 將 **UsedAtleastCustom** 變更為「5 天以上」，並將 [期間] 保留為預設的 28 天。

    ![映像](.\media\app-insights-usage-cohorts\003.png)

    現在，此世代代表在過去 28 天的五個不同日期透過任何自訂事件或頁面檢視傳送的所有使用者識別碼。

5. 按一下 [檔案] 。

   > [!TIP]
   >  為您的世代指定名稱，例如「參與的使用者 (5 天以上)」，並根據您是否要讓有權存取此 Appication Insights 資源的其他人看見此世代，將它儲存到「我的報告」或「共用報告」。

6. 按一下 [回到範本庫]。

### <a name="what-can-you-do-with-this-cohort"></a>此世代有何功能？

開啟 [使用者]工具 > 在 [顯示] 下拉式清單中 > 從 [使用者屬於...] 下選擇您已建立的世代

此時，「使用者」工具已篩選出下列使用者世代：

![篩選出特定世代的使用者窗格](.\media\app-insights-usage-cohorts\004.png)

有一些重點值得注意：
   * 這是您無法透過一般篩選條件建立的集合。 日期邏輯更為進階。
   * 您可以使用 「使用者」工具中的一般篩選條件進一步篩選此世代。 因此，雖然此世代是以 28 天的時間範圍定義的，您仍可在「使用者」工具中將時間範圍調整為 30、60 或 90 天。 

這可讓您詢問更複雜的問題，例如：_在過去 28 天曾參與的這些人，在過去 60 天有哪些行為？_ - 透過查詢產生器無法呈現出此類結果。

## <a name="example-events-cohort"></a>範例：事件世代

您也可以建立事件的世代。 現在，我們將定義事件和頁面檢視的世代，然後看看如何從其他工具加以使用。 定義一組您的小組視為_有效使用_的事件，或定義一組與特定新功能有關的事件，可能會有幫助。

1. 開啟 [世代] 工具。

2. 按一下 [範本庫] 索引標籤。在此處您會看到各種世代的範本集合。

3. 選擇 [事件選擇器]。

    ![事件選擇器的螢幕擷取畫面](.\media\app-insights-usage-cohorts\006.png)

4. 在 [活動] 下拉式清單中，選取要納入世代中的事件

5. 儲存世代，並為它命名。

## <a name="example-active-users-where-you-modify-query"></a>範例：您為其修改查詢的有效使用者

先前的兩個世代是使用下拉式清單定義的。 但我們也可以使用分析查詢來定義世代，以達到整體彈性。 我們將藉由建立英國的使用者世代，來了解其具體方式。

![逐步使用世代工具的動畫影像](.\media\app-insights-usage-cohorts\cohorts0001.gif)

1. 開啟 [世代] 工具 > 按一下 [範本庫] 索引標籤 > 選擇 [空白使用者世代]。

    ![空白使用者世代](.\media\app-insights-usage-cohorts\001.png)

    其中包含三個區段：
       * Markdown 文字區段；您可以在其中為其他小組成員詳細說明世代。

       * 參數區段，可讓您自行設定參數，例如**活動**，以及上述兩個範例中的其他下拉式清單。

       * 查詢區段；可讓您使用分析查詢定義世代。

    在查詢區段中，您可以[撰寫分析查詢](https://docs.loganalytics.io/index)，用以選取特定的幾列來說明您要定義的世代。 然後，「世代」工具會隱含地在查詢中加入 “| summarize by user_Id” 子句。 您可以在查詢下方的資料表中加以預覽，以確定您的查詢會傳回結果。

    > [!NOTE]
    > 如果未看見查詢，請嘗試調高區段的高度，以呈現查詢。 此區段的開頭會以動畫 .gif 說明調整大小的行為。

2. 將下列項目複製並貼到查詢編輯器中：

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. 按一下 [執行查詢]。 您應該會看到使用者識別碼出現在資料表中。 若未看到，請變更為您的應用程式有使用者的國家/地區。

4. 儲存世代並為其命名。

## <a name="frequently-asked-questions"></a>常見問題集

_我已從特定國家/地區定義使用者世代。當我在「使用者」工具中比較此世代與直接在「使用者」工具中設定該國家/地區的篩選條件時，我看到不同的結果。原因為何？_

世代與篩選並不相同。 假設您有來自英國的使用者世代 (如上述範例所定義)，並將其結果與設定「國家或地區 = 英國」篩選條件相比較。

* 世代版本將會顯示在目前的時間範圍內從英國至少傳送了一個事件之使用者的所有事件。 如果您依國家或區域劃分，您可能會看到許多國家和區域。
* 篩選版本只會顯示來自英國的事件。 然而，如果您依國家或區域劃分，您將只會看到英國。

## <a name="learn-more"></a>深入了解
- [分析查詢語言](https://go.microsoft.com/fwlink/?linkid=856587)
- [使用者、工作階段、事件](app-insights-usage-segmentation.md)
- [使用者流程](app-insights-usage-flows.md)
- [使用量概觀](app-insights-usage-overview.md)