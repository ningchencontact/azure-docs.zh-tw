---
title: 為您的 Azure Analysis Services 伺服器新增範例表格式模型 | Microsoft Docs
description: 了解如何在 Azure Analysis Services 中新增範例模型。
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 27353ff8c05f44b76304279e09a8a8d817041d78
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-add-a-sample-model"></a>教學課程：新增範例模型

在本教學課程中，您會將範例 Adventure Works 模型新增至您的伺服器。 此範例模型是 Adventure Works Internet Sales (1200) 資料模型化教學課程的完整版本。 範例模型有助於測試模型管理、與工具和用戶端應用程式連線，以及查詢模型資料。

## <a name="before-you-begin"></a>開始之前

若要完成本教學課程，您需要：

- Azure Analysis Services 伺服器
- 伺服器管理員權限

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-a-sample-model"></a>建立範例模型

1. 在伺服器的 [概觀] 中，按一下 [新增模型]。

    ![建立範例模型](./media/analysis-services-create-sample-model/aas-create-sample-new-model.png)

2. 在 [新增模型] > [選擇資料來源] 中，確認已選取 [範例資料]，然後按一下 [新增]。

    ![選取範例資料](./media/analysis-services-create-sample-model/aas-create-sample-data.png)

3. 在 [概觀] 中，確認 `adventureworks` 範例已建立。

    ![選取範例資料](./media/analysis-services-create-sample-model/aas-create-sample-verify.png)

## <a name="clean-up-resources"></a>清除資源

範例模型會使用快取記憶體資源。 如果您不將範例模型用於測試，則應將其從伺服器中移除。

> [!NOTE]
> 下列步驟說明如何使用 SSMS 從伺服器中刪除模型。 您也可以使用預覽的 Web 設計工具功能來刪除模型。

1. 在 SSMS > [物件總管] 中，按一下 [連線]  > [分析服務] 。

2. 在 [連線到伺服器] 中貼上伺服器名稱，然後在 [驗證] 中選擇 [具 MFA 支援的 Active Directory - 通用]，並輸入您的使用者名稱，然後按一下 [連線]。

    ![登入](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-signin.png)

3. 在 [物件總管] 中，以滑鼠右鍵按一下 `adventureworks`，然後按一下 [刪除]。

    ![刪除範例資料庫](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-delete.png)

## <a name="next-steps"></a>後續步驟 

[在 Power BI Desktop 中連線](analysis-services-connect-pbi.md)   
[管理資料庫角色和使用者](analysis-services-database-users.md)


