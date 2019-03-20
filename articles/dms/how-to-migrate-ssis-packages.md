---
title: 將 SQL Server Integration Services 套件遷移至 Azure | Microsoft Docs
description: 了解如何將 SQL Server Integration Services 套件遷移至 Azure。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 884af4624c1e92ee765353c90fd189220664381d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58183419"
---
# <a name="migrate-sql-server-integration-services-packages-to-azure"></a>將 SQL Server Integration Services 套件遷移至 Azure
如果您使用 SQL Server Integration Services (SSIS)，並想要將 SSIS 專案/套件從 SQL Server 所裝載的來源 SSISDB，遷移至 Azure SQL Database 伺服器或 Azure SQL Database 受控執行個體所裝載的目的地 SSISDB，則可以使用 Integration Services 部署精靈來予以重新部署。 您可以從 SQL Server Management Studio (SSMS) 內啟動該精靈。

如果您使用 2012 以前的 SSIS 版本，則在將 SSIS 專案/套件重新部署至專案部署模型之前，您必須先使用 Integration Services 專案轉換精靈 (亦可從 SSMS 啟動) 進行轉換。 如需詳細資訊，請參閱[將專案轉換為專案部署模型](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert)一文。

> [!NOTE]
> Azure 資料庫移轉服務 (DMS) 目前不支援移轉來源 SSISDB，但您可以使用下列程序重新部署 SSIS 專案/套件。 

在本文中，您將了解：
> [!div class="checklist"]
> * 評估來源 SSIS 專案/套件。
> * 將 SSIS 專案/套件遷移至 Azure。

## <a name="prerequisites"></a>必要條件
若要完成這些步驟，您需要：

- SSMS 17.2 版或更新版本。
- 目標資料庫伺服器的執行個體，以便裝載 SSISDB。
 
  如果您目前沒有：
    - 針對 Azure SQL Database，請使用 Azure 入口網站，藉由瀏覽至 SQL Server (僅限邏輯伺服器) [表單](https://ms.portal.azure.com/#create/Microsoft.SQLServer)來建立 Azure SQL Database 伺服器 (不含資料庫)。
    - 針對 Azure SQL Database 受控執行個體，請遵循[建立 Azure SQL Database 受控執行個體](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)一文中的詳細資料來進行。

- 必須使用 Azure SQL Database 伺服器執行個體 (如[在 Azure Data Factory 中佈建 Azure-SSIS 整合執行階段](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)一文所述) 或 Azure SQL Database 受控執行個體 (如[在 Azure Data Factory 中建立 Azure-SSIS 整合執行階段](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)一文所述) 所裝載的目的地 SSISDB，在包含 Azure-SSIS Integration Runtime (IR) 的 Azure Data Factory (ADF) 中佈建 SSIS。 

## <a name="assess-source-ssis-projectspackages"></a>評估來源 SSIS 專案/套件
評估來源 SSISDB 的功能尚未整合至 Database Migration Assistant (DMA) 或 Azure 資料庫移轉服務 (DMS)，因此 SSIS 專案/套件會在重新部署至目的地 SSISDB (裝載於 Azure SQL Database 伺服器或 Azure SQL Database 受控執行個體上) 時進行評估/驗證。

## <a name="migrate-ssis-projectspackages"></a>遷移 SSIS 專案/套件
若要將 SSIS 專案/套件遷移至 Azure SQL Database 伺服器或 Azure SQL Database 受控執行個體，請執行下列步驟。

1.  開啟 SSMS，然後選取 [選項] 來顯示 [連線至伺服器] 對話方塊。

2.  在 [登入] 索引標籤上，指定所需資訊以連線至裝載了目的地 SSISDB 的 Azure SQL Database 伺服器或 Azure SQL Database 受控執行個體。

    ![SSIS 的 [登入] 索引標籤](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)
 
3.  在 [連線屬性] 索引標籤上的 [連線至資料庫] 文字方塊中，選取或輸入 [SSISDB]，然後選取 [連線]。

    ![SSIS 的 [連線屬性] 索引標籤](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4.  在 [SSMS 物件總管] 中，依序展開 [Integration Services 目錄] 節點和 [SSISDB]，如果其中沒有現有的資料夾，則在 [SSISDB] 上按一下滑鼠右鍵並建立新的資料夾。

5.  展開 [SSISDB] 底下的任何資料夾，以滑鼠右鍵按一下 [專案]，然後選取 [部署專案]。

    ![SSIS 已展開 SSISDB 節點](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6.  在 Integration Services 部署精靈中的 [簡介] 頁面上，於檢閱資訊後選取 [下一步]。

    ![部署精靈的 [簡介] 頁面](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7.  在 [選取來源] 頁面上，指定您想要部署的現有 SSIS 專案。

    如果 SSMS 也連線至裝載了來源 SSISDB 的 SQL Server，請選取 [Integration Services 目錄]，然後在目錄中輸入伺服器名稱和專案路徑以直接部署專案。

    或者，請選取 [專案部署檔案]，然後指定現有專案部署檔案 (.ispac) 的路徑來部署專案。

    ![部署精靈的 [選取來源] 頁面](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8.  選取 [下一步] 。
9.  在 [選取目的地] 頁面上，指定專案的目的端。

       a. 在 [伺服器名稱] 文字方塊中，輸入完整的 Azure SQL Database 伺服器名稱 (<server_name>.database.windows.net) 或 Azure SQL Database 受控執行個體名稱 (<server_name.dns_prefix>.database.windows.net)。
 
       b. 提供驗證資訊，然後選取 [連線]。
    
       ![部署精靈的 [選取目的地] 頁面](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. 選取 [瀏覽] 以便在 SSISDB 中指定目的地資料夾，然後選取 [下一步]。

    > [!NOTE]
    > 在選取 [連線] 之後，才會啟用 [下一步] 按鈕。 

10. 檢視 [驗證] 頁面上的任何錯誤/警告，然後視需要據此修改套件。

    ![部署精靈的 [驗證] 頁面](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. 選取 [下一步] 。

12. 在 [檢閱] 頁面上，檢閱您的部署設定。

    > [!NOTE]
    > 選取 [上一步] 或左窗格中的任何一個步驟連結，即可變更您的設定。

13. 選取 [部署] 來啟動部署程序。

14. 部署程序完成後，您可以檢視 [結果] 頁面，裡面會顯示每個部署動作是成功還是失敗。
    a. 如果有任何動作失敗，請在 [結果] 資料行中選取 [失敗]，以顯示錯誤的說明。
    b. (選擇性) 選取 [儲存報告] 將結果儲存至 XML 檔案。

15. 選取 [關閉] 來結束 Integration Services 部署精靈。

如果專案部署成功且沒有任何失敗，您便可以選取其內含的任何套件以在 Azure-SSIS IR 上執行。

## <a name="next-steps"></a>後續步驟
- 在 Microsoft [資料庫移轉指南](https://datamigration.microsoft.com/) \(英文\) 中檢閱移轉指引。