---
title: 從 Azure Data Factory 複製資料到 Azure 資料總管
description: 在本主題中，您學會 （負載） 資料內嵌至 Azure 的資料總管，使用 Azure Data Factory 複製工具
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 64856d53168a7676cf279da2d8675ce81e1985f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60447721"
---
# <a name="copy-data-to-azure-data-explorer-using-azure-data-factory"></a>將資料複製到使用 Azure Data Factory 的 Azure 資料總管 

Azure 的資料總管是快速且受到完整管理的資料分析服務來執行即時分析大量資料流從許多來源，例如應用程式、 網站和 IoT 裝置上。 反覆地瀏覽資料並識別模式和異常，以改善產品、 加強客戶體驗，監視裝置，並提升作業。 探索新的問題並在幾分鐘內獲得解答。 Azure Data Factory 是完全受控的雲端式資料整合服務。 您可以在資料庫中填入 Azure 資料總管資料從您現有的系統，並節省時間使用服務時建置分析解決方案。

Azure Data Factory 資料載入 Azure 資料總管產生下列好處：

* **容易設定**：直覺式 5 步驟精靈不需要編寫指令碼。
* **豐富的資料存放區支援**:一組豐富內部部署和雲端資料存放區的內建支援。 如需詳細清單，請參閱[支援的資料存放區](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)的資料表。
* **安全且符合規範**:資料會透過 HTTPS 或 ExpressRoute 傳輸。 具有全域服務，可確保資料絕不會離開地理界限。
* **高效能**:最多 1 GB/s 資料到 Azure 資料總管的載入速度。 如需詳細資料，請參閱[複製活動效能](/azure/data-factory/copy-activity-performance)。

這篇文章會示範如何使用 Data Factory 複製資料工具將資料從 Amazon S3 載入 Azure 資料總管。 您可以依照類似的步驟，從其他資料存放區複製資料時，也將這類[Azure Blob 儲存體](/azure/data-factory/connector-azure-blob-storage)， [Azure SQL Database](/azure/data-factory/connector-azure-sql-database)， [Azure SQL 資料倉儲](/azure/data-factory/connector-azure-sql-data-warehouse)， [GoogleBigQuery](/azure/data-factory/connector-google-bigquery)，[Oracle](/azure/data-factory/connector-oracle)，以及[檔案系統](/azure/data-factory/connector-file-system)。

## <a name="prerequisites"></a>必要條件

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* [Azure Data Explorer 叢集和資料庫](create-cluster-database-portal.md)
* 資料來源。

## <a name="create-a-data-factory"></a>建立 Data Factory

1. 選取 **建立資源**入口網站左上角的按鈕 （+） > **Analytics** > **Data Factory**。

   ![建立新的資料處理站](media/data-factory-load-data/create-adf.png)

1. 在 **新的 data factory**頁面上，提供下列欄位的值，然後選取**建立**。

    ![新增資料處理站頁面](media/data-factory-load-data/my-new-data-factory.png)

    **設定**  | **欄位描述**
    |---|---|
    | **名稱** | 輸入您的 data factory 的全域唯一名稱。 如果您收到錯誤 *「 Data factory 名稱\"LoadADXDemo\"不提供 「*，輸入不同的 data factory 的名稱。 Data Factory 成品的命名規則，請參閱 < [Data Factory 命名規則](/azure/data-factory/naming-rules)。|
    | **訂用帳戶** | 選取用來在其中建立資料處理站的 Azure 訂用帳戶。 |
    | **資源群組** | 選取 **新建**，然後輸入新的資源群組的名稱。 選取 **使用現有**，如果您有現有的資源群組。 |
    | **版本** | 選取 [V2] |
    | **位置** | 選取資料處理站的位置。 只有受到支援的位置會顯示在下拉式清單中。 資料處理站所使用的資料存放區可以是其他位置或地區。 |
    | | |

1. 若要監視的建立程序工具列上選取通知。 建立完成之後，請移至您所建立的資料處理站。 **Data Factory**首頁隨即開啟。

   ![Data Factory 首頁](media/data-factory-load-data/data-factory-home-page.png)

