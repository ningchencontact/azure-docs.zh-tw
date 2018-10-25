---
title: Azure 資料表 | Microsoft Docs
description: 針對 Azure 資料表設定潛在客戶管理。
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
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 60e3e3d81b07bf7ae681b5cef2d6d9681877a35f
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2018
ms.locfileid: "48806672"
---
<a name="lead-management-instructions-for-azure-table"></a>運用 Azure 資料表進行潛在客戶管理之說明
============================================

此文章說明如何設定 Azure 資料表以儲存業務潛在客戶。 Azure 資料表能讓您儲存並自訂客戶資訊。

## <a name="to-configure-azure-table"></a>設定 Azure 資料表

1.  如果您沒有 Azure 帳戶，您可以[建立免費的試用帳戶](https://azure.microsoft.com/pricing/free-trial/)。

2.  在您的 Azure 帳戶啟用之後，請登入 [Azure 入口網站](https://portal.azure.com)。
3.  在 Azure 入口網站中，建立儲存體帳戶。 下一個螢幕擷取畫面會顯示如何建立儲存體帳戶。 如需儲存體定價的詳細資訊，請參閱[儲存體定價](https://azure.microsoft.com/pricing/details/storage/)。

    ![建立 Azure 儲存體帳戶的步驟](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

4.  複製金鑰的儲存體帳戶連接字串，並將它貼到 Cloud Partner 入口網站上的 [儲存體帳戶連接字串] 欄位。 以下是連接字串的範例：`DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net `
    
    ![Azure 儲存體金鑰](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

您可以使用 [Azure 儲存體總管](http://azurestorageexplorer.codeplex.com/) \(英文\) 或任何其他工具，來檢視儲存體資料表中的資料。 您也可以將 Azure 資料表中的資料匯出。
data.

## <a name="optional-to-use-azure-functions-with-an-azure-table"></a>**(選擇性)** 搭配 Azure 資料表使用 Azure Functions

如果您想要自訂自己接收潛在客戶的方式，請搭配 Azure 資料表使用 [Azure Functions](https://azure.microsoft.com/services/functions/)。 Azure Functions 服務能讓您將產生潛在客戶的程序自動化。

下列步驟示範如何建立使用計時器的 Azure 函式。 該函式每隔五分鐘便會查看 Azure 資料表中是否有新的記錄，並會在有新記錄的情況下使用 SendGrid 服務傳送電子郵件通知。


1.  在您的 Azure 訂閱中[建立](https://portal.azure.com/#create/SendGrid.SendGrid)免費的 SendGrid 服務帳戶。

    ![建立 SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/createsendgrid.png)

2.  建立 SendGrid API 金鑰 
    - 選取 [管理] 以移至 SendGrid UI
    - 依序選取 [設定] 和 [API 金鑰]，然後建立具有 [郵件傳送] -\> [完整存取] 的金鑰
    - 儲存該 API 金鑰


    ![SendGrid API 金鑰](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridkey.png)


3.  使用名為 [使用情況方案] 的 [主控方案] 選項，[建立](https://portal.azure.com/#create/Microsoft.FunctionApp) Azure 函式應用程式。

    ![建立 Azure 函式應用程式](./media/cloud-partner-portal-lead-management-instructions-azure-table/createfunction.png)


4.  建立新的函式定義。

    ![建立 Azure 函式定義](./media/cloud-partner-portal-lead-management-instructions-azure-table/createdefinition.png)
 

5.  若要讓函式在特定時間傳送更新，請選取 [TimerTrigger-CSharp] 作為起始選項。

     ![Azure 函式時間觸發程序選項](./media/cloud-partner-portal-lead-management-instructions-azure-table/timetrigger.png)


6.  以下列程式碼範例取代 [開發] 程式碼。 以您想要作為寄件者和接收者使用的地址來編輯電子郵件地址。

        #r "Microsoft.WindowsAzure.Storage"
        #r "SendGrid"
        using Microsoft.WindowsAzure.Storage.Table;
        using System;
        using SendGrid;
        using SendGrid.Helpers.Mail;
        public class MyRow : TableEntity
        {
            public string Name { get; set; }
        }
        public static void Run(TimerInfo myTimer, IQueryable<MyRow> inputTable, out Mail message, TraceWriter log)
        {
            // UTC datetime that is 5.5 minutes ago while the cron timer schedule is every 5 minutes
            DateTime dateFrom = DateTime.UtcNow.AddSeconds(-(5 * 60 + 30));
            var emailFrom = "YOUR EMAIL";
            var emailTo = "YOUR EMAIL";
            var emailSubject = "Azure Table Notification";
            // Look in the table for rows that were added recently
            var rowsList = inputTable.Where(r => r.Timestamp > dateFrom).ToList();
            // Check how many rows were added
            int rowsCount = rowsList.Count;
            if (rowsCount > 0)
            {
                log.Info($"Found {rowsCount} rows added since {dateFrom} UTC");
                // Configure the email message describing how many rows were added
                message = new Mail
                {
                    From = new Email(emailFrom),
                    Subject = emailSubject + " (" + rowsCount + " new rows)"
                };
                var personalization = new Personalization();
                personalization.AddTo(new Email(emailTo));
                message.AddPersonalization(personalization);
                var content = new Content
                {
                    Type = "text/plain",
                    Value = "Found " + rowsCount + " new rows added since " + dateFrom.ToString("yyyy-MM-dd HH:mm:ss") + " UTC"
                };
                message.AddContent(content);
            }
            else
            {
                // Do not send the email if no new rows were found
                message = null;
            }
        }

    ![Azure 函式程式碼片段](./media/cloud-partner-portal-lead-management-instructions-azure-table/code.png)


7.  選取 [整合] 和 [輸入] 來定義 Azure 資料表連線。

    ![Azure 函式整合](./media/cloud-partner-portal-lead-management-instructions-azure-table/integrate.png)


8.  選取 [新增] 來輸入資料表名稱並定義連接字串。


    ![Azure 函式資料表連接](./media/cloud-partner-portal-lead-management-instructions-azure-table/configtable.png)

9.  現在將輸出定義為 SendGrid，並保留所有預設值。

    ![SendGrid 輸出](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutput.png)

    ![SendGrid 輸出預設值](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutputdefaults.png)

10. 使用 "SendGridApiKey" 這個名稱，以及從 SendGrid UI 中的 [API 金鑰] 取得的值，將 SendGrid API 金鑰新增至 [函式應用程式設定]

    ![SendGrid [管理]](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanage.png)
    ![SendGrid 管理金鑰](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanagekey.png)

完成設定函式之後，[整合] 區段中的程式碼看起來應該會和下列範例類似。

    {
      "bindings": [
        {
          "name": "myTimer",
          "type": "timerTrigger",
          "direction": "in",
          "schedule": "0 */5 * * * *"
        },
        {
          "type": "table",
          "name": "inputTable",
          "tableName": "MarketplaceLeads",
          "take": 50,
          "connection": "yourstorageaccount_STORAGE",
          "direction": "in"
        },
        {
          "type": "sendGrid",
          "name": "message",
          "apiKey": "SendGridApiKey",
          "direction": "out"
        }
      ],
      "disabled": false
    }

11. 最後的步驟是瀏覽至函式的 [開發] UI，然後選取 [執行] 以啟動計時器。 現在，每當有新的潛在客戶時，您便會收到通知。
