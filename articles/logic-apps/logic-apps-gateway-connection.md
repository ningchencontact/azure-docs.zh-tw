---
title: 從 Azure Logic Apps 存取內部部署資料來源
description: 藉由建立內部部署資料閘道，從邏輯應用程式連線至內部部署資料來源
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: ed2ba70b803940700044e900a1b2bb6607c0f051
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934055"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>從 Azure Logic Apps 連線到內部部署資料來源

若要從您的邏輯應用程式存取內部部署資料來源，請在 Azure 入口網站中建立內部部署資料閘道資源。 然後，您的邏輯應用程式可以使用[內部部署連接器](../connectors/apis-list.md#on-premises-connectors)。 本文說明如何在您于[本機電腦上下載並安裝閘道](../logic-apps/logic-apps-gateway-install.md)*之後*，建立 Azure 閘道資源。 如需閘道的詳細資訊，請參閱[閘道的運作方式](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service)。

> [!TIP]
> 若要連線至 Azure 虛擬網路，請考慮建立[*整合服務環境*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。 

如需如何使用閘道與其他服務的資訊，請參閱下列文章：

* [Microsoft Power BI 內部部署資料閘道](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft Flow 內部部署資料閘道](https://flow.microsoft.com/documentation/gateway-manage/)
* [Microsoft PowerApps 內部部署資料閘道](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Azure Analysis Services 內部部署資料閘道](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>支援的資料來源

針對 Azure Logic Apps，內部部署資料閘道支援這些資料來源的內部[部署連接器](../connectors/apis-list.md#on-premises-connectors)：

* BizTalk Server 2016
* 檔案系統
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle Database
* PostgreSQL
* SAP
* SharePoint Server
* [SQL Server]
* Teradata

雖然閘道本身不會產生額外成本，但[Logic Apps 定價模式](../logic-apps/logic-apps-pricing.md)適用于 Azure Logic Apps 中的這些連接器和其他作業。

## <a name="prerequisites"></a>必要條件

* 您已在[本機電腦上安裝內部部署資料閘道](../logic-apps/logic-apps-gateway-install.md)。

* 您擁有安裝內部部署資料閘道時所使用的[相同 azure 帳戶和 azure 訂用帳戶](../logic-apps/logic-apps-gateway-install.md#requirements)。

* 您先前尚未將閘道安裝連結至 Azure 中的另一個閘道資源。

  當您建立閘道資源時，請選取要與閘道資源建立關聯的閘道安裝。 當您建立閘道資源時，無法選取已連結的閘道安裝。

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>建立 Azure 閘道資源

在本機電腦上安裝閘道之後，請為您的閘道建立 Azure 資源。 

1. 使用與用來安裝閘道相同的 Azure 帳戶登入[Azure 入口網站](https://portal.azure.com)。

1. 在 [Azure 入口網站搜尋] 方塊中，輸入「內部部署資料閘道」，然後選取 [內部**部署資料閘道**]。

   ![尋找 [內部部署資料閘道]](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

1. 在 [**內部部署資料閘道**] 底下，選取 [**新增**]。

   ![新增資料閘道](./media/logic-apps-gateway-connection/add-gateway.png)

1. 在 [**建立連線閘道**] 底下，為您的閘道資源提供此資訊。 當您完成時，選取 [建立]。

   | 屬性 | 描述 |
   |----------|-------------|
   | **資源名稱** | 您的閘道資源名稱，其中只能包含字母、數位、連`-`字元（）、`_`底線（）、`(`括弧`)`（，）和句點`.`（）。 |
   | **訂用帳戶** | 您的 Azure 訂用帳戶必須與閘道安裝和邏輯應用程式相同。 預設的訂用帳戶會由您用來登入的 Azure 帳戶來決定。 |
   | **資源群組** | 您想要使用的[Azure 資源群組](../azure-resource-manager/resource-group-overview.md) |
   | **位置** | [閘道安裝](../logic-apps/logic-apps-gateway-install.md)期間為閘道雲端服務所選取的位置所在的相同區域。 否則，您的閘道安裝將不會出現在 [**安裝名稱**] 清單中供您選取。 您的邏輯應用程式位置可能與閘道資源位置不同。 |
   | **安裝名稱** | 如果您的閘道安裝還不是選取狀態，請選取您先前安裝的閘道。 先前連結的閘道安裝不會出現在此清單中供您選取。 |
   |||

   請看以下範例：

   ![提供詳細資料以建立內部部署資料閘道](./media/logic-apps-gateway-connection/gateway-details.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>連線至內部部署資料

在您建立閘道資源並且讓 Azure 訂用帳戶與此資源相關聯之後，即可使用閘道，建立邏輯應用程式與內部部署資料來源之間的連線。

1. 在 Azure 入口網站中，於邏輯應用程式設計工具中建立或開啟邏輯應用程式。

1. 新增可支援內部部署連線的連接器，例如 **SQL Server**。

1. 選取 [透過內部部署資料閘道連線]。 

1. 針對 [**閘道**]，請選取您建立的閘道資源。

   > [!NOTE]
   > 閘道清單包含其他區域中的閘道資源，因為您的邏輯應用程式位置可能與閘道資源的位置不同。

1. 提供唯一的連線名稱和其他必要資訊，這取決於您想要建立的連接。

   唯一的連線名稱可協助您稍後輕鬆地找到該連接，特別是當您建立多個連接時。 如果情況允許，也請在使用者名稱中包含完整網域。
   
   請看以下範例：

   ![建立邏輯應用程式與資料閘道之間的連線](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. 當您完成時，選取 [建立]。 

您的閘道連線現已可供邏輯應用程式使用。

## <a name="edit-connection"></a>編輯連線

若要更新閘道連線的設定，您可以編輯您的連線。

1. 若只要尋找邏輯應用程式的所有 API 連線，請在邏輯應用程式功能表的 [**開發工具**] 底下，選取 [ **API**連線]。
   
   ![在邏輯應用程式功能表上，選取 [API 連線]](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

1. 選取您想要的閘道連線，然後選取 [**編輯 API**連線]。

   > [!TIP]
   > 如果您的更新不會生效，請嘗試[停止並重新啟動閘道 Windows 服務帳戶](../logic-apps/logic-apps-gateway-install.md#restart-gateway)以進行閘道安裝。

若要尋找與您的 Azure 訂用帳戶相關聯的所有 API 連線： 

* 從主要 Azure 功能表，移至 [所有資源] > [Web] > [API 連線]。
* 或者，從主要 Azure 功能表，移至 [所有資源]。 將 [**類型**] 篩選準則設定為 [ **API**連線]。

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>刪除閘道資源

若要建立不同的閘道資源，請將您的閘道安裝連結至不同的閘道資源，或移除閘道資源，您可以刪除閘道資源，而不會影響閘道安裝。 

1. 從主要 Azure 功能表中，選取 [**所有資源**]。 尋找並選取您的閘道資源。

1. 如果尚未選取，請在您的閘道資源功能表上選取 [內部部署資料閘道]。 在 [閘道資源] 工具列上，選取 [**刪除**]。

   例如:

   ![刪除閘道](./media/logic-apps-gateway-connection/gateway-delete.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>常見問題集

**問**：為何在 Azure 中建立閘道資源時看不到我的閘道安裝？ <br/>
**答**：發生此問題的原因如下：

* 您的閘道安裝已經由 Azure 中的其他閘道資源加以註冊及宣告。 建立閘道資源之後，閘道安裝就不會出現在執行個體清單中。 若要在 Azure 入口網站中檢查您的閘道註冊，請使用 [內部部署資料閘道] 類型，檢閱「所有」 Azure 訂用帳戶的所有 Azure 資源。

* 安裝閘道的人員的 Azure AD 身分識別與登入 Azure 入口網站的人員不同。 請檢查您已使用安裝閘道的相同身分識別進行登入。

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>後續步驟

* [保護邏輯應用程式](./logic-apps-securing-a-logic-app.md)
* [Logic Apps 範例和常見案例](./logic-apps-examples-and-scenarios.md)
