---
title: "管理整合帳戶構件中繼資料 - Azure Logic Apps | Microsoft Docs"
description: "從 Azure Logic Apps 的整合帳戶新增或擷取構件中繼資料"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 02/23/2018
ms.author: LADocs; padmavc
ms.openlocfilehash: 59cebb6c0b86f4e3c4e16a5b6d2ada7b3e7a44a2
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="manage-artifact-metadata-in-integration-accounts-for-logic-apps"></a>在 Logic Apps 的整合帳戶中管理構件中繼資料

您可以在整合帳戶中定義構件的自訂中繼資料，並在邏輯應用程式的執行階段期間擷取該中繼資料。 例如，您可以指定構件的中繼資料 (如合作夥伴、合約、結構描述及對應)，這些全都會使用索引鍵值組來儲存中繼資料。 

## <a name="add-metadata-to-artifacts-in-integration-accounts"></a>將中繼資料新增至整合帳戶中的構件

1. 在 Azure 入口網站中建立[整合帳戶](logic-apps-enterprise-integration-create-integration-account.md)。

2. 將構件新增至您的整合帳戶，例如，[合作夥伴](logic-apps-enterprise-integration-partners.md)、[合約](logic-apps-enterprise-integration-agreements.md)或[結構描述](logic-apps-enterprise-integration-schemas.md)。

3. 選取構件、選擇 [編輯]，然後輸入中繼資料詳細資訊。

   ![輸入中繼資料](media/logic-apps-enterprise-integration-metadata/image1.png)

## <a name="retrieve-metadata-from-artifacts-for-logic-apps"></a>從邏輯應用程式的構件擷取中繼資料

1. 在 Azure 入口網站中建立[邏輯應用程式](quickstart-create-first-logic-app-workflow.md)。

2. 建立[從邏輯應用程式到您整合帳戶的連結](logic-apps-enterprise-integration-create-integration-account.md#link-an-integration-account-to-a-logic-app)。 

3. 在邏輯應用程式設計工具中，將要求 或 HTTP 之類的觸發程序新增至邏輯應用程式。

4. 在觸發程序底下，選擇 [新增步驟] > [新增動作]。 搜尋「整合帳戶」，如此一來，您就能尋找然後選取此動作：**整合帳戶 - 整合帳戶構件查閱**

   ![選取整合帳戶構件查閱](media/logic-apps-enterprise-integration-metadata/image2.png)

5. 選取 [構件類型]，並提供 [構件名稱]。 例如︰

   ![選取構件類型並指定構件名稱](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="example-retrieve-partner-metadata"></a>範例：擷取合作夥伴中繼資料

假設此合作夥伴具有此中繼資料與 `routingUrl` 詳細資料：

![尋找合作夥伴 "routingURL" 中繼資料](media/logic-apps-enterprise-integration-metadata/image6.png)

1. 在您的邏輯應用程式中，新增觸發程序、適用於您合作夥伴的**整合帳戶 - 整合帳戶構件查閱**動作，以及 **HTTP** 動作，例如：

   ![將觸發程序、構件查閱及 HTTP 動作新增至您的邏輯應用程式](media/logic-apps-enterprise-integration-metadata/image4.png)

2. 若要擷取 URI，在邏輯應用程式設計工具工具列上，為您的邏輯應用程式選擇 [程式碼檢視]。 您的邏輯應用程式定義應該看起來像這個範例：

   ![搜尋查閱](media/logic-apps-enterprise-integration-metadata/image5.png)

## <a name="next-steps"></a>後續步驟

* [深入了解合約](logic-apps-enterprise-integration-agreements.md)
