---
title: 將資料從 Azure Data Factory 複製到 Azure 資料總管
description: 在本主題中，您將瞭解如何使用 Azure Data Factory 複製工具，將資料內嵌（載入）至 Azure 資料總管
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 5eb05df7ed97839ef80798a752565234d180f0e2
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268828"
---
# <a name="copy-data-to-azure-data-explorer-using-azure-data-factory"></a>使用 Azure Data Factory 將資料複製到 Azure 資料總管 

Azure 資料總管是快速、完全受控的資料分析服務，可即時分析來自許多來源（例如應用程式、網站和 IoT 裝置）的大量資料串流。 反復探索資料並識別模式和異常，以改善產品、增強客戶體驗、監視裝置，以及提升營運效率。 探索新的問題並在幾分鐘內獲得解答。 Azure Data Factory 是完全受控的雲端式資料整合服務。 您可以使用服務，將現有系統的資料填入 Azure 資料總管資料庫，並在建立分析解決方案時節省時間。

Azure Data Factory 提供將資料載入 Azure 資料總管的下列優點：

* **容易設定**：不需要編寫腳本的直覺五個步驟的 wizard。
* **豐富的資料存放區支援**：內建支援一組豐富的內部部署和雲端式資料存放區。 如需詳細清單，請參閱[支援的資料存放區](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)的資料表。
* **安全且符合規範**：資料會透過 HTTPS 或 ExpressRoute 傳輸。 具有全域服務，可確保資料絕不會離開地理界限。
* **高效**能：Azure 資料總管最多可達 1 GB/秒的資料載入速度。 如需詳細資料，請參閱[複製活動效能](/azure/data-factory/copy-activity-performance)。

本文說明如何使用 Data Factory 資料複製工具，將資料從 Amazon S3 載入至 Azure 資料總管。 您可以遵循類似的步驟，從其他資料存放區（例如[Azure Blob 儲存體](/azure/data-factory/connector-azure-blob-storage)、 [Azure SQL Database](/azure/data-factory/connector-azure-sql-database)、 [Azure SQL 資料倉儲](/azure/data-factory/connector-azure-sql-data-warehouse)、 [Google BigQuery](/azure/data-factory/connector-google-bigquery)、[Oracle](/azure/data-factory/connector-oracle)和[檔案系統](/azure/data-factory/connector-file-system)）複製資料。

## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* [Azure 資料總管叢集和資料庫](create-cluster-database-portal.md)
* 資料來源。

## <a name="create-a-data-factory"></a>建立 Data Factory

1. 選取入口網站左上角的 **建立資源** 按鈕（+） >**分析** >  **Data Factory**。

   ![建立新的資料處理站](media/data-factory-load-data/create-adf.png)

1. 在 [**新增 data factory** ] 頁面上，提供下欄欄位的值，然後選取 [**建立**]。

    ![新增資料處理站頁面](media/data-factory-load-data/my-new-data-factory.png)

    **設定**  | **欄位描述**
    |---|---|
    | **名稱** | 輸入資料處理站的全域唯一名稱。 如果您收到「資料處理站*名稱\"LoadADXDemo\"無法使用*」錯誤，請為資料處理站輸入不同的名稱。 如需 Data Factory 成品的命名規則，請參閱[Data Factory 命名規則](/azure/data-factory/naming-rules)。|
    | **訂用帳戶** | 選取用來在其中建立資料處理站的 Azure 訂用帳戶。 |
    | **資源群組** | 選取 **[新建]** ，然後輸入新資源群組的名稱。 如果您有現有的資源群組，請選取 [**使用現有**的]。 |
    | **版本** | 選取 [V2] |
    | **位置** | 選取資料處理站的位置。 只有受到支援的位置會顯示在下拉式清單中。 Data factory 所使用的資料存放區可位於其他位置或區域中。 |
    | | |

1. 選取工具列上的 [通知] 來監視建立程式。 建立完成後，請移至您所建立的 data factory。 [ **Data Factory** ] 首頁隨即開啟。

   ![Data Factory 首頁](media/data-factory-load-data/data-factory-home-page.png)

1. 選取 [**作者 & 監視器**] 圖格，以在另一個索引標籤中啟動應用程式。

