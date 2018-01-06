---
title: "外部資料表繫結，如 Azure 函式 （實驗）"
description: "使用 Azure Functions 中的外部資料表繫結"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: alkarche
ms.openlocfilehash: 8a4358fa67e45d0b7a2df1519d649099b5ef5850
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2018
---
# <a name="external-table-binding-for-azure-functions-experimental"></a>外部資料表繫結，如 Azure 函式 （實驗）

本文說明如何使用 SaaS 提供者，例如 Sharepoint 和 Dynamics，在 Azure 函式上的表格式資料。 Azure 函數支援輸入和輸出的外部資料表繫結。

> [!IMPORTANT]
> 外部資料表繫結屬實驗性質，並可能永遠無法抵達通常可用 (GA) 狀態。 它包含只在 Azure 中的函式 1.x 和沒有計劃將其新增至 Azure 函式 2.x。 需要 SaaS 提供者中的資料存取的情況下，請考慮使用[函式呼叫的 logic apps](functions-twitter-email.md)。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="api-connections"></a>應用程式開發介面的連線

資料表繫結利用外部的應用程式開發介面連接向協力廠商 SaaS 提供者。 

指派的繫結時您可以建立新的應用程式開發介面連接或使用現有的應用程式開發介面連接相同的資源群組中。

### <a name="available-api-connections-tables"></a>可用的 API 連線 （資料表）

|連接器|觸發程序|輸入|輸出|
|:-----|:---:|:---:|:---:|
|[DB2](https://docs.microsoft.com/azure/connectors/connectors-create-api-db2)||x|x
|[Dynamics 365 for Operations (英文)](https://ax.help.dynamics.com/wiki/install-and-configure-dynamics-365-for-operations-warehousing/)||x|x
|[Dynamics 365](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[Dynamics NAV (英文)](https://msdn.microsoft.com/library/gg481835.aspx)||x|x
|[Google 試算表](https://docs.microsoft.com/azure/connectors/connectors-create-api-googledrive)||x|x
|[Informix](https://docs.microsoft.com/azure/connectors/connectors-create-api-informix)||x|x
|[Dynamics 365 for Financials](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[MySQL](https://docs.microsoft.com/azure/store-php-create-mysql-database)||x|x
|[Oracle Database](https://docs.microsoft.com/azure/connectors/connectors-create-api-oracledatabase)||x|x
|[Common Data Service (英文)](https://docs.microsoft.com/common-data-service/entity-reference/introduction)||x|x
|[Salesforce](https://docs.microsoft.com/azure/connectors/connectors-create-api-salesforce)||x|x
|[SharePoint](https://docs.microsoft.com/azure/connectors/connectors-create-api-sharepointonline)||x|x
|[SQL Server](https://docs.microsoft.com/azure/connectors/connectors-create-api-sqlazure)||x|x
|[Teradata](http://www.teradata.com/products-and-services/azure/products/)||x|x
|UserVoice||x|x
|Zendesk||x|x

> [!NOTE]
> 資料表的外部連接也可用在[Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list)。

## <a name="creating-an-api-connection-step-by-step"></a>建立 API 連線：逐步解說

1. 在您的函式應用程式 Azure 入口網站頁面上，選取加號 (**+**) 建立函式。

1. 在**案例**方塊中，選取**實驗**。

1. 選取**External table**。

1. 選取的語言。

2. 在下**外部資料表連接**，選取現有的連接，或選取**新**。

1. 新的連接設定的設定，然後選取**授權**。

1. 選取**建立**建立函式。

1. 選取**整合 > External Table**。

1. 設定連線以使用目標資料表。 這些設定之間 SaaS 提供者而異。 範例會包含在下一節。

## <a name="example"></a>範例

此範例會連線至名為 "Contact"，並具有 ID, LastName 及 FirstName 資料行的資料表。 程式碼會在資料表中列出 Contact 實體，並記錄名字和姓氏。

以下是 *function.json* 檔案：

```json
{
  "bindings": [
    {
      "type": "manualTrigger",
      "direction": "in",
      "name": "input"
    },
    {
      "type": "apiHubTable",
      "direction": "in",
      "name": "table",
      "connection": "ConnectionAppSettingsKey",
      "dataSetName": "default",
      "tableName": "Contact",
      "entityId": "",
    }
  ],
  "disabled": false
}
```

以下是 C# 指令碼程式碼：

```cs
#r "Microsoft.Azure.ApiHub.Sdk"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.ApiHub;

//Variable name must match column type
//Variable type is dynamically bound to the incoming data
public class Contact
{
    public string Id { get; set; }
    public string LastName { get; set; }
    public string FirstName { get; set; }
}

public static async Task Run(string input, ITable<Contact> table, TraceWriter log)
{
    //Iterate over every value in the source table
    ContinuationToken continuationToken = null;
    do
    {   
        //retrieve table values
        var contactsSegment = await table.ListEntitiesAsync(
            continuationToken: continuationToken);

        foreach (var contact in contactsSegment.Items)
        {   
            log.Info(string.Format("{0} {1}", contact.FirstName, contact.LastName));
        }

        continuationToken = contactsSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

### <a name="sql-server-data-source"></a>SQL Server 資料來源

若要建立資料表以搭配此範例中的 SQL Server 中，以下是指令碼。 `dataSetName`為"default"。

```sql
CREATE TABLE Contact
(
    Id int NOT NULL,
    LastName varchar(20) NOT NULL,
    FirstName varchar(20) NOT NULL,
    CONSTRAINT PK_Contact_Id PRIMARY KEY (Id)
)
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (1, 'Bitt', 'Prad') 
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (2, 'Glooney', 'Ceorge') 
GO
```

### <a name="google-sheets-data-source"></a>Google 工作表的資料來源

若要建立用於 Google 的文件中的這個範例的資料表，建立名為工作表的試算表`Contact`。 連接器不能使用試算表顯示名稱。 內部名稱 (以粗體顯示) 必須做為 dataSetName 使用，例如：`docs.google.com/spreadsheets/d/`**`1UIz545JF_cx6Chm_5HpSPVOenU4DZh4bDxbFgJOSMz0`** 將資料行名稱 `Id`, `LastName`, `FirstName` 新增至第一個資料列，然後將資料填入後續資料列。

### <a name="salesforce"></a>Salesforce

若要使用此範例與 Salesforce，`dataSetName`為"default"。

## <a name="configuration"></a>組態

下表說明您在 function.json 檔案中設定的繫結設定屬性。

|function.json 屬性 | 說明|
|---------|----------------------|
|**type** | 必須設為 `apiHubTable`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。|
|**direction** | 必須設為 `in`。 當您在 Azure 入口網站中建立觸發程序時，會自動設定此屬性。 |
|**name** | 代表函式程式碼中事件項目的變數名稱。 | 
|**連接**| 識別儲存在應用程式開發介面的連接字串的應用程式設定。 應用程式設定會在您於整合 UI 中新增 API 連線時自動建立。|
|**dataSetName**|包含要讀取之資料表的資料集的名稱。|
|**tableName**|資料表的名稱|
|**entityId**|必須是空的資料表繫結。

表格式連接器會提供資料集，每個資料集皆包含資料表。 預設資料集的名稱為 "default"。 以下列出各種 SaaS 提供者的資料集和資料表標題：

|連接器|Dataset|資料表|
|:-----|:---|:---| 
|**SharePoint**|網站|SharePoint 清單
|**SQL**|資料庫|資料表 
|**Google 試算表**|試算表|工作表 
|**Excel**|Excel 檔案|工作表 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [深入了解 Azure Functions 觸發程序和繫結](functions-triggers-bindings.md)
