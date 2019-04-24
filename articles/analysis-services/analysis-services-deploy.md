---
title: 使用 Visual Studio (SSDT) 部署至 Azure Analysis Services | Microsoft Docs
description: 了解如何使用 SSDT 將表格式模型部署至 Azure Analysis Services 伺服器。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b9740d74a25964286ea92b4238684db81a64c9e2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60497444"
---
# <a name="deploy-a-model-from-visual-studio"></a>從 Visual Studio 部署模型

以您的 Azure 訂用帳戶建立伺服器後，您即可將表格式模型資料庫部署至該伺服器。 您可以使用 SQL Server Data Tools (SSDT) 建置與部署您正在使用的表格式模型專案。 

## <a name="prerequisites"></a>必要條件

若要開始，您需要：

* Azure 中的 **Analysis Services 服务器**。 若要深入了解，請參閱[建立 Azure Analysis Services 伺服器](analysis-services-create-server.md)。
* SSDT 中的**表格式模型專案**，或 1200 或更高相容性層級的現有表格式模型。 未曾建立過？ 嘗試 [Adventure Works 網際網路銷售表格式模型教學課程](/sql/analysis-services/tabular-modeling-adventure-works-tutorial)。
* **內部部署閘道** - 如果您組織的網路中有一或多個資料來源為內部部署，您必須安裝[內部部署資料閘道](analysis-services-gateway.md)。 您在雲端中的伺服器必須有閘道，才能連線至您的內部部署資料來源，以處理和重新整理模型中的資料。

> [!TIP]
> 部署之前，請確定您可以在資料表中處理資料。 在 SSDT 中，按一下 [模型]  >  [程序]  >  **Process All** (全部處理)。 如果處理失敗，您就無法部署成功。
> 
> 

## <a name="get-the-server-name"></a>取得伺服器名稱

在 [Azure 入口網站] > 伺服器 > [概觀]  >  [伺服器名稱] 中，複製伺服器名稱。
   
![在 Azure 中取得伺服器名稱](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="to-deploy-from-ssdt"></a>若要從 SSDT 部署

1. 在 SSDT > [方案總管] 中，以滑鼠右鍵按一下專案 > [屬性]。 接著在 [部署]  >  [伺服器] 中，貼上伺服器名稱。   
   
    ![將伺服器名稱貼到部署伺服器屬性](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
2. 在 [方案總管] 中，以滑鼠右鍵按一下 [屬性]，然後按一下 [部署]。 系統會提示您登入 Azure。
   
    ![部署至伺服器](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    部署狀態會出現在 [輸出] 視窗和 [部署] 中。
   
    ![部署狀態](./media/analysis-services-deploy/aas-deploy-status.png)

就是這麼簡單！


## <a name="troubleshooting"></a>疑難排解

如果在部署中繼資料時部署失敗，則可能是因為 SSDT 無法連線至您的伺服器。 請確定您可以使用 SSMS 連線至您的伺服器。 接著確定專案的 [部署伺服器] 屬性正確。

如果在資料表上部署失敗，則可能是因為您的伺服器無法連線至資料來源。 如果您的組織來源在您組織的網路中為內部部署，請務必安裝[內部部署資料閘道](analysis-services-gateway.md)。

## <a name="next-steps"></a>後續步驟

現在您的伺服器上已部署了表格式模型，您即可連線至該伺服器。 您可以[使用 SSMS 連線至該伺服器](analysis-services-manage.md)以進行管理。 此外，您可以[使用用戶端工具連線至該伺服器 ](analysis-services-connect.md) (如 Power BI、Power BI Desktop 或 Excel 等工具)，並開始建立報告。