## <a name="load-data-into-azure-data-explorer"></a>將資料載入 Azure 資料總管

資料可以從許多類型的資料存放[區](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)載入至 Azure 資料總管。 本主題詳細說明如何從 Amazon S3 載入資料。

有兩種方式可以使用 Azure Data Factory 將資料載入 Azure 資料總管：

* Azure Data Factory 使用者介面-[ [**作者**] 索引標籤](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)
* 本文中使用[Azure Data Factory**資料複製**工具](/azure/data-factory/quickstart-create-data-factory-copy-data-tool)。

### <a name="copy-data-from-amazon-s3-source"></a>從 Amazon S3 （來源）複製資料

1. 在 [**現在就開始**吧] 頁面中，選取 [**資料複製**] 圖格以啟動資料複製工具。

   ![複製資料工具圖格](media/data-factory-load-data/copy-data-tool-tile.png)

1. 在 [**屬性**] 頁面中指定 [工作**名稱**]，然後選取 **[下一步]** 。

    ![從來源屬性複製](media/data-factory-load-data/copy-from-source.png)

1. 在 [**來源資料存放區**] 頁面中，按一下 [ **+ 建立新連接**]。

    ![來源資料建立連接](media/data-factory-load-data/source-create-connection.png)

1. 選取 [ **Amazon S3**]，然後選取 [**繼續**]

    ![新增連結服務](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. 在 [**新增連結服務（Amazon S3）** ] 頁面中，執行下列步驟：

    ![指定 Amazon S3 連結服務](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    * 指定新連結服務的**名稱**。
    * 選取下拉式清單中的 **[透過整合運行**時間來連線] 值。
    * 指定 [存取金鑰識別碼] 值。
    * 指定 [祕密存取金鑰] 值。
    * 選取 [**測試連接**] 來測試您所建立的連結服務連接。
    * 選取 [完成]。
    
    > [!NOTE]
    > 在 Amazon S3 的導覽列中，選取您的 Amazon 使用者名稱，然後選取 [**我的安全性認證**] 以找出您的**存取金鑰**。 

1. 在 [**來源資料存放區**] 頁面中，您會看到新的 AmazonS31 連接。 選取 [下一步]。

   ![來源資料存放區已建立連接](media/data-factory-load-data/source-data-store-created-connection.png)

1. 在 [**選擇輸入檔案或資料夾**] 頁面中：

    1. 流覽至您要複製的資料夾/檔案。 選取資料夾/檔案。
    1. 視需要選取複製行為。 保留未核取的**二進位複製**。
    1. 選取 [下一步]。

    ![選擇輸入檔案或資料夾](media/data-factory-load-data/source-choose-input-file.png)

1. 在 [**檔案格式設定**] 頁面中，選取檔案的相關設定，然後按 **[下一步]** 。

   ![選擇輸入檔案或資料夾](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>將資料複製到 Azure 資料總管（目的地）

建立 azure 資料總管新的連結服務，以將資料複製到以下指定的 Azure 資料總管目的地資料表（接收）。

#### <a name="create-the-azure-data-explorer-linked-service"></a>建立 Azure 資料總管連結服務

1. 在 [**目的地資料存放區**] 頁面中，您可以使用現有的資料存放區連接，或按一下 [ **+ 建立新的連接**] 來指定新的資料存放區。

    ![目的地資料存放區頁面](media/data-factory-load-data/destination-create-connection.png)

1. 在 [**新增連結服務**] 頁面中，選取 [ **Azure 資料總管**]，然後選取 [**繼續**]。

    ![選取 Azure 資料總管-新增連結服務](media/data-factory-load-data/adx-select-new-linked-service.png)

1. 在 [**新增連結服務（Azure 資料總管）** ] 頁面中，執行下列步驟：

    ![ADX 新的連結服務](media/data-factory-load-data/adx-new-linked-service.png)

   * 選取 [Azure 資料總管連結服務的**名稱**]。
   * 在 [**帳戶選取方法**] 中： 
        * 選取 [**從 azure 訂用**帳戶] 選項按鈕，然後選取您的**Azure 訂**用帳戶。 然後，**選取您的**叢集。 請注意，下拉式清單只會列出屬於使用者的叢集。
        * 或者，選取 [**手動輸入**] 選項按鈕，然後輸入您的**端點**。
    * 指定**租**使用者。
    * 輸入**服務主體識別碼**。
    * 選取 [**服務主體金鑰**] 按鈕，然後輸入**服務主體金鑰**。
    * 從下拉式功能表中選取您的**資料庫**。 或者，選取 [**編輯**] 核取方塊，並輸入您的資料庫名稱。
    * 選取 [**測試連接**] 來測試您所建立的連結服務連接。 如果您可以連接到您的設定，則會出現綠色的核取記號連線**成功**。
    * 選取 **[完成]** 以完成已連結的服務建立。

    > [!NOTE]
    > Azure Data Factory 使用服務主體來存取 Azure 資料總管服務。 針對服務主體，[建立 Azure Active Directory （Azure AD）服務主體](/azure-stack/operator/azure-stack-create-service-principals#manage-an-azure-ad-service-principal)。 請勿使用**Azure Key Vault**方法。

#### <a name="configure-the-azure-data-explorer-data-connection"></a>設定 Azure 資料總管資料連線

1. **目的地資料存放區**隨即開啟。 您所建立的 Azure 資料總管資料連線可供使用。 選取 **[下一步]** 以設定連接。

    ![ADX 目的地資料存放區](media/data-factory-load-data/destination-data-store.png)

1. 在 [**資料表對應**] 中，設定目的地資料表名稱，然後選取 **[下一步]** 。

    ![目的地資料集資料表對應](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. 在 [資料**行對應**] 頁面中：
    * 第一個對應是由 ADF 根據[adf 架構對應](/azure/data-factory/copy-activity-schema-and-type-mapping)來執行
        * 設定 Azure Data Factory 目的地資料表的資料**行**對應。 預設的對應會從來源顯示到 ADF 目的地資料表。
        * 取消選取您不需要定義資料行對應的資料行。
    * 當此表格式資料內嵌至 Azure 資料總管時，就會發生第二個對應。 對應是根據[CSV 對應規則](/azure/kusto/management/mappings#csv-mapping)來執行。 請注意，即使來源資料不是 CSV 格式，ADF 也會將資料轉換成表格式格式，因此，CSV 對應是此階段中唯一相關的對應。
        * 在 **[Azure 資料總管（Kusto）接收屬性**] 下，新增相關的內嵌**對應名稱**（選擇性），以便使用資料行對應。
        * 如果未指定內嵌**對應名稱**，則會在 [資料**行**對應] 區段中定義「依名稱」對應順序。 如果「依名稱」對應失敗，Azure 資料總管將會嘗試以「逐資料行位置」順序內嵌資料（依位置對應預設值）。
    * 選取 [**下一步**]

    ![目的地資料集資料行對應](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. 在 [設定] 頁面中：
    * 設定相關的**容錯設定**。
    * **效能設定**：啟用暫存不適用。 [**高級設定**] 包含成本考慮。 如果沒有特定的需求，請保持原狀。
    * 選取 [下一步]。

    ![複製資料設定](media/data-factory-load-data/copy-data-settings.png)

1. 在 [**摘要**] 中，檢查設定，然後選取 **[下一步]** 。

    ![複製資料摘要](media/data-factory-load-data/copy-data-summary.png)

1. 在 [**部署] 頁面**中：
    * 選取 [**監視**] 以切換至 [**監視**] 索引標籤，並查看管線的狀態（進度、錯誤和資料流程）。
    * 選取 [**編輯管線**] 以編輯連結的服務、資料集和管線。
    * 選取 **[完成]** 以完成複製資料工作

    ![部署頁面](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>後續步驟

* 瞭解 Azure Data Factory 中的[Azure 資料總管連接器](/azure/data-factory/connector-azure-data-explorer)。

* 深入瞭解如何在[DATA FACTORY UI](/azure/data-factory/quickstart-create-data-factory-portal)中編輯連結的服務、資料集和管線。

* 瞭解用於資料查詢的[Azure 資料總管查詢](/azure/data-explorer/web-query-data)。
