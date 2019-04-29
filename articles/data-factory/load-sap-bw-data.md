---
title: 使用 Azure Data Factory 從 SAP Business Warehouse 中載入資料 |Microsoft Docs
description: 使用 Azure Data Factory 從 SAP Business Warehouse (BW) 複製資料
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: jingwang
ms.openlocfilehash: 9a123ed45b5857aa40fc9853a95c528833ba8aa9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60548665"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 SAP Business Warehouse 複製資料

本文說明如何使用 Azure Data Factory 將資料從 SAP Business Warehouse (BW) 透過開啟中樞複製到 Azure Data Lake 儲存體 Gen2。 您可以使用類似的程序，將資料複製到其他[支援的接收資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。

> [!TIP]
> 如需將資料從 SAP BW，包括 SAP BW Open Hub 整合和差異擷取流程中，複製的一般資訊，請參閱[使用 Azure Data Factory 將資料複製從 SAP Business Warehouse，透過開啟中樞](connector-sap-business-warehouse-open-hub.md)。

## <a name="prerequisites"></a>必要條件

- **Azure Data Factory**:如果您沒有帳戶，請依照下列步驟來[建立資料處理站](quickstart-create-data-factory-portal.md#create-a-data-factory)。

- **SAP BW 開啟中樞目的地 (OHD) 使用的目的型別 「 資料庫資料表 」**:若要建立 OHD 或檢查您 OHD 已正確設定，Data Factory 整合，請參閱[SAP BW 開放式中心目的地組態](#sap-bw-open-hub-destination-configurations)一節。

- **SAP BW 使用者需要下列權限**:

  - 遠端函式呼叫 (RFC) 的授權和 SAP BW。
  - 「 執行 」 活動的權限**S_SDSAUTH**授權物件。

- **A[自我裝載整合執行階段 (IR)](concepts-integration-runtime.md#self-hosted-integration-runtime)與 SAP.NET connector 3.0**。 請依照下列這些設定步驟：

  1. 安裝並註冊為自我裝載的整合執行階段，3.13 或更新版本。 （此程序在本文稍後說明）。

  2. 下載[64 位元 SAP 連接器，適用於 Microsoft.NET 3.0](https://support.sap.com/en/product/connectors/msnet.html)從 SAP 的網站，並將它安裝在自我裝載 IR 的同一部電腦上 在安裝期間，請確定您選取**組件安裝到 GAC**中**選擇性設定步驟**對話方塊中，下列影像所示：

     ![設定 SAP.NET 連接器 對話方塊](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>從 SAP BW Open Hub 中執行的完整複本

在 Azure 入口網站中，移至您的 data factory。 選取 **編寫與監視**個別索引標籤中開啟 Data Factory UI。

1. 在 **讓我們開始**頁面上，選取**複製資料**以開啟 複製資料工具。

2. 在 [**屬性**頁面上，指定**工作名稱**，然後選取**下一步]**。

3. 在 **來源資料存放區**頁面上，選取 **+ 建立新的連接**。 選取  **SAP BW Open Hub**從連接器資源庫，然後選取**繼續**。 若要篩選的連接器，您可以輸入**SAP**在搜尋方塊中。

4. 在 **指定 SAP BW Open Hub 連接**頁面上，依照下列步驟來建立新的連接。

   ![建立 SAP BW Open Hub 連結的服務頁面](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. 從**透過整合執行階段連線**清單中，選取現有的自我裝載 ir。 或者，如果您還沒有帳戶，請建立一個選擇。

      若要建立新的自我裝載的 IR，請選取 **+ 新增**，然後選取**自我裝載**。 請輸入**名稱**，然後選取**下一步**。 選取 **快速安裝**目前的電腦上安裝，或遵循**手動安裝**所提供的步驟。

      中所述[必要條件](#prerequisites)，請確定您有適用於 Microsoft.NET 3.0 安裝自我裝載整合執行階段執行所在的同一部電腦上使用 SAP 連接器。

   2. 填寫 SAP BW**伺服器名稱**，**系統編號**，**用戶端識別碼****語言**(如果以外**EN**)**使用者名**，並**密碼**。

   3. 選取 **測試連接**若要驗證的設定，然後選取**完成**。

   4. 建立新的連線。 選取 [下一步] 。

5. 在 **選取 開啟中樞目的地**頁面上，瀏覽開啟 SAP BW 中可用的中樞目的地。 選取以複製資料，然後選取 OHD**下一步**。

   ![選取 SAP BW 開放式中心目的地資料表](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. 篩選條件，如果您需要指定其中一個。 如果您 OHD 只包含資料的單一資料傳輸程序 (DTP) 執行以單一要求識別碼，或您要確定您 DTP 完畢，而且您想要複製的資料清除**排除上次要求**核取方塊。

   深入了解這些設定[SAP BW 開放式中心目的地組態](#sap-bw-open-hub-destination-configurations)一節。 選取 **驗證**来再次將傳回的資料。 然後，選取 [下一步]。

   ![設定 SAP BW Open Hub 篩選器](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. 在 **目的地資料存放區**頁面上，選取 **+ 建立新的連接** > **Azure Data Lake 儲存體 Gen2**  >  **繼續**。

8. 在 **指定的 Azure Data Lake 儲存體連接**頁面上，依照下列步驟來建立連線。

   ![建立 ADLS Gen2 連結的服務頁面](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. 選取 將 Data Lake 儲存體能夠 Gen2 的帳戶，從**名稱**下拉式清單。
   2. 選取 [完成] 以建立連線。 然後，選取 [下一步]。

9. 在 **選擇輸出檔案或資料夾**頁面上，輸入**copyfromopenhub**做為輸出資料夾名稱。 然後，選取 [下一步]。

   ![選擇輸出資料夾頁面](media/load-sap-bw-data/choose-output-folder.png)

10. 在 [**檔案格式設定**頁面上，選取**下一步]** 以使用預設設定。

    ![指定接收格式 頁面](media/load-sap-bw-data/specify-sink-format.png)

11. 在 **設定**頁面上，展開**效能設定**。 輸入的值**複製平行處理原則程度**5 以平行方式載入從 SAP BW 等。 然後，選取 [下一步]。

    ![設定 複製設定](media/load-sap-bw-data/configure-copy-settings.png)

12. 在 **摘要**頁面上，檢閱設定。 然後，選取 [下一步]。

13. 在 **部署**頁面上，選取**監視器**來監視管線。

    ![部署頁面](media/load-sap-bw-data/deployment.png)

14. 請注意，**監視器**頁面左側的索引標籤會自動選取。 **動作**資料行包含連結可供檢視活動執行詳細資料，以及重新執行管線。

    ![監視檢視的管線](media/load-sap-bw-data/pipeline-monitoring.png)

15. 若要檢視與此管線執行相關聯的活動執行，請選取**檢視活動執行**中**動作**資料行。 管線中只有一個活動 (複製活動)，所以您只會看到一個項目。 若要切換回管線執行檢視，請選取**管線**頂端的連結。 選取 [重新整理] 即可重新整理清單。

    ![活動監控 畫面](media/load-sap-bw-data/activity-monitoring.png)

16. 若要監視每個複製活動執行詳細資料，請選取**詳細資料**連結，也就是下面的眼鏡圖示**動作**[活動監控] 檢視中。 可用的詳細資料包含的資料磁碟區從來源複製到接收、 資料輸送量、 執行步驟和持續時間和使用的組態。

    ![活動監控 詳細資料](media/load-sap-bw-data/activity-monitoring-details.png)

17. 若要檢視**最大的要求識別碼**，請回到活動監控檢視，然後選取**輸出**之下**動作**。

    ![活動輸出畫面](media/load-sap-bw-data/activity-output.png)

    ![活動輸出詳細資料檢視](media/load-sap-bw-data/activity-output-details.png)

## <a name="do-an-incremental-copy-from-sap-bw-open-hub"></a>從 SAP BW Open Hub 進行增量複本

> [!TIP]
> 請參閱[SAP BW Open Hub 連接器差異擷取流程](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow)若要了解 Data Factory 中的 SAP BW Open Hub 連接器從 SAP BW 所複製的增量資料。 這篇文章也有助於您了解基本的連接器設定。

現在，讓我們繼續來設定從 SAP BW Open Hub 增量複本。

增量複本會使用 「 高水位線 」 機制為基礎**要求識別碼**。 SAP BW 開放式中心目的地自動產生該識別碼的 DTP。 下圖顯示此工作流程：

![累加複製工作流程的流程圖](media/load-sap-bw-data/incremental-copy-workflow.png)

在 data factory**讓我們開始**頁面上，選取**從範本建立管線**使用內建的範本。

1. 搜尋**SAP BW**尋找並選取**Incremental 從 SAP BW 複製至 Azure Data Lake 儲存體 Gen2**範本。 此範本會將資料複製到 Azure Data Lake 儲存體 Gen2。 您可以使用類似的工作流程，將複製到其他的接收類型。

2. 在範本的主要頁面上，選取或建立下列的三個連線，然後選取**使用此範本**視窗右下角。

   - **Azure Blob 儲存體**:在此逐步解說中，我們必須使用 Azure Blob 儲存體來儲存高水位線，也就是*max 複製要求識別碼*。
   - **SAP BW 開啟中樞**:這是要從其中複製資料的來源。 請參閱先前的完整複製逐步解說中，如需詳細的設定。
   - **Azure Data Lake 儲存體 Gen2**:這是要將資料複製到接收。 請參閱先前的完整複製逐步解說中，如需詳細的設定。

   ![從 SAP BW 範本的增量複製](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. 此範本會產生具有下列三個活動的管線，並使其鏈結上成功：*查閱*，*將資料複製*，以及*Web*。

   移至管線**參數** 索引標籤。您會看到您需要提供的所有組態。

   ![從 SAP BW 設定增量複製](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**:指定 Open Hub 資料表名稱，要從其中複製資料。

   - **ADLSGen2SinkPath**:指定將資料複製到目的地 Azure 資料湖儲存體 Gen2 路徑。 如果路徑不存在，Data Factory 複製活動會建立在執行期間的路徑。

   - **HighWatermarkBlobPath**:指定的路徑來儲存高水位線值，例如`container/path`。

   - **HighWatermarkBlobName**:指定 blob 名稱，來儲存高水位線值，例如`requestIdCache.txt`。 在 Blob 儲存體，移至對應的路徑的 HighWatermarkBlobPath + HighWatermarkBlobName，這類*container/path/requestIdCache.txt*。 建立 blob 以 0 的內容。

      ![Blob 內容](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**:此範本中，我們會使用 WebActivity 來呼叫 Azure Logic Apps，若要在 Blob 儲存體設定高水位線值。 或者，您可以使用 Azure SQL Database 來儲存它。 您可以使用預存程序活動來更新值。

      您必須先建立邏輯應用程式，下列影像所示。 然後，貼入**HTTP POST URL**。

      ![邏輯應用程式設定](media/load-sap-bw-data/logic-app-config.png)

      1. 移至 Azure 入口網站。 選取新**Logic Apps**服務。 選取  **+ 空白邏輯應用程式**以前往**Logic Apps 設計工具**。

      2. 建立的觸發程序**HTTP 要求時收到**。 指定的 HTTP 要求主體如下所示：

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               },
               "type": "object"
            }
         }
         ```

      3. 新增**建立 blob**動作。 針對**資料夾路徑**並**Blob 名稱**，使用您先前設定中的相同值**HighWatermarkBlobPath**和**HighWatermarkBlobName**.

      4. 選取 [ **儲存**]。 然後，將複製的值**HTTP POST URL** Data Factory 管線中使用。

4. 提供 Data Factory 管線參數之後，請選取**偵錯** > **完成**叫用執行以驗證設定。 或者，選取**全部發佈**若要發行的變更，然後選取**觸發程序**執行測試回合。

## <a name="sap-bw-open-hub-destination-configurations"></a>SAP BW 開放式中心目的地組態

本節將介紹設定 SAP BW 側邊，以使用 Data Factory 中的 SAP BW Open Hub 連接器，將資料複製。

### <a name="configure-delta-extraction-in-sap-bw"></a>設定 SAP BW 中的差異擷取

如果您需要歷程記錄的複製和增量複本或是只有增量複本，請在 SAP BW 設定差異擷取。

1. 建立的開放式中心目的地。 您可以建立 OHD SAP 交易 RSA1，會自動建立必要的轉換和資料傳輸程序中。 套用下列設定：

   - **ObjectType**:您可以使用任何物件類型。 在這裡，我們使用**InfoCube**做為範例。
   - **目的型別**:選取 **資料庫資料表**。
   - **資料表的索引鍵**:選取 **技術的索引鍵**。
   - **擷取**：選取 **插入資料表中保留的資料和插入資料錄**。

   ![建立 SAP BW OHD 差異擷取對話方塊](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![建立 SAP BW OHD delta2 擷取對話方塊](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   您可能會增加平行 DTP 執行 SAP 工作程序數目：

   ![create-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. 排程中處理序鏈結 DTP。

   如果您尚未已壓縮的所需的資料列，僅適用於 cube 的差異 DTP。 請確定 BW cube 壓縮不執行之前 DTP Open Hub 資料表。 若要這樣做最簡單的方式是將 DTP 整合到您現有的處理序鏈結。 在下列範例中，（以 OHD) DTP 插入之間的處理序鏈結*調整*（彙總套件） 和*摺疊*（cube 壓縮） 的步驟。

   ![建立 SAP BW 處理序鏈結流程圖](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>設定 SAP BW 中的完整擷取

除了差異擷取，您可以使用相同的 SAP BW InfoProvider 完整擷取。 這通常適用於 如果您想要執行完整複本，但不是累加，或您想要[重新同步處理差異擷取](#resync-delta-extraction)。

您不能有一個以上的 DTP 的相同 OHD。 因此，您必須建立其他 OHD 差異擷取前。

![建立完整的 SAP BW OHD](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

完整載入 OHD，選擇不同的選項比差異擷取：

- 在 OHD:設定**擷取**選項設定為**刪除的資料和插入記錄**。 否則，資料也將擷取許多次，當您重複 BW 處理序鏈結中的 DTP。

- 在 DTP:設定**擷取模式**要**完整**。 您必須將變更從自動建立的 DTP**差異**要**完整**之後立即 OHD 建立時，此映像所示：

   ![建立 SAP BW OHD 為 「 完整 」 擷取設定 對話方塊](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- 在 Data Factory 的 BW Open Hub 連接器：關閉**排除上次要求**。 否則，執行任何動作將擷取。

您通常會以手動方式執行完整的 DTP。 或者，您可以建立處理序鏈結完整 DTP。 它通常是獨立於您現有的處理序鏈結的另一個鏈結。 在任一情況下，*確定 DTP 會完成啟動之前擷取，使用 Data Factory 複製*。 否則，只有部分的資料將會複製。

### <a name="run-delta-extraction-the-first-time"></a>執行差異擷取第一次

第一次的差異擷取就技術上來說*完整擷取*。 它會將資料複製時，預設情況下，SAP BW Open Hub 連接器會排除最後一個要求。 針對第一次的差異擷取，沒有擷取資料的 Data Factory 複製活動所直到後續 DTP 產生差異資料中的資料表，並提供個別的要求識別碼。 有兩種方式可避免這種情況：

- 關閉**排除上次要求**第一次的差異擷取的選項。 請確定第一次的差異 DTP 完畢之前啟動差異擷取第一次。
-  使用程序重新同步的差異擷取、 下一節中所述。

### <a name="resync-delta-extraction"></a>重新同步處理差異擷取

下列情況下變更 SAP BW cube 中的資料，但不是會考慮差異 DTP:

- SAP BW 選擇性刪除 （資料列使用任何篩選條件）
- SAP BW （屬於錯誤的要求） 的要求刪除

SAP 開放式中心目的地不是資料超市控制資料中的目標 （2015年之後的所有 SAP BW 支援封裝）。 因此，您可以刪除 cube 中的資料，而不需要變更 OHD 中的資料。 然後，您必須重新同步使用 Data Factory 之 cube 的資料：

1. （透過 SAP 中使用完整的 DTP），Data Factory 中執行完整的擷取。
2. 刪除差異 DTP Open Hub 資料表中的所有資料列。
3. 設定狀態的差異 DTP 要**已提取**。

在此之後，所有後續的差異 DTPs 和 Data Factory 差異擷取如預期般運作。

若要設定的差異 DTP 狀態來**已提取**，您可以使用下列選項來手動執行差異 DTP:

    *No Data Transfer; Delta Status in Source: Fetched*

## <a name="next-steps"></a>後續步驟

深入了解 SAP BW Open Hub 連接器支援：

> [!div class="nextstepaction"]
>[SAP 商務倉儲 Open Hub 連接器](connector-sap-business-warehouse-open-hub.md)