1. 選取 **編寫與監視**圖格以啟動另一個索引標籤中的應用程式。

## <a name="load-data-into-azure-data-explorer"></a>將資料載入 Azure 資料總管

可以將資料從載入的多種[資料存放區](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)到 Azure 資料總管。 本主題詳細說明從 Amazon S3 載入資料。

有兩種方式可將資料載入 Azure 資料總管，使用 Azure Data Factory:

* Azure Data Factory 使用者介面- [**作者** 索引標籤](/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)
* [Azure Data Factory**將資料複製**工具](/azure/data-factory/quickstart-create-data-factory-copy-data-tool)本文中使用。

### <a name="copy-data-from-amazon-s3-source"></a>將資料從 Amazon S3 複製 （來源）

1. 在 **讓我們開始**頁面上，選取**複製資料**圖格以啟動複製資料工具。

   ![複製資料工具圖格](media/data-factory-load-data/copy-data-tool-tile.png)

1. 在 [**屬性**頁面上，指定**工作名稱**，然後選取**下一步]**。

    ![複製來源屬性](media/data-factory-load-data/copy-from-source.png)

1. 在 **來源資料存放區**頁面上，按一下 **+ 建立新的連接**。

    ![資料來源建立連接](media/data-factory-load-data/source-create-connection.png)

1. 選取  **Amazon S3**，然後選取 **繼續**

    ![新增連結服務](media/data-factory-load-data/amazons3-select-new-linked-service.png)

1. 在 **新增連結服務 (Amazon S3)** 頁面上，執行下列步驟：

    ![指定 Amazon S3 已連結服務](media/data-factory-load-data/amazons3-new-linked-service-properties.png)

    * 指定**名稱**您新的連結服務。
    * 選取 **透過整合執行階段連線**從下拉式清單中的值。
    * 指定 [存取金鑰識別碼] 值。
    * 指定 [祕密存取金鑰] 值。
    * 選取 **測試連接**來測試您所建立的連結的服務連線。
    * 選取 [完成]。

1. 在 [**來源資料存放區**] 頁面上，您會看到新 AmazonS31 連線。 選取 [下一步] 。

   ![來源資料存放區所建立的連線](media/data-factory-load-data/source-data-store-created-connection.png)

1. 在 **選擇輸入的檔案或資料夾**頁面：

    1. 瀏覽至您想要複製資料夾/檔案。 選取的資料夾/檔案。
    1. 視需要選取複製行為。 保持**二進位複製**未核取。
    1. 選取 [下一步] 。

    ![選擇輸入檔案或資料夾](media/data-factory-load-data/source-choose-input-file.png)

1. 在 [**檔案格式設定**頁面上，選取相關的設定，為您的檔案，然後按一下**下一步]**。

   ![選擇輸入檔案或資料夾](media/data-factory-load-data/source-file-format-settings.png)

### <a name="copy-data-into-azure-data-explorer-destination"></a>將資料複製到 Azure 資料總管 （目的地）

Azure 資料總管新增連結的服務會建立以複製到 Azure 資料總管目的地資料表 （接收） 下面指定的資料。

1. 在 **目的地資料存放區**頁面上，您可以使用現有的資料存放區連線，或按一下，以指定新的資料存放區 **+ 建立新的連接**。

    ![目的地資料存放區頁面](media/data-factory-load-data/destination-create-connection.png)

1. 在 **新增連結服務**頁面上，選取**Azure 資料總管**，然後選取 **繼續**

    ![選取 Azure 資料總管-新增連結服務](media/data-factory-load-data/adx-select-new-linked-service.png)

