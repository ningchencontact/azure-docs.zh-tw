---
title: 將成品新增至整合服務環境-Azure Logic Apps
description: 將邏輯應用程式、整合帳戶和自訂連接器新增至您的整合服務環境（ISE），以存取 Azure 虛擬網路（Vnet）
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 266c942adb62b187d39d2eac4a47455482bfadaa
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680518"
---
# <a name="add-artifacts-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>在 Azure Logic Apps 中將成品新增至您的整合服務環境（ISE）

在您建立[整合服務環境（ISE）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)之後，請新增邏輯應用程式、整合帳戶和連接器等成品，使其可以存取您 Azure 虛擬網路中的資源。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 您建立用來執行邏輯應用程式的 ISE。 如果您沒有 ISE，請[先建立 ise](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)。

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>建立邏輯應用程式

若要建立在整合服務環境（ISE）中執行的邏輯應用程式，請遵循下列步驟：

1. 尋找並開啟您的 ISE （如果尚未開啟）。 從 ISE 功能表的 **設定** 底下，選取 **邏輯應用程式**  > **新增**。

   ![將新的邏輯應用程式新增至 ISE](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

   -或-

   在主要 Azure 功能表中，選取 [建立資源] > [整合] > [邏輯應用程式]。

1. 提供用於邏輯應用程式的名稱、Azure 訂用帳戶和 Azure 資源群組（新的或現有的）。

1. 從 [**位置**] 清單的 [**整合服務環境**] 區段底下，選取您的 ISE，例如：

   ![選取整合服務環境](./media/add-artifacts-integration-service-environment-ise/create-logic-app-with-integration-service-environment.png)

   > [!IMPORTANT]
   > 如果您想要將邏輯應用程式與整合帳戶搭配使用，則這些邏輯應用程式和整合帳戶必須使用相同的 ISE。

1. 繼續[以一般方式建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

   如需觸發程式和動作的工作方式，以及當您使用 ISE 與全域 Logic Apps 服務時的標記方式差異，請參閱[ISE 總覽中的隔離與全域](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)。

1. 若要管理 ISE 中的邏輯應用程式和 API 連線，請參閱[管理您的整合服務環境](../logic-apps/ise-manage-integration-service-environment.md)。

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>建立整合帳戶

根據在建立時選取的[ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) ，您的 ISE 會包含特定的整合帳戶使用方式，而不需額外費用。 存在於整合服務環境（ISE）中的邏輯應用程式，只能參考存在於相同 ISE 中的整合帳戶。 因此，若要讓整合帳戶在 ISE 中使用邏輯應用程式，整合帳戶和邏輯應用程式都必須使用與其位置*相同的環境*。 如需整合帳戶和 Ise 的詳細資訊，請參閱[整合帳戶與 ISE](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment)。

若要建立使用 ISE 的整合帳戶，請遵循下列步驟：

1. 尋找並開啟您的 ISE （如果尚未開啟）。 從 ISE 功能表的 **設定** 底下，選取 **整合帳戶**  > **新增**。

   ![將新的整合帳戶新增至 ISE](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

   -或-

   從主要 Azure 功能表中，選取 [**建立資源**] [ > **整合**] [ > **整合帳戶**]。

1. 提供您整合帳戶的名稱、Azure 訂用帳戶、Azure 資源群組（新的或現有的）和定價層。

1. 從 [**位置**] 清單的 [**整合服務環境**] 區段底下，選取您的邏輯應用程式所使用的相同 ISE，例如：

   ![選取整合服務環境](./media/add-artifacts-integration-service-environment-ise/create-integration-account-with-integration-service-environment.png)

1. [以一般方式將邏輯應用程式連結到您的整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)。

1. 繼續將成品新增至您的整合帳戶，例如[交易夥伴](../logic-apps/logic-apps-enterprise-integration-partners.md)和[協定](../logic-apps/logic-apps-enterprise-integration-agreements.md)。

1. 若要管理 ISE 中的整合帳戶，請參閱[管理您的整合服務環境](../logic-apps/ise-manage-integration-service-environment.md)。

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>新增 ISE 連接器

您可以新增可在 ISE 中使用但不會部署在 ISE 中的 Microsoft 管理的連接器。

1. 在您的 ISE 功能表的 [**設定**] 底下，選取 [**受控連接器**]。 在工具列上，選取 [新增]。

   ![查看受管理的連接器](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. 在 [新增**受管理的連接器**] 窗格中，開啟 [**尋找連接器**] 清單。 如果您想要的連接器可供使用，請選取該連接器，然後選取 [**建立**]。

   此清單只會顯示符合資格但未部署在 ISE 中的連接器。 您的 ISE 中已部署的連接器似乎無法選取。

   ![選取合格連接器](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>建立自訂連接器

若要在 ISE 中使用自訂連接器，請直接在 ISE 內建立這些自訂連接器。

1. 尋找並開啟您的 ISE （如果尚未開啟）。 從 ISE 功能表的 **設定** 底下，選取 **自訂連接器**  > **新增**。

   ![建立自訂連接器](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. 提供名稱、Azure 訂用帳戶和 Azure 資源群組（新的或現有的），以用於您的自訂連接器。

1. 從 [**位置**] 清單的 [**整合服務環境**] 區段底下，選取您的邏輯應用程式所使用的相同 ISE，然後選取 [**建立**]，例如：

   ![選取整合服務環境](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-with-integration-service-environment.png)

1. 選取新的自訂連接器，然後選取 [**編輯**]，例如：

   ![選取並編輯自訂連接器](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. 繼續以一般的方式從[OpenAPI 定義](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition)或[SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector)建立連接器。

1. 若要管理 ISE 中的自訂連接器，請參閱[管理您的整合服務環境](../logic-apps/ise-manage-integration-service-environment.md)。

## <a name="next-steps"></a>後續步驟

* [管理整合服務環境](../logic-apps/ise-manage-integration-service-environment.md)
