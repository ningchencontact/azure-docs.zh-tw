---
title: 使用 Microsoft Graph 安全性整合安全性作業 - Azure Logic Apps
description: 使用 Microsoft Graph 安全性和 Azure Logic Apps 管理安全性作業，改善您的應用程式威脅防護、偵測和回應功能
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 01/30/2019
tags: connectors
ms.openlocfilehash: 24963a35bc3e54b2d140bf4ed1d169b213bd9b2a
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673677"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>使用 Microsoft Graph 安全性和 Azure Logic Apps 整合安全性作業，改善威脅防護功能

使用 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 和 [Microsoft Graph 安全性](https://docs.microsoft.com/graph/security-concept-overview)連接器，您就可以建立整合 Microsoft 安全性產品、服務和合作夥伴的自動化工作流程，改善應用程式偵測、保護和回應威脅的方式。 例如，您可以建立 [Azure 資訊安全中心劇本](../security-center/security-center-playbooks.md)，用來監視和管理 Microsoft Graph 安全性實體 (例如警告)。 以下是 Microsoft Graph 安全性連接器支援的一些案例：

* 依據查詢或警示識別碼取得警示。 例如，您可以取得包含高嚴重性警示的清單。
* 更新警示。 例如，您可以更新警示指派、新增警示的註解或標記警示。
* 建立[警示訂閱 (Webhook)](https://docs.microsoft.com/graph/api/resources/webhooks) 監視警示建立或變更的時間。
* 管理您的警示訂閱。 例如，您可以取得有效的訂閱、延長訂閱的到期時間，或刪除訂閱。

您的邏輯應用程式工作流程可使用從 Microsoft Graph 安全性連接器取得回應的動作，並讓該輸出提供給工作流程中的其他動作使用。 您也可以讓您工作流程中的其他動作使用來自 Microsoft Graph 安全性連接器動作的輸出。 例如，如果您透過 Microsoft Graph 安全性連接器取得高嚴重性警示，您可以使用 Outlook 連接器將那些警示以電子郵件訊息傳送。 

若要深入了解 Microsoft Graph 安全性，請參閱 [Microsoft Graph 安全性 API 概觀](https://aka.ms/graphsecuritydocs)。 如果您不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps？](../logic-apps/logic-apps-overview.md)。 如果您正在尋找 Microsoft Flow 或 PowerApps，請參閱[什麼是 Flow？](https://flow.microsoft.com/) 或[什麼是 PowerApps？](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。 

* 若要使用 Microsoft Graph 安全性連接器，您必須擁有*明確提供*的 Azure Active Directory (AD) 租用戶系統管理員同意，這是 [Microsoft Graph 安全性驗證需求](https://aka.ms/graphsecurityauth)的一部分。 這項同意需要 Microsoft Graph 安全性連接器的應用程式識別碼和名稱，您也可以在 [Azure 入口網站](https://portal.azure.com)中找到：

   | 屬性 | 值 |
   |----------|-------|
   | **應用程式名稱** | `MicrosoftGraphSecurityConnector` |
   | **應用程式識別碼** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
   |||

   若要為連接器授與同意，您的 Azure AD 租用戶系統管理員可遵循下列任一步驟：

   * [為 Azure AD 應用程式授與租用戶系統管理員同意](../active-directory/develop/v2-permissions-and-consent.md)。

   * 在您的邏輯應用程式第一次執行時，您的應用程式可透過[應用程式同意體驗](../active-directory/develop/application-consent-experience.md)要求 Azure AD 租用戶系統管理員的同意。
   
* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

* 邏輯應用程式即為您想要存取 Microsoft Graph 安全性實體 (例如警示) 的位置。 目前，此連接器沒有任何觸發程序。 因此，若要使用 Microsoft Graph 安全性動作，請使用觸發程序啟動您的邏輯應用程式，例如「週期」觸發程序。

## <a name="connect-to-microsoft-graph-security"></a>連線至 Microsoft Graph 安全性 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登入 [Azure 入口網站](https://portal.azure.com/)，如果邏輯應用程式尚未開啟，請在邏輯應用程式設計工具中開啟邏輯應用程式。

1. 針對空白邏輯應用程式，請先新增觸發程序與您要的任何其他動作，然後再新增 Microsoft Graph 安全性動作。

   -或-

   若是現有的邏輯應用程式，請在想要新增 Microsoft Graph 安全性動作的最後一個步驟底下，選擇 [新增步驟]。

   -或-

   若要在步驟之間新增動作，將指標移至步驟之間的箭號。 
   選擇所顯示的加號 (+)，然後選取 [新增動作]。

1. 在搜尋方塊中，輸入「Microsoft Graph 安全性」作為篩選條件。 從 [動作] 清單中，選取您想要的動作。

1. 使用您的 Microsoft Graph 安全性認證登入。

1. 為您選取的動作提供必要的詳細資料，並且繼續建置邏輯應用程式的工作流程。

## <a name="add-actions"></a>新增動作

以下是有關使用 Microsoft Graph 安全性連接器提供之各種動作的具體詳細資料。

### <a name="manage-alerts"></a>管理警示

若要篩選、排序或取得最近結果，只要提供 [Microsoft Graph 支援的 ODATA 查詢參數](https://docs.microsoft.com/graph/query-parameters)。 「請勿指定」完整的基底 URL 或 HTTP 動作，例如 `https://graph.microsoft.com/v1.0/security/alerts`，或是 `GET` 或 `PATCH` 作業。 以下具體範例顯示當您要列出高嚴重性警示時，適用於**取得警示**動作的參數：

`Filter alerts value as Severity eq 'high'`

如需您可搭配此連接器使用之查詢的詳細資訊，請參閱 [Microsoft Graph 安全性警示參考文件](https://docs.microsoft.com/graph/api/alert-list) \(英文\)。 若要建置含有此連接器的增強體驗，請深入了解連接器支援的[結構描述屬性警示](https://docs.microsoft.com/graph/api/resources/alert)。

|  動作 | 描述 |
|--------|-------------|
| **取得警示** | 取得依據一或多個[警示屬性](https://docs.microsoft.com/graph/api/resources/alert)篩選的警示，例如： <p>`Provider eq 'Azure Security Center' or 'Palo Alto Networks'` | 
| **依識別碼取得警示** | 依據警示識別碼取得特定警示。 | 
| **更新警示** | 依據警示識別碼更新特定警示。 <p>為確保您在要求中傳遞必要和可編輯的屬性，請參閱[警示的可編輯屬性](https://docs.microsoft.com/graph/api/alert-update) \(英文\)。 例如，若要指派警示給安全性分析師以讓他們可以進行調查，您可以更新警示的「指派給」屬性。 |
|||

### <a name="manage-alert-subscriptions"></a>管理警示訂閱

Microsoft Graph 支援[*訂閱*](https://docs.microsoft.com/graph/api/resources/subscription)或 [*Webhook*](https://docs.microsoft.com/graph/api/resources/webhooks)。 若要取得、更新或刪除訂閱，請將 [Microsoft Graph 支援的 ODATA 查詢參數](https://docs.microsoft.com/graph/query-parameters)提供給 Microsoft Graph 實體，並在 ODATA 查詢後包含 `security/alerts`。 
「請勿包含」基底 URL，例如：`https://graph.microsoft.com/v1.0`。 相反地，請使用此範例的格式：

`security/alerts?$filter=status eq 'New'`

|  動作 | 描述 |
|--------|-------------|
| **建立訂用帳戶** | [建立訂閱](https://docs.microsoft.com/graph/api/subscription-post-subscriptions)可通知您相關的任何變更。 您可以針對您想要的特定警示類型篩選此訂閱。 例如，您可以建立通知您有關高嚴重性警示的訂閱。 |
| **取得有效的訂閱** | [取得未到期的訂閱](https://docs.microsoft.com/graph/api/subscription-list)。 | 
| **更新訂閱** | 提供訂閱識別碼以[更新訂閱](https://docs.microsoft.com/graph/api/subscription-update)。 例如，若要延長訂閱，您可以更新訂閱的 `expirationDateTime` 屬性。 | 
| **刪除訂閱** | 提供訂閱識別碼以[刪除訂閱](https://docs.microsoft.com/graph/api/subscription-delete)。 | 
||| 

## <a name="connector-reference"></a>連接器參考

如需觸發程序、動作和限制的技術詳細資訊，它們是由連接器的 OpenAPI (以前稱為 Swagger) 來描述，請檢閱連接器的[參考頁面](https://aka.ms/graphsecurityconnectorreference)。

## <a name="get-support"></a>取得支援

如有問題，請瀏覽 [Azure Logic Apps 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
若要提交或票選功能構想，請造訪 [Logic Apps 使用者意見反應網站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>後續步驟

了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
