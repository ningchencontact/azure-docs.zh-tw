---
title: 使用 Azure 複製資料工具複製資料 | Microsoft Docs
description: 建立 Azure 資料處理站，然後使用「複製資料」工具將資料從 Azure Blob 儲存體中的某個位置複製到另一個位置。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/16/2018
ms.author: jingwang
ms.openlocfilehash: d09422f31a2dda5e14fb891fa07f65fdcceb72c1
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/17/2018
---
# <a name="use-the-copy-data-tool-to-copy-data"></a>使用複製資料工具複製資料 
> [!div class="op_single_selector" title1="Select the version of Data Factory service that you are using:"]
> * [第 1 版 - 正式推出](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [第 2 版 - 預覽](quickstart-create-data-factory-copy-data-tool.md)

在此快速入門中，您會使用 Azure 入口網站建立資料處理站。 接著，您會使用「複製資料」工具建立管線，將資料從 Azure Blob 儲存體的一個資料夾複製到另一個資料夾。 

> [!NOTE]
> 如果您不熟悉 Azure Data Factory，在執行此快速入門之前，請先參閱 [Azure Data Factory 簡介](data-factory-introduction.md)。 
>
> 本文適用於第 2 版的 Data Fatory (目前為預覽版)。 如果您使用第 1 版的服務 (也就是公開上市版 (GA))，請參閱[開始使用 Data Factory 第 1 版](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。


[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>建立 Data Factory

1. 選取左側功能表上的 [新增]、[資料 + 分析]，然後選取 [資料處理站]。 
   
   ![在 [新增] 窗格中選取資料處理站](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory-menu.png)
2. 在 [新增資料處理站] 頁面上，輸入 **ADFTutorialDataFactory** 作為 [名稱]。 
      
   ![[新增資料處理站] 頁面](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory.png)
 
   Azure Data Factory 的名稱必須是 *全域唯一的*。 如果您看到下列錯誤，請變更資料處理站的名稱 (例如 **&lt;yourname&gt;ADFTutorialDataFactory**)，然後試著重新建立。 如需 Data Factory 成品的命名規則，請參閱 [Data Factory - 命名規則](naming-rules.md)一文。
  
   ![名稱無法使用時的錯誤](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
3. 針對 [訂用帳戶]，選取您要用來建立資料處理站的 Azure 訂用帳戶。 
4. 針對 [資源群組]，使用下列其中一個步驟︰
     
   - 選取 [使用現有的]，然後從清單中選取現有的資源群組。 
   - 選取 [建立新的] ，然後輸入資源群組的名稱。   
         
   若要了解資源群組，請參閱 [使用資源群組管理您的 Azure 資源](../azure-resource-manager/resource-group-overview.md)。  
4. 針對 [版本]，選取 [V2 (預覽版)]。
5. 針對 [位置]，選取資料處理站的位置。 

   清單只會顯示支援的位置。 Data Factory 所使用的資料存放區 (如 Azure 儲存體和 Azure SQL Database) 和計算 (如 Azure HDInsight) 可位於其他位置/區域。

6. 選取 [釘選到儀表板]。     
7. 選取 [建立] 。
8. 在儀表板上，您會看到 [正在部署 Data Factory] 狀態的下列圖格︰ 

    ![[部署 Data Factory] 圖格](media/quickstart-create-data-factory-copy-data-tool/deploying-data-factory.png)
9. 建立完成之後，您會看到 [Data Factory] 頁面。 選取 [編寫與監視] 圖格，以在個別的索引標籤上啟動 Azure Data Factory 使用者介面 (UI) 應用程式。
   
   ![資料處理站的首頁，具有 [編寫與監視] 圖格](./media/quickstart-create-data-factory-copy-data-tool/data-factory-home-page.png)

## <a name="start-the-copy-data-tool"></a>啟動複製資料工具

1. 在 [現在就開始吧] 頁面上選取 [複製資料] 圖格，以啟動複製資料工具。 

   ![[複製資料] 圖格](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-tile.png)
2. 在複製資料工具的 [屬性] 頁面上，選取 [下一步]。 您可以在此頁面上指定管線的名稱及其說明。 

   ![[屬性] 頁面](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
3. 在 [來源資料存放區] 頁面上選取 [Azure Blob 儲存體]，然後選取 [下一步]。

   ![[來源資料存放區] 頁面](./media/quickstart-create-data-factory-copy-data-tool/source-data-store-page.png)
4. 在 [指定 Azure Blob 儲存體帳戶] 頁面上，從 [儲存體帳戶名稱] 清單中選取您的儲存體帳戶，然後選取 [下一步]。 

   ![[指定 Azure Blob 儲存體帳戶] 頁面](./media/quickstart-create-data-factory-copy-data-tool/specify-blob-storage-account.png)
5. 在 [選擇輸入檔案或資料夾] 頁面上，完成下列步驟︰

   a. 瀏覽至 **adftutorial/input** 資料夾。

   b. 選取 **emp.txt** 檔案。

   c. 選取 [選擇]。 您可以按兩下 **emp.txt** 略過此步驟。

   d. 選取 [下一步] 。 

   ![[選擇輸入檔案或資料夾] 頁面](./media/quickstart-create-data-factory-copy-data-tool/choose-input-file-folder.png)
6. 在 [檔案格式設定] 頁面上，留意到此工具會自動偵測資料行和資料列分隔符號，然後選取 [下一步]。 您也可以在此頁面上預覽資料，以及檢視輸入資料的結構描述。 

   ![[檔案格式設定] 頁面](./media/quickstart-create-data-factory-copy-data-tool/file-format-settings-page.png)
7. 在 [目的地資料存放區] 頁面上，選取 [Azure Blob 儲存體]，然後選取 [下一步]。 

   ![[目的地資料存放區] 頁面](./media/quickstart-create-data-factory-copy-data-tool/destination-data-store-page.png)    
8. 在 [指定 Azure Blob 儲存體帳戶] 頁面上，選取您的 Azure 儲存體帳戶，然後選取 [下一步]。 

   ![[指定 Azure Blob 儲存體帳戶] 頁面](./media/quickstart-create-data-factory-copy-data-tool/specify-sink-blob-storage-account.png)
9. 在 [選擇輸出檔案或資料夾] 頁面上，完成下列步驟︰ 

   a. 輸入 **adftutorial/output** 作為資料夾路徑。

   b. 輸入 **emp.txt** 作為檔案名稱。

   c. 選取 [下一步] 。 

   ![[選擇輸出檔案或資料夾] 頁面](./media/quickstart-create-data-factory-copy-data-tool/choose-output-file-folder.png) 
10. 在 [檔案格式設定] 頁面上，選取 [下一步]。 

    ![[檔案格式設定] 頁面](./media/quickstart-create-data-factory-copy-data-tool/file-format-settings-output-page.png)
11. 在 [設定] 頁面上，選取 [下一步]。 

    ![[設定] 頁面](./media/quickstart-create-data-factory-copy-data-tool/advanced-settings-page.png)
12. 檢閱 [摘要] 頁面上的所有設定，然後選取 [下一步]。 

    ![[摘要] 頁面](./media/quickstart-create-data-factory-copy-data-tool/summary-page.png)
13. 在 [部署完成] 頁面上選取 [監視]，以監視您建立的管線。 

    ![[部署完成] 頁面](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)
14. 應用程式會切換至 [監視] 索引標籤。您會在此索引標籤上看到管線的狀態。選取 [重新整理] 可重新整理清單。 
    
    ![用來監視管線執行的索引標籤，具有 [重新整理] 按鈕](./media/quickstart-create-data-factory-copy-data-tool/monitor-pipeline-runs-page.png)
15. 選取 [動作] 資料行中的 [檢視活動執行] 連結。 管線只有一個 [複製] 類型的活動。 

    ![活動執行清單](./media/quickstart-create-data-factory-copy-data-tool/activity-runs.png)
16. 若要檢視關於複製作業的詳細資訊，請選取 [動作] 資料行中的 [詳細資料] (眼鏡圖片) 連結。 如需屬性的詳細資訊，請參閱[複製活動概觀](copy-activity-overview.md)。 

    ![複製作業詳細資料](./media/quickstart-create-data-factory-copy-data-tool/copy-operation-details.png)
17. 確認已在 **adftutorial** 容器的 **output** 資料夾中建立 **emp.txt** 檔案。 如果 output 資料夾不存在，Data Factory 服務會自動加以建立。 
18. 切換至可讓您編輯連結服務、資料集和管線的 [編輯] 索引標籤。 若要深入了解如何在 Data Factory UI 中加以編輯，請參閱[使用 Azure 入口網站建立資料處理站](quickstart-create-data-factory-portal.md)。

    ![編輯索引標籤](./media/quickstart-create-data-factory-copy-data-tool/edit-tab.png)

## <a name="next-steps"></a>後續步驟
此範例中的管線會將資料從 Azure Blob 儲存體中的一個位置複製到其他位置。 若想了解使用 Data Factory 的更多案例，請瀏覽[教學課程](tutorial-copy-data-portal.md)。 