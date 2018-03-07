---
title: "在 Azure Logic Apps 中建立、連結、刪除或移動整合帳戶 | Microsoft Docs"
description: "如何建立整合帳戶，並將它連結到您的邏輯應用程式"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: LADocs; mandia
ms.openlocfilehash: b238ef8cf9d1328913334a92c042584334d81e3c
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="what-is-an-integration-account"></a>什麼是整合帳戶？

整合帳戶讓您的企業整合應用程式 (特別是邏輯應用程式) 能夠存取和管理 B2B 成品，例如交易夥伴、合約、地圖、結構描述、憑證等等。 若要提供此存取權，請在確定整合帳戶與邏輯應用程式的「Azure 位置都相同」之後，將您的整合帳戶連結到邏輯應用程式。

## <a name="create-an-integration-account"></a>建立整合帳戶

1. 登入 [Azure 入口網站](http://portal.azure.com "Azure 入口網站")。 

2. 從主要 Azure 功能表中選取 [所有服務]。 在搜尋方塊中輸入「整合」，然後選取 [整合帳戶]。

   ![建立整合帳戶](./media/logic-apps-enterprise-integration-accounts/account-1.png)

3. 在頁面頂端選擇 [新增]。

   ![選擇 [新增]](./media/logic-apps-enterprise-integration-accounts/account-3.png)

4. 替您的整合帳戶命名，並選取您要使用的 Azure 訂用帳戶。 您可以建立新的 [資源群組]，或選取現有的資源群組。 選取 [位置] 以便裝載整合帳戶和 [定價層]。 準備就緒時，請選擇 [建立]。

   ![提供整合帳戶的詳細資料](./media/logic-apps-enterprise-integration-accounts/account-4.png)

   Azure 會在選取的位置佈建整合帳戶，這應該會在一分鐘內完成。

5. 重新整理頁面。 您會看到已列出新的整合帳戶。

   ![您的新整合帳戶隨即出現](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

接下來，將您建立的整合帳戶連結至您的邏輯應用程式。 

## <a name="link-an-integration-account-to-a-logic-app"></a>將整合帳戶連結至邏輯應用程式

若要讓您的邏輯應用程式存取整合帳戶中的 B2B 成品，例如交易夥伴、合約、地圖及結構描述，請將整合帳戶連結到邏輯應用程式。 

1. 在 Azure 入口網站中，選取邏輯應用程式，然後檢查邏輯應用程式的位置。

   ![選取邏輯應用程式，檢查位置](./media/logic-apps-enterprise-integration-accounts/linkaccount-1.png)

2. 在 [設定] 之下，選取 [整合帳戶]。

   ![選取 [整合帳戶]](./media/logic-apps-enterprise-integration-accounts/linkaccount-2.png)

3. 從 [選取整合帳戶] 清單，選取您想要連結到邏輯應用程式的整合帳戶。 若要完成連結，請選擇 [儲存]。

   ![選取您的整合帳戶](./media/logic-apps-enterprise-integration-accounts/linkaccount-3.png)

   您會收到一則通知，表示您的整合帳戶已連結到邏輯應用程式，而整合帳戶中的所有成品現在都可在邏輯應用程式中使用。

   ![您的邏輯應用程式已連結到您的整合帳戶](./media/logic-apps-enterprise-integration-accounts/linkaccount-5.png)

現在您的整合帳戶已連結到邏輯應用程式，您可以在邏輯應用程式中使用 B2B 連接器。 部分常見的 B2B 連接器包括 XML 驗證和一般檔案編碼/解碼。  

## <a name="delete-your-integration-account"></a>刪除您的整合帳戶

1. 從主要 Azure 功能表中選取 [所有服務]。 在搜尋方塊中輸入「整合」，然後選取 [整合帳戶]。

   ![尋找您的整合帳戶](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. 選取您想要刪除的整合帳戶。

    ![選取要刪除的整合帳戶](./media/logic-apps-enterprise-integration-accounts/account-5.png)

3. 在功能表上，選擇 [刪除]。

    ![選擇 [刪除]](./media/logic-apps-enterprise-integration-accounts/delete.png)

4. 確認您的選擇以刪除整合帳戶。

## <a name="move-your-integration-account"></a>移動您的整合帳戶

若要將整合帳戶移到其他 Azure 訂用帳戶或資源群組，請遵循下列步驟。 在您移動整合帳戶之後，請務必更新所有指令碼，以使用新的資源識別碼。

1. 從主要 Azure 功能表中選取 [所有服務]。 在搜尋方塊中輸入「整合」，然後選取 [整合帳戶]。

   ![尋找您的整合帳戶](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. 選取您想要移動的整合帳戶。 在 [設定] 之下選擇 [屬性]。

   ![選取要移動的整合帳戶。 在 [設定] 之下選擇 [屬性]](./media/logic-apps-enterprise-integration-accounts/move.png)

3. 變更與整合帳戶相關聯的資源群組或 Azure 訂用帳戶。

   ![選擇 [變更資源群組] 或 [變更訂用帳戶]](./media/logic-apps-enterprise-integration-accounts/move-2.png)

## <a name="next-steps"></a>後續步驟

* [深入了解合約](../logic-apps/logic-apps-enterprise-integration-agreements.md "了解企業整合合約")  

