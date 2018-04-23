---
title: 將 Power BI Desktop 檔案匯入 Azure Analysis Services | Microsoft Docs
description: 描述如何使用 Azure 入口網站來匯入 Power BI Desktop 檔案 (pbix)。
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 35bf2ba85017de43788f802b6244d61ed2bb62df
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2018
---
# <a name="import-a-power-bi-desktop-file"></a>匯入 Power BI Desktop 檔案

您可以藉由匯入 Power BI Desktop 檔案 (pbix)，在 Azure AS 中建立新的模型。 將會匯入的項目包括模型中繼資料、快取資料和資料來源連線。 報告和視覺效果則不會匯入。

**限制**   
- pbix 模型只能連線到 Azure SQL Database 和 Azure SQL Data Warehouse 資料來源。 
- Pbix 模型不能有即時或 DirectQuery 連線。 
- 如果 pbix 資料模型包含 Analysis Services 中不支援的中繼資料，匯入可能會失敗。

## <a name="to-import-from-pbix"></a>從 pbix 匯入

1. 在您的伺服器 [概觀] > [Web 設計工具] 中，按一下 [開啟]。

    ![在 Azure 入口網站中建立模型](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. 在 [Web 設計工具]  >  [模型] 中，按一下 [+ 新增]。

    ![在 Azure 入口網站中建立模型](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. 在 [新增模型] 中輸入模型名稱，然後選取 Power BI Desktop 檔案。

    ![在 Azure 入口網站中的新增模型對話方塊](./media/analysis-services-import-pbix/aas-import-pbix-new-model.png)

4. 在 [匯入] 中找到並選取您的檔案。

     ![在 Azure 入口網站中的連線對話方塊](./media/analysis-services-import-pbix/aas-import-pbix-select-file.png)

## <a name="see-also"></a>另請參閱

[在 Azure 入口網站中建立模型](analysis-services-create-model-portal.md)   
[連線至 Azure Analysis Services](analysis-services-connect.md)  
