---
title: Azure Analysis Services 中的資料模型相容性層級 | Microsoft Docs
description: 了解表格式資料模型的相容性層級。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 67a6c99253c549f0b8d3b55809b35b81756843eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032598"
---
# <a name="compatibility-level-for-analysis-services-tabular-models"></a>Analysis Services 表格式模型的相容性層級

*相容性層級*是指 Analysis Services 引擎中特定版本的表現方式。 相容性層級的變更通常與 SQL Server 的主要版本相符。 這些變更也會在 Azure Analysis Services 中實作，以維護兩個平台之間的同位。 相容性層級變更也會影響您表格式模型中的可用功能。 例如，DirectQuery 和表格式物件中繼資料根據相容性層級而有不同的實作方式。 相容性層級會在 Visual Studio (SSDT) 中的表格式模型專案指定。

Azure Analysis Services 支援 1200 和 1400 相容性層級的表格式模型。 最新兼容性级别是 1400。 此级别与 SQL Server 2017 Analysis Services 一致。 1400 相容性層級中的主要功能包括：

*  資料連線和匯入的新功能，並支援 TOM API 和 TMSL 指令碼。 
*  使用 Get Data 與 M 運算式的資料轉換和資料混搭功能。
*  量值支援使用 DAX 運算式的詳細資料列屬性。 此屬性可讓用戶端工具 (例如 Microsoft Excel) 從彙總的報表向下鑽研到詳細資料。 例如，當使用者依區域和月份檢視總銷售額，他們可以檢視相關的訂單詳細資料。 
*  除了資料表和資料行名稱中的資料以外，還有其物件層級安全性。
*  不完全階層的增強支援。
*  效能和監控改善。

> [!NOTE]
> Azure Analysis Services 支援 1465年相容性層級的匯入的 Power BI Desktop 檔案。 不過，從 Power BI Desktop 的功能，一直是預覽功能，匯入已停用，而且在 2019 年 3 月從服務中移除。 1465 相容性層級的現有模型會持續支援。  


## <a name="set-compatibility-level"></a>設定相容性層級

 在 SSDT 中建立新的表格式模型專案時，您可以在 [表格式模型設計工具] 對話方塊中指定相容性層級。 
  
 ![ssas_tabularproject_compat1200](./media/analysis-services-compat-level/aas-tabularproject-compat.png)  
  
 如果您選取 [不要再顯示這則訊息] 選項，所有後續專案會使用您指定的相容性層級做為預設。 您可以在 SSDT 中，利用 [工具] > [選項] 變更預設相容性層級。  
  
 若要升級 SSDT 中現有的表格式模型專案，請在模型 [屬性] 視窗中，設定 [相容性層級] 屬性。 請記住，升級相容性層級無法復原。
  
## <a name="check-compatibility-level-for-a-tabular-model-database-in-sql-server-management-studio"></a>檢查 SQL Server Management Studio 中表格式模型資料庫的相容性層級 

 在 SSMS 中，以滑鼠右鍵按一下資料庫名稱 > [屬性] > [相容性層級]。  
  
## <a name="check-supported-compatibility-level-for-a-server-in-ssms"></a>檢查 SSMS 中的伺服器支援的相容性層級  

 在 SSMS 中，以滑鼠右鍵按一下伺服器名稱 > [屬性] > [支援的相容性層級]。  
  
 此屬性會指定將在伺服器上執行之資料庫的最高相容性層級 (不包含預覽)。 支援的相容性層級不能變更。  

> [!NOTE]
> 在 SSMS 中，当连接到 Azure Analysis Services 服务器时，“支持的兼容性级别”属性将显示 **1200**。 这是一个已知问题，将在即将发布的 SSMS 更新中解决。 在解决后，此属性将显示所支持的最高兼容性级别。

## <a name="next-steps"></a>後續步驟

  [ Analysis Services](analysis-services-manage.md)  
