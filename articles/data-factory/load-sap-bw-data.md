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
ms.openlocfilehash: 9458903378576a50db9be92b9377987829e1ba41
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58200152"
---
# <a name="load-data-from-sap-business-warehouse-bw-by-using-azure-data-factory"></a>使用 Azure Data Factory 將資料從 SAP Business Warehouse (BW)

本文將說明逐步解說如何使用 Data Factory_載入資料從 SAP Business Warehouse (BW) 開啟中樞透過 Azure Data Lake 儲存體 Gen2_。 您可以依照類似的步驟，將資料複製到其他[支援的接收資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。 

> [!TIP]
> 請參閱[SAP BW Open Hub 連接器文件](connector-sap-business-warehouse-open-hub.md)在一般情況下從 SAP BW 複製資料，包括 SAP BW Open Hub 整合和差異擷取流程的簡介。

## <a name="prerequisites"></a>必要條件

- **Azure Data Factory (ADF):** 如果您沒有 data factory，請依照 「[建立資料處理站](quickstart-create-data-factory-portal.md#create-a-data-factory)」 一節，以建立一個。 

- **SAP BW 開啟中樞目的地 (OHD) 與 「 資料庫資料表 」 目的地類型。** 請遵循[SAP BW 開放式中心目的地組態](#sap-bw-open-hub-destination-configurations)建立一個，或確認您現有的 OHD 是否已正確設定為使用 ADF 整合 區段。

- **SAP BW 所使用的使用者必須具有下列權限：**

  - RFC 和 SAP BW 的授權。
  - 權限 」**執行**「 活動的授權物件 」**S_SDSAUTH**"。

- **[自我裝載整合執行階段](concepts-integration-runtime.md#self-hosted-integration-runtime)使用 SAP.NET connector 3.0 所需**。 以下是詳細必須完成的準備工作：

  1. 安裝和註冊自我裝載整合執行階段版本 > = 3.13 （涵蓋在以下的逐步解說）。 

  2. 下載[64 位元 SAP.NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html)從 SAP 的網站，並將它安裝在自我裝載 IR 機器上。  當安裝，請在 「 選擇性設定步驟 」 視窗中，請確定您選取 「**組件安裝到 GAC**」 選項，如下圖所示。

     ![設定 SAP.NET Connector](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="full-copy-from-sap-bw-open-hub"></a>從 SAP BW 開啟中樞的完整複本

Azure 入口網站中，移至您的 data factory]-> [選取**編寫與監視**啟動 ADF UI 中的個別索引標籤。 

1. 在 [現在就開始吧] 頁面中，選取 [複製資料] 以啟動複製資料工具。 

2. 在 [**屬性**頁面上，指定的名稱**工作名稱**欄位，然後選取**下一步]**。

3. 在上**來源資料存放區**頁面上，按一下 **+ 建立新的連接**-> 選取**SAP BW Open Hub**從連接器資源庫-> 選取**繼續**. 您可以篩選連接器的 [搜尋] 方塊中輸入 「 SAP 」。

4. 在 [**指定 SAP BW Open Hub 連接**] 頁面上， 

   ![建立 SAP BW Open Hub 連結服務](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. 選擇**透過整合執行階段連線**： 按一下下拉式清單選取現有的自我裝載 IR，或如果您沒有設定的自我裝載 IR，請建立一個。 

      若要建立新的請按一下 **+ 新增**下拉式清單中]-> [選取的型別**自我裝載**]-> [指定**名稱**，按一下 [**下一步]** ]-> [選擇**快速安裝**目前的電腦上安裝，或遵循**手動安裝**那里步驟。

      中所述[必要條件](#prerequisites)，請確定您也可以**SAP.NET connector 3.0**安裝自我裝載 IR 執行所在的同一部電腦上。

   2. 指定 SAP BW**伺服器名稱**，**系統編號**，**用戶端識別碼****語言**（如果除了 EN 之外）， **的使用者名稱**，並**密碼**。

   3. 按一下 [測試連線] 以驗證設定，然後選取 [完成]。

   4. 您會看到新的連線隨即建立。 選取 [下一步] 。

5. 在 **選取 Open Hub 目的地**頁面上，瀏覽 開啟位於您的 SAP BW 的中樞目的地，然後選取您想要複製資料，然後按一下 **下一步**。

   ![選取 SAP BW Open Hub 資料表](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. 如有需要請指定篩選條件。 如果您的開放式中心目的地只會包含來自單一要求識別碼的單次資料傳輸程序 (DTP) 執行的資料，或您確定您 DTP 已完成，而且想要複製的所有資料，請取消選取**排除上次要求**。 您可以了解更多有關如何使用這些設定與 SAP BW 中的組態[SAP BW 開放式中心目的地組態](#sap-bw-open-hub-destination-configurations)一節。 按一下 [ **Validate**若要再次檢查資料傳回，然後選取**下一步]**。

   ![設定 SAP BW Open Hub 篩選器](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. 在**目的地資料存放區**頁面上，按一下 **+ 建立新的連接**，然後選取**Azure Data Lake 儲存體 Gen2**，然後選取**繼續**.

8. 在 [**指定的 Azure Data Lake 儲存體連接**] 頁面上， 

   ![建立 ADLS Gen2 連結服務](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. 從 [儲存體帳戶名稱] 下拉式清單選取您的 Data Lake 儲存體 Gen2 能夠帳戶。
   2. 選取 [完成] 以建立連線。 然後，選取 [下一步]。

9. 在 [**選擇輸出檔案或資料夾**頁面上，輸入 「 copyfromopenhub"做為輸出資料夾名稱，然後選取**下一步]**。

   ![選擇輸出資料夾](media/load-sap-bw-data/choose-output-folder.png)

10. 在 [**檔案格式設定**頁面上，選取**下一步]** 以使用預設設定。

    ![指定接收格式](media/load-sap-bw-data/specify-sink-format.png)

11. 在 **設定**頁面上，展開**效能設定**，並設定**複製平行處理原則程度**例如，若要以平行方式載入從 SAP BW 的 5。 单击“下一步”。

    ![設定 複製設定](media/load-sap-bw-data/configure-copy-settings.png)

12. 在 [**摘要**頁面上，檢閱設定，然後選取**下一步]**。

13. 在 **部署**頁面上，選取**監視器**來監視管線。

    ![部署頁面](media/load-sap-bw-data/deployment.png)

14. 請注意，系統會自動選取左側的 [監視] 索引標籤。 [動作] 資料行中會有連結可供檢視活動執行詳細資料，以及重新執行管線：

    ![監視管線](media/load-sap-bw-data/pipeline-monitoring.png)

15. 若要檢視與此管線執行相關聯的活動執行，請選取 [動作] 資料行中的 [檢視活動執行] 連結。 管線中只有一個活動 (複製活動)，所以您只會看到一個項目。 若要切換回 [管線執行] 檢視，請選取頂端的 [管線] 連結。 選取 [重新整理] 即可重新整理清單。

    ![活動監控](media/load-sap-bw-data/activity-monitoring.png)

16. 若要監視每個複製活動的執行詳細資料，請選取活動監控檢視中 [動作] 底下的 [詳細資料] 連結 (眼鏡圖示)。 您可以監視的詳細資料包括從來源複製到接收的資料量、資料輸送量、執行步驟與對應的持續期間，以及所使用的設定：

    ![活動監控 詳細資料](media/load-sap-bw-data/activity-monitoring-details.png)

17. 檢閱**最大的要求識別碼**複製。 返回活動監控 檢視中，按一下**輸出**下方**動作**。

    ![活動輸出](media/load-sap-bw-data/activity-output.png)

    ![活動輸出詳細資料](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>從 SAP BW Open Hub 的增量複製

> [!TIP]
>
> 請參閱[SAP BW Open Hub 連接器差異擷取流程](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow)若要深入了解 SAP BW 複製增量資料，並從了解連接器相關的基本概念開始閱讀這篇文章 ADF SAP BW Open Hub 連接器的運作方式組態。

現在，讓我們繼續來設定從 SAP BW Open Hub 增量複本。 

增量複本會使用高水位線為基礎的機制**要求識別碼**SAP BW 開放式中心目的地中由 DTP 自動產生。 下圖會說明這種方法的工作流程：

![累加複製工作流程](media/load-sap-bw-data/incremental-copy-workflow.png)

在 ADF ui**讓我們開始**頁面上，選取**從範本建立管線**運用內建的範本。 

1. 搜尋來尋找並選取範本名稱的 「 SAP BW 」 **Incremental 從 SAP BW 複製至 Azure Data Lake 儲存體 Gen2**。 此範本會將資料複製到 ADLS Gen2，您可以稍後再遵循類似的流程，將複製到其他的接收類型。

2. 在範本的主要頁面上，選取或建立下列三個連線，然後選取**使用此範本**右下方。

   - **Azure Blob**： 在此逐步解說中，我們可以使用 Azure Blob 儲存高水位線，也就是最大的複製的要求識別碼。
   - **SAP BW Open Hub**： 您要從其中複製資料的來源。 請參閱先前的完整複本逐步解說中，詳細的組態。
   - **ADLS Gen2**： 若要將資料複製到接收器。 請參閱先前的完整複本逐步解說中，詳細的組態。

   ![從 SAP BW 範本的增量複製](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. 此範本會產生具有三個活動的管線**查閱、 複製資料及 Web** -，並使其鏈結上成功。 移至管線**參數**索引標籤上，您會看到您需要提供的所有組態。

   ![從 SAP BW 設定增量複製](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**： 指定要從其中複製資料的 Open Hub 資料表名稱。

   - **ADLSGen2SinkPath**： 指定要將資料複製到目的地 ADLS Gen2 路徑。 如果路徑不存在，則 ADF 複製活動會建立一個在執行期間。

   - **HighWatermarkBlobPath**： 指定的路徑來儲存高水位線值，例如`container/path`。 

   - **HighWatermarkBlobName**： 指定 blob 名稱，例如儲存高水位線值`requestIdCache.txt`。 在您 blob 儲存體，在於 HighWatermarkBlobPath + HighWatermarkBlobName 的對應路徑例如"*container/path/requestIdCache.txt*」，以 0 的內容中建立 blob。 

      ![Blob 內容](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**： 此範本中，我們可以使用 Web 活動呼叫邏輯應用程式，若要在 Blob 儲存體設定高水位線值。 或者，您也可以使用 SQL database 來儲存它，並使用預存程序活動來更新值。 

      這裡，您必須先建立邏輯應用程式，如下所示，然後複製**HTTP POST URL**至這個欄位。 

      ![邏輯應用程式設定](media/load-sap-bw-data/logic-app-config.png)

      1. 移至 Azure 入口網站-> 新增**Logic Apps**服務-> 按一下**空白邏輯應用程式 +** 移至**Logic Apps 設計工具**。

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

      3. 新增的動作**建立 blob**。 [資料夾路徑] 和 [Blob 名稱]，請使用相同的值在上述的 HighWatermarkBlobPath 和 HighWatermarkBlobName 中設定。

      4. 按一下 **儲存**，然後將複製的值**HTTP POST URL** ADF 管線中使用。

4. ADF 管線參數提供的所有值之後，您可以按一下**偵錯** -> **完成**叫用執行以驗證設定。 或者，您可以選取**全部發佈**若要發行的所有變更，然後按一下**觸發程序**執行測試回合。

## <a name="sap-bw-open-hub-destination-configurations"></a>SAP BW 開放式中心目的地組態

本節介紹 SAP BW 端所需的設定，才能在 ADF 中使用 SAP BW Open Hub 連接器，將資料複製。

### <a name="configure-delta-extraction-in-sap-bw"></a>設定 SAP BW 中的差異擷取

如果您需要歷程記錄的複製和增量複本或只是增量複本，設定差異擷取 SAP BW 中。

1. 建立開啟的中心目的地 (OHD)

   您可以建立 OHD SAP 交易 RSA1，會自動建立必要的轉換和資料傳輸程序 (DTP) 中。 套用下列設定：

   - 物件型別可以是任何。 這裡我們使用 InfoCube 做為範例。
   - **目的地類型：***資料庫資料表*
   - **資料表索引鍵：***技術的索引鍵*
   - **擷取：***插入資料表中保留的資料和插入記錄*

   ![建立 SAP BW OHD 差異擷取](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![create-sap-bw-ohd-delta2](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   您可能會增加平行 DTP 執行 SAP 工作程序數目：

   ![create-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. 排程中處理序鏈結 DTP

   Cube 的差異 DTP 僅適用於時所需的資料列有尚未壓縮尚未。 因此，您必須確定 DTP 之前，不將 BW Cube 壓縮執行 Open Hub 資料表。 最簡單的方式將此 DTP 整合您現有的處理序鏈結。 在下列範例中，插入步驟之間的處理序鏈結 （以 OHD) DTP 調整 （彙總套件） 和摺疊 （Cube 壓縮）。

   ![create-sap-bw-process-chain](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>設定 SAP BW 中的完整擷取

除了差異擷取中，您可能想要有相同的 InfoProvider 完整擷取。 它通常會套用如果您想要執行完整累加無須複製，或您想要[重新同步處理差異擷取](#re-sync-delta-extraction)。

您不得相同 OHD 一個以上的 DTP。 因此，您需要建立額外的 OHD 則差異擷取。

![create-sap-bw-ohd-full](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

完整載入 OHD，選擇不同的選項比差異擷取：

- 在 OHD: 「 擷取 」 將選項設定為"*刪除資料和插入記錄*"。 否則會多次時重複 DTP BW 處理序鏈結中的擷取資料。

- 在 DTP： 將 [擷取模式] 設定為"*完整*"。 已建立 OHD 之後，必須從差異變更會自動建立的 DTP 為 Full:

   ![create-sap-bw-ohd-full2](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- 在 ADF SAP BW Open Hub 連接器： 關閉選項 」*排除最後一個要求*"。 否則不會被擷取。 

您通常會以手動方式執行完整的 DTP。 或您也可以建立處理序鏈結的完整 DTP-通常是獨立於您現有的處理序鏈結的個別處理序鏈結。 在任一情況下，您必須**確定 DTP 完成之前啟動使用 ADF 複製擷取**，否則，請將複製部分資料。

### <a name="run-delta-extraction-the-first-time"></a>執行差異擷取第一次

第一次的差異擷取就技術上來說**完整擷取**。 複製資料時，注意預設 ADF SAP BW Open Hub 連接器不包括最後一個要求。 在第一次，在 ADF 複製活動中，差異擷取的情況下不會擷取資料之前後續 DTP 產生差異資料中的資料表，並提供個別的要求識別碼。 雖然有兩種可能的方式，若要避免這種情況：

1. 關閉選項 「 排除上次要求 」 的第一個差異擷取在此情況下，您必須先確定第一次的差異 DTP 已完成第一次啟動差異擷取之前
2. 使用重新同步處理差異擷取，如下所述的程序。

### <a name="re-sync-delta-extraction"></a>重新同步處理差異擷取

有少數的情況下，SAP BW Cube 中的資料變更，但不會考慮差異 DTP:

- SAP BW 選擇性刪除 （資料列使用任何篩選條件）
- SAP BW 要求刪除 （屬於錯誤的要求）

SAP 開放式中心目的地不是資料超市控制資料目標 （在所有 SAP BW 支援套件自 2015 年）。 因此，就可以刪除 cube 中的資料，而不需要變更 OHD 中的資料。 在此情況下，您必須重新同步處理的資料 cube 的 ADF 中的資料執行下列步驟：

1. 在 ADF 中執行完整的擷取，（透過 SAP 中使用完整的 DTP）
2. 刪除差異 DTP Open Hub 資料表中的所有資料列
3. 將差異 DTP 狀態設為已提取

在此之後，所有後續的差異 DTPs 和 ADF 差異擷取正常運作如預期般運作。

您可以執行使用下列選項，以手動方式差異 DTP，已提取到設定差異 DTP 的狀態：「*會傳輸任何資料;在來源中的差異狀態：擷取*"。

## <a name="next-steps"></a>後續步驟

請前往下列文章，以了解 SAP BW Open Hub 連接器支援： 

> [!div class="nextstepaction"]
>[SAP 商務倉儲 Open Hub 連接器](connector-sap-business-warehouse-open-hub.md)
