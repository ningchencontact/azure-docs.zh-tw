---
title: 建立和管理交易夥伴協議-Azure Logic Apps
description: 建立和管理 Azure Logic Apps 與企業整合套件的使用中交易夥伴之間的協議
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 447ffb8e-3e91-4403-872b-2f496495899d
ms.date: 04/05/2019
ms.openlocfilehash: 26d653b873e959f0804e0456ed87ee68c39413e5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64720684"
---
# <a name="create-and-manage-trading-partner-agreements-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>建立和使用 Azure Logic Apps 與企業整合套件來管理交易夥伴協議

A[交易夥伴](../logic-apps/logic-apps-enterprise-integration-partners.md) 
*協議*可協助組織和企業，順暢地彼此通訊所定義的交換時所要使用特定的業界標準通訊協定企業對企業 (B2B) 訊息。 合約會提供常見的優點，例如：

* 讓組織能夠利用已知格式交換資訊。
* 進行 B2B 交易時，請提高效率。
* 可輕鬆地建立、 管理及使用來建置企業整合解決方案。

這篇文章示範如何建立 AS2、 EDIFACT 或 X12 建置使用企業整合解決方案，適用於 B2B 案例時，您可以使用的協議[企業整合套件](../logic-apps/logic-apps-enterprise-integration-overview.md)和[Azure Logic Apps](../logic-apps/logic-apps-overview.md). 建立協議之後，您可以接著使用 AS2、 EDIFACT 或 X12 交換 B2B 訊息的連接器。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* [整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)來儲存您的合約及其他 B2B 成品。 此整合帳戶必須與您 Azure 訂用帳戶相關聯。

* 至少兩個[交易夥伴](../logic-apps/logic-apps-enterprise-integration-partners.md)您已建立整合帳戶中。 合約需要主機合作夥伴和來賓合作夥伴。 這兩個夥伴伺服器必須使用相同的 「 商務身分識別 」 辨識符號，為您想要建立，例如 AS2、 x12，或 EDIFACT 協議。

* 選用：您要使用您的合約和啟動邏輯應用程式的工作流程的觸發程序的邏輯應用程式。 若要只建立您的整合帳戶和 B2B 成品，您不需要在邏輯應用程式。 不過，邏輯應用程式可以使用整合帳戶中的 B2B 成品之前，您必須將您的整合帳戶連結到邏輯應用程式。 如果您不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps](../logic-apps/logic-apps-overview.md) 和[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-agreements"></a>建立協議

1. 登入 [Azure 入口網站](https://portal.azure.com)。
在主要 Azure 功能表上，選取 [所有服務]  。 在 [搜尋] 方塊中，輸入 「 整合 」 作為篩選條件。 從結果中，選取 此資源：**整合帳戶**

   ![尋找您的整合帳戶](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. 底下**整合帳戶**，選取您要建立協議的整合帳戶。

   ![選取您要在其中建立合約的整合帳戶](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. 在右窗格中，在**元件**，選擇**協議**圖格。

   ![選擇 [合約]](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. 在 [合約]  之下，選擇 [新增]  。 在 [**新增**] 窗格中，提供您的合約的相關資訊，例如：

   ![選擇 [新增]](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | 屬性 | 必要項 | Value | 描述 |
   |----------|----------|-------|-------------|
   | **名稱** | 是 | <*agreement-name*> | 合約名稱 |
   | **合約類型** | 是 | **AS2**， **X12**，或**EDIFACT** | 針對您的協議通訊協定類型。 當您建立協議檔案時，該檔案中的內容必須符合的合約型別。 | |  
   | **主機合作夥伴** | 是 | <*host-partner-name*> | 主機合作夥伴代表組織指定協議 |
   | **主機識別** | 是 | <*host-partner-identifier*> | 主控夥伴的識別項 |
   | **來賓合作夥伴** | 是 | <*guest-partner-name*> | 來賓合作夥伴代表與主機合作夥伴有生意往來的組織 |
   | **來賓身分識別** | 是 | <*guest-partner-identifier*> | 來賓夥伴的識別項 |
   | **接收設定** | 視情況而異 | 視情況而異 | 這些屬性會指定如何處理收到的協議的所有內送訊息。 如需詳細資訊，請參閱個別的合約類型： <p>- [AS2 訊息設定](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT 訊息設定](logic-apps-enterprise-integration-edifact.md) <br>- [X12 訊息設定](logic-apps-enterprise-integration-x12.md) |
   | **傳送設定** | 視情況而異 | 視情況而異 | 這些屬性會指定如何處理 「 合約 」 所傳送的所有外寄訊息。 如需詳細資訊，請參閱個別的合約類型： <p>- [AS2 訊息設定](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT 訊息設定](logic-apps-enterprise-integration-edifact.md) <br>- [X12 訊息設定](logic-apps-enterprise-integration-x12.md) |
   |||||

1. 當您完成上建立您的合約**新增**頁面上，選擇**確定**，並回到整合帳戶。

   **協議**清單現在會顯示您新增的協議。

## <a name="edit-agreements"></a>編輯合約

1. 在  [Azure 入口網站](https://portal.azure.com)，在主要 Azure 功能表中，選取**所有服務**。

1. 在 [搜尋] 方塊中，輸入 「 整合 」 作為篩選條件。 從結果中，選取 此資源：**整合帳戶**

1. 底下**整合帳戶**，選取包含您想要編輯之的合約的整合帳戶。

1. 在右窗格中，在**元件**，選擇**協議**圖格。

1. 底下**協議**，選取您的合約，然後選擇**編輯**。

1. 進行，然後儲存變更。

## <a name="delete-agreements"></a>刪除合約

1. 在  [Azure 入口網站](https://portal.azure.com)，在主要 Azure 功能表中，選取**所有服務**。

1. 在 [搜尋] 方塊中，輸入 「 整合 」 作為篩選條件。 從結果中，選取 此資源：**整合帳戶**

1. 底下**整合帳戶**，選取包含您想要刪除之的合約的整合帳戶。

1. 在右窗格中，在**元件**，選擇**協議**圖格。

1. 底下**協議**，選取您的合約，然後選擇**刪除**。

1. 確認您要刪除選取的合約。

## <a name="next-steps"></a>後續步驟

* [交換 AS2 訊息](logic-apps-enterprise-integration-as2.md)
* [交換 EDIFACT 訊息](logic-apps-enterprise-integration-edifact.md)
* [交換的 X12 訊息](logic-apps-enterprise-integration-x12.md)
