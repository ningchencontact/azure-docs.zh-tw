---
title: 使用 Microsoft Flow 自動化 Azure Application Insights 程序
description: 了解如何利用 Application Insights Connector，使用 Microsoft Flow 來快速自動執行可重複的程序。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: mbullwin
ms.openlocfilehash: 15299be83758c157bf3bc7d9fb27b50763b9148e
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438932"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>使用適用於 Microsoft Flow 的連接器自動執行 Azure Application Insights 程序

您是否發現自己在遙測資料上不斷重複地執行相同查詢，以檢查服務是否正常運作？ 想要將此類查詢自動化以尋找趨勢和異常，然後針對它們建立您自己的工作流程嗎？ Azure Application Insights connector for Microsoft Flow 是正確的工具，針對這些用途。

有了此整合，您就能立即自動執行許多流程，而不需撰寫任何一行程式碼。 在您使用 Application Insights 動作建立流程後，此流程會自動執行 Application Insights Analytics 查詢。 

您也可以新增額外的動作。 Microsoft Flow 提供數百個動作。 例如，您可以使用 Microsoft Flow 自動傳送電子郵件通知，或是在 Azure DevOps 中建立 Bug。 您也可以使用提供給適用於 Microsoft Flow 之連接器的任何一種[範本](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights)。 這些範本可加快建立流程的程序。 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="create-a-flow-for-application-insights"></a>建立 Application Insights 的流程

在此教學課程中，您將學習如何建立流程，該流程會使用 Analytics 自動叢集演算法將 Web 應用程式資料中的屬性分組。 此流程會以電子郵件自動傳送結果，這只是如何將 Microsoft Flow 和 Application Insights Analytics 一起使用的其中一個範例。 

### <a name="step-1-create-a-flow"></a>步驟 1：建立流程
1. 登入 [Microsoft Flow](https://flow.microsoft.com)，然後選取 [我的流程]。
2. 按一下 **的新**再**從空白建立**。

    ![從空白建立新的流程](./media/automate-with-flow/1createflow.png)

### <a name="step-2-create-a-trigger-for-your-flow"></a>步驟 2：建立流程的觸發程序
1. 在組建中索引標籤上選取**排程**，然後選取**排程-週期**。

    ![選取排程下建置它](./media/automate-with-flow/2schedule.png)

1. 在 **間隔**方塊中，輸入**1**，然後在**頻率**方塊中，選取**天**。
2. 按一下 **新增步驟**

    ![設定輸入頻率和間隔排程週期](./media/automate-with-flow/3schedulerecurrence.png)


### <a name="step-3-add-an-application-insights-action"></a>步驟 3：新增 Application Insights 動作
1. 搜尋 **Azure Application Insights**。
2. 按一下  **Azure Application Insights 視覺化 Analytics 查詢**。
 
    ![選擇一個動作：Azure Application Insights 視覺化 Analytics 查詢](./media/automate-with-flow/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>步驟 4：連線到 Application Insights 資源

若要完成此步驟，您需要資源的應用程式識別碼和 API 金鑰。 您可以從 Azure 入口網站擷取這些資訊，如下圖所示：

![Azure 入口網站中的應用程式識別碼](./media/automate-with-flow/5apiaccess.png)

![在 Azure 入口網站中的 API 金鑰](./media/automate-with-flow/6apikey.png)

- 為您的連線提供名稱，以及應用程式識別碼和 API 金鑰。

    ![Microsoft Flow 連線視窗](./media/automate-with-flow/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>步驟 5：指定 Analytics 查詢和圖表類型
此範例查詢會選取最後一天內的失敗要求，並將它們與作業發生的例外狀況相互關聯。 Analytics 會根據 operation_Id 識別碼使其相互關聯。 查詢接著會使用自動叢集演算法將結果分段。 

建立自己的查詢時，先確認它們可在 Analytics 中正常運作，再將其新增到您的流程中。

- 新增下列 Analytics 查詢，然後選取 HTML 表格圖表類型。 然後選取**新增步驟**。

    ```
    requests
    | where timestamp > ago(1d)
    | where success == "False"
    | project name, operation_Id
    | join ( exceptions
        | project problemId, outerMessage, operation_Id
    ) on operation_Id
    | evaluate autocluster()
    ```
    
    ![Analytics 查詢設定畫面](./media/automate-with-flow/8query.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>步驟 6：設定傳送電子郵件的流程

1. 搜尋 **Office 365 Outlook**。
2. 按一下 [Office 365 Outlook - 傳送電子郵件]。

    ![Office 365 Outlook 選取視窗](./media/automate-with-flow/9outlookaction.png)

1. 在 [傳送電子郵件]  視窗中，執行下列動作：

   a. 輸入收件者的電子郵件地址。

   b. 輸入電子郵件的主旨。

   c. 按一下 [內文] 方塊中的任意處，然後在右方開啟的動態內容功能表上，選取 [內文]。

   d. 按一下 [顯示進階選項]。

    ![Office 365 Outlook 設定](./media/automate-with-flow/10sendemailbody.png)

1. 在動態內容功能表上執行下列動作：

    a. 選取 [附件名稱]。

    b. 選取 [附件內容]。
    
    c. 在 [為 HTML] 方塊中選取 [是]。

    ![Office 365 電子郵件設定畫面](./media/automate-with-flow/11emailattachment.png)

### <a name="step-7-save-and-test-your-flow"></a>步驟 7：儲存並測試流程
- 在 **流程名稱**方塊中，新增您的流程的名稱，然後按一下**儲存**。

    ![命名流程並儲存](./media/automate-with-flow/12nameflow.png)

您可以等候觸發程序執行此動作，或[視需要執行觸發程序](https://flow.microsoft.com/blog/run-now-and-six-more-services/)來立即執行流程。

流程執行時，您在電子郵件清單中所指定的收件者會收到看起來如下的電子郵件：

![範例電子郵件](./media/automate-with-flow/flow9.png)


## <a name="next-steps"></a>後續步驟

- 深入了解建立 [Analytics 查詢](../../azure-monitor/log-query/get-started-queries.md)。
- 深入了解 [Microsoft Flow](https://ms.flow.microsoft.com)。



<!--Link references-->





