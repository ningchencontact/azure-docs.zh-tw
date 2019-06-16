---
title: 重新部署到 Azure SQL Database 的 SQL Server Integration Services 封裝 |Microsoft Docs
description: 了解如何將 SQL Server Integration Services 封裝移轉到 Azure SQL Database。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/08/2019
ms.openlocfilehash: 603a9df8e3f499c832bbfdcbef966de86003d6b7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080644"
---
# <a name="redeploy-sql-server-integration-services-packages-to-azure-sql-database"></a>重新部署到 Azure SQL Database 的 SQL Server Integration Services 封裝

如果您使用 SQL Server Integration Services (SSIS)，並想要從來源到目的地 Azure SQL Database 所裝載的 SSISDB 的 SQL Server 所裝載的 SSISDB 移轉您的 SSIS 專案/套件，您可以重新部署這些使用 Integration Services 部署精靈。 您可以從 SQL Server Management Studio (SSMS) 內啟動該精靈。

如果您使用 2012 以前的 SSIS 版本，則在將 SSIS 專案/套件重新部署至專案部署模型之前，您必須先使用 Integration Services 專案轉換精靈 (亦可從 SSMS 啟動) 進行轉換。 如需詳細資訊，請參閱[將專案轉換為專案部署模型](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert)一文。

> [!NOTE]
> Azure 資料庫移轉服務 (DMS) 目前不支援移轉到 Azure SQL Database 伺服器，來源 SSISDB，但您可以重新部署您的 SSIS 專案/套件使用下列程序。

在本文中，您將了解：
> [!div class="checklist"]
>
> * 評估來源 SSIS 專案/套件。
> * 將 SSIS 專案/套件遷移至 Azure。

## <a name="prerequisites"></a>必要條件

若要完成這些步驟，您需要：

* SSMS 17.2 版或更新版本。
* 目標資料庫伺服器的執行個體，以便裝載 SSISDB。 如果您還沒有做，建立使用 Azure 入口網站中瀏覽至 SQL Server （僅限邏輯伺服器） 的 Azure SQL Database 伺服器 （不含資料庫）[表單](https://ms.portal.azure.com/#create/Microsoft.SQLServer)。
* 必須佈建 SSIS 在 Azure Data Factory (ADF) 包含 Azure SSIS Integration Runtime (IR) 與目的地的 Azure SQL Database 伺服器執行個體所裝載的 SSISDB (如本文所述[佈建 Azure SSIS 整合Azure Data Factory 中的執行階段](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure))。

## <a name="assess-source-ssis-projectspackages"></a>評估來源 SSIS 專案/套件

評估來源 SSISDB 尚未整合至 Database Migration Assistant (DMA) 或 Azure 資料庫移轉服務 (DMS)，而您 SSIS 專案/套件將會評估/驗證，它們會重新部署到目的地裝載的 SSISDBAzure SQL Database 伺服器。

## <a name="migrate-ssis-projectspackages"></a>遷移 SSIS 專案/套件

若要將 SSIS 專案/套件移轉至 Azure SQL Database 伺服器中，執行下列步驟。

1. 開啟 SSMS，然後選取 [選項]  來顯示 [連線至伺服器]  對話方塊。

2. 在 **登入**索引標籤上，指定連接至將裝載 SSISDB 的目的地 Azure SQL Database 伺服器所需的資訊。

    ![SSIS 的 [登入] 索引標籤](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)

3. 在 [連線屬性]  索引標籤上的 [連線至資料庫]  文字方塊中，選取或輸入 [SSISDB]  ，然後選取 [連線]  。

    ![SSIS 的 [連線屬性] 索引標籤](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4. 在 [SSMS 物件總管] 中，依序展開 [Integration Services 目錄]  節點和 [SSISDB]  ，如果其中沒有現有的資料夾，則在 [SSISDB]  上按一下滑鼠右鍵並建立新的資料夾。

5. 展開 [SSISDB]  底下的任何資料夾，以滑鼠右鍵按一下 [專案]  ，然後選取 [部署專案]  。

    ![SSIS 已展開 SSISDB 節點](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6. 在 Integration Services 部署精靈中的 [簡介]  頁面上，於檢閱資訊後選取 [下一步]  。

    ![部署精靈的 [簡介] 頁面](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7. 在 [選取來源]  頁面上，指定您想要部署的現有 SSIS 專案。

    如果 SSMS 也連線至裝載了來源 SSISDB 的 SQL Server，請選取 [Integration Services 目錄]  ，然後在目錄中輸入伺服器名稱和專案路徑以直接部署專案。

    或者，請選取 [專案部署檔案]  ，然後指定現有專案部署檔案 (.ispac) 的路徑來部署專案。

    ![部署精靈的 [選取來源] 頁面](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8. 選取 [下一步]  。
9. 在 [選取目的地]  頁面上，指定專案的目的端。

    a. 在 [伺服器名稱] 文字方塊中，輸入完整的 Azure SQL Database 伺服器名稱 (< 伺服器名稱 >。 database.windows.net)。

    b. 提供驗證資訊，然後選取 [連線]  。

    ![部署精靈的 [選取目的地] 頁面](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. 選取 [**瀏覽**在 SSISDB 中，指定目的地資料夾，然後選取**下一步]** 。

    > [!NOTE]
    > 在選取 [連線]  之後，才會啟用 [下一步]  按鈕。

10. 檢視 [驗證]  頁面上的任何錯誤/警告，然後視需要據此修改套件。

    ![部署精靈的 [驗證] 頁面](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. 選取 [下一步]  。

12. 在 [檢閱]  頁面上，檢閱您的部署設定。

    > [!NOTE]
    > 您可以變更您的設定，方法是選取**Previous**或左窗格中選取任何一個步驟的連結。

13. 選取 [部署]  來啟動部署程序。

14. 部署程序完成後，您可以檢視 [結果] 頁面，裡面會顯示每個部署動作是成功還是失敗。
    a. 如果有任何動作失敗，請在 [結果]  資料行中選取 [失敗]  ，以顯示錯誤的說明。
    b. (選擇性) 選取 [儲存報告]  將結果儲存至 XML 檔案。

15. 選取 [關閉]  來結束 Integration Services 部署精靈。

如果專案部署成功且沒有任何失敗，您便可以選取其內含的任何套件以在 Azure-SSIS IR 上執行。

## <a name="next-steps"></a>後續步驟

* 在 Microsoft [資料庫移轉指南](https://datamigration.microsoft.com/) \(英文\) 中檢閱移轉指引。
