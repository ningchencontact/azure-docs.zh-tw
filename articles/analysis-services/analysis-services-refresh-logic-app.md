---
title: Azure Analysis Services 模型的重新整理與 Logic Apps |Microsoft Docs
description: 了解如何使用 Azure Logic Apps 中撰寫非同步重新整理。
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: 6ffce339fe7b1a434c8f007b417ee81a42529dfc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66142349"
---
# <a name="refresh-with-logic-apps"></a>使用 Logic Apps 重新整理

藉由使用邏輯應用程式與 REST 呼叫，您可以執行自動的資料重新整理作業，對您 Azure 分析表格式模型，包括同步處理的向外延展查詢的唯讀複本。

若要深入了解使用 Azure Analysis Services 的 REST Api，請參閱[非同步重新整理使用 REST API](analysis-services-async-refresh.md)。

## <a name="authentication"></a>Authentication

所有呼叫必須使用有效的 Azure Active Directory (OAuth 2) 權杖進行都驗證。  這篇文章中的範例會使用服務主體 (SPN)，向 Azure Analysis Services。 若要進一步了解，請參閱[使用 Azure 入口網站建立服務主體](../active-directory/develop/howto-create-service-principal-portal.md)。

## <a name="design-the-logic-app"></a>設計邏輯應用程式

> [!IMPORTANT]
> 下列範例假設 Azure Analysis Services 防火牆已停用。  如果已啟用防火牆，要求起始端的公用 IP 位址必須是在 Azure Analysis Services 防火牆的白名單。 若要深入了解每個區域的邏輯應用程式的 IP 範圍，請參閱[限制和 Azure Logic Apps 的組態資訊](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses)。

### <a name="prerequisites"></a>必要條件

#### <a name="create-a-service-principal-spn"></a>建立服務主體 (SPN)

若要了解如何建立服務主體，請參閱[使用 Azure 入口網站建立服務主體](../active-directory/develop/howto-create-service-principal-portal.md)。

#### <a name="configure-permissions-in-azure-analysis-services"></a>Azure Analysis Services 中設定權限
 
您建立服務主體必須具有伺服器系統管理員權限，在伺服器上。 若要深入了解，請參閱[將服務主體新增至伺服器管理員角色](analysis-services-addservprinc-admins.md)。

### <a name="configure-the-logic-app"></a>設定邏輯應用程式

在此範例中，邏輯應用程式被設計來觸發時收到 HTTP 要求。 這可讓協調流程工具，例如 Azure Data Factory，以觸發 Azure Analysis Services 模型重新整理。

當您建立邏輯應用程式：

1. 在邏輯應用程式設計工具中，選擇 第一個動作**HTTP 要求時收到**。

   ![新增接收的 HTTP 活動](./media/analysis-services-async-refresh-logic-app/1.png)

儲存邏輯應用程式之後，將會以 HTTP POST URL 產生此步驟。

2. 加入新的步驟，並搜尋**HTTP**。  

   ![新增 HTTP 活動](./media/analysis-services-async-refresh-logic-app/9.png)

   ![新增 HTTP 活動](./media/analysis-services-async-refresh-logic-app/10.png)

3. 選取  **HTTP**新增此動作。

   ![新增 HTTP 活動](./media/analysis-services-async-refresh-logic-app/2.png)

設定 HTTP 活動，如下所示：

|屬性  |Value  |
|---------|---------|
|**方法**     |POST         |
|**URI**     | https://*伺服器地區*/servers/*aas 伺服器名稱*/models/*您的資料庫名稱*/ <br /> <br /> 例如： https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/|
|**標頭**     |   內容類型、 application/json <br /> <br />  ![標頭](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**內文**     |   若要深入了解的要求主體的形成，請參閱[非同步重新整理使用 REST API-POST /refreshes](analysis-services-async-refresh.md#post-refreshes)。 |
|**驗證**     |Active Directory OAuth         |
|**租用戶**     |填寫您 Azure Active Directory 租用戶識別碼         |
|**目標對象**     |https://*.asazure.windows.net         |
|**用戶端識別碼**     |輸入您的服務主體名稱 ClientID         |
|**認證類型**     |祕密         |
|**祕密**     |輸入您的服務主體名稱祕密         |

範例：

![已完成的 HTTP 活動](./media/analysis-services-async-refresh-logic-app/7.png)

現在測試邏輯應用程式。  在 邏輯應用程式設計工具中，按一下**執行**。

![測試邏輯應用程式](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>使用邏輯應用程式使用 Azure Data Factory

一旦儲存邏輯應用程式時，檢閱**HTTP 要求時收到**活動，然後複製**HTTP POST URL** ，現在就會產生。  這是可供 Azure Data Factory 來進行非同步呼叫以觸發邏輯應用程式的 URL。

以下是範例 Azure Data Factory Web 活動執行此動作。

![Data Factory Web 活動](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>使用獨立的邏輯應用程式

如果您不打算使用 Data Factory 等的協調流程工具，以觸發模型重新整理，您可以設定邏輯應用程式會觸發重新整理排程為基礎。

使用上述範例中，刪除第一個活動，並將它取代為**排程**活動。

![排程活動](./media/analysis-services-async-refresh-logic-app/12.png)

![排程活動](./media/analysis-services-async-refresh-logic-app/13.png)

此範例會使用**循環**。

一旦新增活動之後，設定的間隔和頻率，然後加入新的參數並選擇**在這幾小時內**。

![排程活動](./media/analysis-services-async-refresh-logic-app/16.png)

選取想的小時。

![排程活動](./media/analysis-services-async-refresh-logic-app/15.png)

儲存邏輯應用程式。

## <a name="next-steps"></a>後續步驟

[範例](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)
