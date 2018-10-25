---
title: 使用 Azure Analysis Services Web 設計工具來建立表格式模型 | Microsoft Docs
description: 了解如何在 Azure 入口網站中使用 Web 設計工具，建立 Azure Analysis Services 表格式模型。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 837601a55bbe97e904f3dece9f4c87563d6dd4e9
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2018
ms.locfileid: "49092672"
---
# <a name="create-a-model-in-azure-portal"></a>在 Azure 入口網站中建立模型

Azure 入口網站中的 Azure Analysis Services Web 設計工具 (預覽) 提供快速又簡單的方法，直接在瀏覽器中建立及編輯表格式模型和查詢模型資料。 

> [!IMPORTANT]
> 此功能已被取代。 它可能會在未來更新中遭到移除或大幅變更。 建議您停止在新的和現有專案中使用這項功能，以維持與未來更新的相容性。 對於進階模型開發和測試，最好使用 Visual Studio (SSDT) 和 SQL Server Management Studio (SSMS)。


## <a name="before-you-begin"></a>開始之前

- Azure Analysis Services 伺服器必須位於標準或開發人員層。 使用 Web 設計工具所建立的新模型是 DirectQuery，只受到這些層級的支援。
- 作為資料來源的 Azure SQL Database、Azure SQL 資料倉儲或 Power BI Desktop (.pbix) 檔案。 從 Power BI Desktop 檔案建立的新模型支援 Azure SQL Database 和 Azure SQL 資料倉儲。
- 用來連線至 Azure SQL Database 或 Azure SQL 資料倉儲資料來源的 SQL Server 帳戶和密碼。
- 您必須具有伺服器系統管理員權限，才能建立新模型。 需要有資料庫管理員權限，才能使用設計工具來編輯和查詢模型。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="to-create-a-new-tabular-model"></a>若要建立新的表格式模型

1. 在您的伺服器 [概觀] > [Web 設計工具] 中，按一下 [開啟]。

    ![在 Azure 入口網站中建立模型](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. 在 [Web 設計工具]  >  [模型] 中，按一下 [+ 新增]。

    ![在 Azure 入口網站中建立模型](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. 在 [新增模型] 中輸入模型名稱，然後選取資料來源。

    ![在 Azure 入口網站中的新增模型對話方塊](./media/analysis-services-create-model-portal/aas-create-portal-new-model.png)

4. 在 [連線] 中，輸入連線屬性。 使用者名稱和密碼必須是 SQL Server 帳戶。

     ![在 Azure 入口網站中的連線對話方塊](./media/analysis-services-create-model-portal/aas-create-portal-connect.png)

5. 在 [資料表和檢視] 中，選取要包含在模型中的資料表，然後按一下 [建立]。 會使用金鑰組自動建立資料表之間的關聯性。

     ![選取資料表和檢視](./media/analysis-services-create-model-portal/aas-create-portal-tables.png)

新的模型會出現在您的瀏覽器。 您可以在這裡執行下列動作：   

- 將欄位拖曳到查詢設計工具並且新增篩選，來查詢模型資料。
- 在資料表中建立新的量值。
- 使用 json 編輯器來編輯模型中繼資料。
- 在 Visual Studio (SSDT)、Power BI Desktop 或 Excel 中開啟模型。

![選取資料表和檢視](./media/analysis-services-create-model-portal/aas-create-portal-query.png)

> [!NOTE]
> 當您在瀏覽器中編輯模型中繼資料或建立新的量值時，您會將對於模型所做的變更儲存至 Azure。 如果您正在 SSDT、Power BI Desktop 或 Excel 中使用模型，您的模型可以不同步。


## <a name="next-steps"></a>後續步驟 
[管理資料庫角色和使用者](analysis-services-database-users.md)  
[與 Excel 連線](analysis-services-connect-excel.md)  