1. 在 **新增連結服務 （Azure 資料總管）** 頁面上，執行下列步驟：

    ![ADX 新增連結的服務](media/data-factory-load-data/adx-new-linked-service.png)

   * 選取 **名稱**Azure 資料總管的連結服務。
   * 在 **帳戶選取方法**: 
        * 選取 **從 Azure 訂用帳戶**選項按鈕，然後選取您**Azure 訂用帳戶**帳戶。 然後，選取您**叢集**。 請注意下拉式清單將只列出屬於某位使用者的叢集。
        * 或者，選取**手動輸入**選項按鈕，然後輸入您**端點**。
    * 指定**租用戶**。
    * 請輸入**服務主體識別碼**。
    * 選取 **服務主體金鑰**按鈕，然後輸入**服務主體金鑰**。
    * 選取您**資料庫**從下拉式功能表中。 或者，選取**編輯**核取方塊，然後輸入您的資料庫名稱。
    * 選取 **測試連接**來測試您所建立的連結的服務連線。 如果您可以連接到您的設定，綠色的核取記號**連線成功**會出現。
    * 選取 **完成**完成連結的服務建立。

    > [!NOTE]
    > 服務主體由 Azure Data Factory 來存取 Azure 資料總管服務。 服務主體[建立 Azure Active Directory (Azure AD) 服務主體](/azure/azure-stack/azure-stack-create-service-principals#manage-service-principal-for-azure-ad)。 請勿使用**Azure Key Vault**方法。

1. **目的地資料存放區**隨即開啟。 您所建立的 Azure 資料總管資料連接是可供使用。 選取 [**下一步]** 來設定連線。

    ![ADX 目的地資料存放區](media/data-factory-load-data/destination-data-store.png)

1. 在 [**資料表對應**，設定目的地資料表的名稱，然後選取**下一步]**。

    ![目的地資料集資料表對應](media/data-factory-load-data/destination-dataset-table-mapping.png)

1. 在 **資料行對應**頁面：
    * 第一個對應都由 ADF 根據[ADF 結構描述對應](/azure/data-factory/copy-activity-schema-and-type-mapping)
        * 設定**資料行對應**在 Azure Data Factory 的目的地資料表。 預設對應會顯示從來源 ADF 目的地資料表。
        * 取消選取您不需要定義您的資料行對應的資料行。
    * 此表格式資料擷取至 Azure Data Explorer 時，就會發生的第二個對應。 對應會根據[CSV 對應規則](/azure/kusto/management/mappings#csv-mapping)。 請注意，即使來源資料不是以 CSV 格式，ADF 已將資料轉換成表格式格式，因此，CSV 對應是在這個階段只相關對應。
        * 底下**Azure 資料總管 (Kusto) 接收屬性**加入相關**擷取對應名稱**（選擇性） 讓該資料行對應可以使用。
        * 如果**擷取對應的名稱**未指定，定義中的"依名稱 」 對應順序**資料行對應**區段就會發生。 「 依名稱 」 對應時，Azure 資料總管會嘗試擷取資料，以 「 依資料行位置 」 的順序 （對應的位置為預設值）。
    * 選取**下一步**

    ![目的地資料集資料行對應](media/data-factory-load-data/destination-dataset-column-mapping.png)

1. 在 [設定] 頁面中：
    * 設定的相關**錯誤容錯設定**。
    * **效能設定**:啟用預備環境不適用。 **進階設定**包含成本考量。 保持原狀如果沒有特定需求。
    * 選取 [下一步] 。

    ![複製資料設定](media/data-factory-load-data/copy-data-settings.png)

1. 在 [**摘要**，檢閱設定，然後選取**下一步]**。

    ![複製資料摘要](media/data-factory-load-data/copy-data-summary.png)

1. 在 **部署頁面**:
    * 選取 **監視器**轉為**監視器**索引標籤，查看管線 （進度、 錯誤和資料的流程） 的狀態。
    * 選取 **編輯管線**編輯連結的服務、 資料集和管線。
    * 選取 **完成**完整複本的資料工作

    ![部署頁面](media/data-factory-load-data/deployment.png)

## <a name="next-steps"></a>後續步驟

* 深入了解[Azure 資料總管連接器](/azure/data-factory/connector-azure-data-explorer)Azure Data Factory 中。

* 深入了解編輯連結的服務、 資料集和中的管線[Data Factory UI](/azure/data-factory/quickstart-create-data-factory-portal)。

* 深入了解[Azure 資料總管查詢](/azure/data-explorer/web-query-data)資料查詢。
