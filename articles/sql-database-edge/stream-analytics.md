---
title: 使用 SQL Database 的 DAC 封裝，以及 Azure SQL Database Edge 串流分析作業 |Microsoft Docs
description: 瞭解如何在 SQL Database Edge 中使用串流分析作業
keywords: sql database 邊緣、串流分析、sqlpackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: c3ed84e06f693925ed8b484070616e223929e401
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2019
ms.locfileid: "74108755"
---
# <a name="using-sql-database-dac-package-and-stream-analytics-job-with-sql-database-edge"></a>使用 SQL Database DAC 封裝和串流分析作業搭配 SQL Database Edge

Azure SQL Database Edge 預覽是專為 IoT 和 Edge 部署而優化的關係資料庫引擎。 它是以最新版本的 Microsoft SQL Server 資料庫引擎為基礎，可提供領先業界的效能、安全性和查詢處理功能。 隨著 SQL Server 的領先業界關係資料庫管理功能，Azure SQL Database Edge 為即時分析和複雜的事件處理提供了內建的串流功能。

Azure SQL Database Edge 也提供 SQLPackage 的原生執行，可讓使用者在部署 SQL Database Edge 期間部署[SQL DATABASE DAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications)封裝。

Azure SQL Database Edge 會透過 IoT Edge 模組之模組對應項*的 [所需屬性*] 選項，公開兩個選擇性參數。

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|欄位 | 描述 |
|------|-------------|
| SQLPackage | 包含 SQL Database DAC 封裝的 * .zip 檔案 Azure Blob 儲存體 URI。
| ASAJobInfo | ASA Edge 作業的 Azure Blob 儲存體 URI。 如需發佈 ASA Edge 作業的詳細資訊，請參閱[發佈適用于 SQL Database 邊緣的 Asa edge 作業](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge)。

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>搭配 SQL Database Edge 使用 SQL Database DAC 封裝

若要使用 SQL Database 的 DAC 封裝（* .dacpac）搭配 SQL Database Edge，請遵循下列步驟。

1. 建立或解壓縮 SQL Database DAC 封裝。 您可以使用[從現有的資料庫解壓縮 DAC](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/)中所述的概念，來產生現有 SQL Database 的 DacPac。

2. 將 * *.dacpac*壓縮並上傳至 Azure Blob 儲存體帳戶。 如需將檔案上傳至 Azure Blob 儲存體的詳細資訊，請參閱[上傳、下載及列出具有 Azure 入口網站的 blob](../storage/blobs/storage-quickstart-blobs-portal.md)。

3. 使用 Azure 入口網站產生 zip 檔案的 SAS 簽章。 如需詳細資訊，請參閱[使用共用存取簽章（SAS）委派存取](../storage/common/storage-sas-overview.md)。

4. 更新 SQL Database Edge 模組設定，以包含 DAC 封裝的 SAS URI。 更新 SQL Database Edge 模組

    1. 在 Azure 入口網站上，流覽至您的 IoT 中樞部署。

    2. 在左側窗格中，按一下 [ **IoT Edge**]。

    3. 在 [ **IoT Edge** ] 頁面上，尋找並按一下 SQL Database Edge 模組部署所在的 IoT Edge。

    4. 在 [ *IoT Edge 裝置*裝置] 頁面上，按一下 [**設定模組**]。 

    5. 在 [**設定模組**]*頁面上，按一下 [針對 SQL Database* Edge 模組進行設定]。 

    6. 在 [ **IoT Edge 自訂模組**] 窗格中，選取 [*設定模組對應項所需的屬性*]，然後更新所需的屬性，以包含 SQLPackage 選項的 URI，如下列範例所示。 

        > [!NOTE]
        > 以下的 SAS URI 僅供說明之用。 請以您部署中的實際 URI 取代 URI。

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. 按一下 [檔案]。

    8. 在 [**設定模組**] 頁面上，按 *[下一步]* 。

    9. 在 [**設定模組**] 頁面上按 *[下一步]* ，然後按一下 [**提交**]。

5. 在模組更新之後，會下載、解壓縮 dacpac 檔案，並針對 SQL Database Edge 實例進行部署。

## <a name="using-streaming-jobs-with-sql-database-edge"></a>使用具有 SQL Database 邊緣的串流作業

Azure SQL Database Edge 有串流分析執行時間的原生執行。 這可讓使用者建立 Azure 串流分析 Edge 作業，並將該作業部署為 SQL Database Edge 串流作業。 若要建立串流分析 Edge 作業，請遵循下列步驟。

1. 使用預覽[URL](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true)流覽至 Azure 入口網站。 此預覽 URL 可讓使用者設定串流分析 edge 作業的 SQL Database 輸出。

2. 建立新的**Azure IoT Edge 串流分析**作業，然後選擇以**Edge**為目標的主控環境。

3. 定義 Azure 串流分析作業的*輸入*和*輸出*。 每個 SQL 輸出（如下所設定）都會系結至資料庫內的單一資料表。 如果需要將資料串流至多個資料表，您將需要建立多個 SQL Database 輸出。 SQL 輸出可以設定為指向不同的資料庫。

    *輸入-選擇 [EdgeHub] 作為 Edge 作業的輸入，並填入資源資訊。*

    *輸出-選取 [SQL Database 做為輸出]、[手動提供 SQL Database 設定]，並提供資料庫和資料表的設定詳細資料。*

    |欄位      | 描述 |
    |---------------|-------------|
    |輸出別名 | 輸出別名的名稱。|
    |資料庫 | SQL Database 的名稱。 這必須是有效的資料庫名稱，存在於 SQL Database Edge 實例上。|
    |伺服器名稱 | SQL 實例的名稱（或 IP 位址）和埠號碼詳細資料。 針對 SQL Database Edge 部署，您可以使用**tcp：.，1433**做為伺服器名稱。|
    |使用者名稱 | SQL 登入帳戶，其具有上述資料庫的資料讀取器和資料寫入器存取權。|
    |密碼 | 前述 SQL 登入帳戶的密碼。|
    |資料表 | 將為串流作業輸出之資料表的名稱。|
    |繼承資料分割| 此 SQL 輸出設定選項可讓您繼承先前查詢步驟或輸入的資料分割配置。 啟用此功能之後，寫入磁片資料表，並為您的作業擁有完全平行拓撲，預期會看到較佳的輸送量。|
    |批次大小| [批次大小] 是每個大量插入交易傳送的最大記錄數目。|

    範例輸入/輸出設定如下：

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output :
            Output alias: output
            Database:  MeasurementsDB
            Server name: tcp:.,1433
            Username: sa
            Password: <Password>
            Table: TemperatureMeasurements
            Inherit Partitioning: Merge all input partitions into a single writer
            Max batch count: 10000
    ```

    > [!NOTE]
    > 如需有關 Azure 串流分析 SQL 輸出介面卡的詳細資訊，請參閱[azure 串流分析輸出至 Azure SQL Database](../stream-analytics/stream-analytics-sql-output-perf.md)。

4. 定義 edge 作業的 ASA 工作查詢。 此查詢應該使用定義的輸入/輸出別名做為查詢中的輸入和輸出名稱。 如需詳細資訊，請參閱[串流分析查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)。

5. 設定 Edge 作業的儲存體帳戶設定。 儲存體帳戶會用來做為 edge 作業的發佈目標。

6. 在 [設定] 底下，選取 [發佈]，然後按一下 [發佈] 按鈕。 儲存用於 SQL Database Edge 模組的 SAS URL。

### <a name="deploy-the-stream-analytics-edge-job-to-the-sql-database-edge"></a>將串流分析 edge 作業部署到 SQL Database 邊緣

若要將串流作業部署到 SQL Database Edge 模組，請更新 SQL Database Edge 模組設定，以包含上述步驟中串流作業的 SAS URI。 更新 SQL Database Edge 模組

1. 在 Azure 入口網站上，流覽至您的 IoT 中樞部署。

2. 在左側窗格中，按一下 [ **IoT Edge**]。

3. 在 [ **IoT Edge** ] 頁面上，尋找並按一下 SQL Database Edge 模組部署所在的 IoT Edge。

4. 在 [ *IoT Edge 裝置*裝置] 頁面上，按一下 [**設定模組**]。 

5. 在 [**設定模組**]*頁面上，按一下 [針對 SQL Database* Edge 模組進行設定]。 

6. 在 [ **IoT Edge 自訂模組**] 窗格中，選取 [*設定模組對應項所需的屬性*]，然後更新所需的屬性，以包含 ASAJobInfo 選項的 URI，如下列範例所示。 

    > [!NOTE]
    > 以下的 SAS URI 僅供說明之用。 請以您部署中的實際 URI 取代 URI。

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. 按一下 [檔案]。

8. 在 [**設定模組**] 頁面上，按 *[下一步]* 。

9. 在 [**設定模組**] 頁面上按 *[下一步]* ，然後按一下 [**提交**]。

10. 發佈模組更新之後，串流分析作業檔案會下載、解壓縮，並針對 SQL Database Edge 實例進行部署。

## <a name="next-steps"></a>後續步驟

- 如需價格與可用性的相關詳細資料，請參閱[Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/)。
- 要求為您的訂用帳戶啟用 Azure SQL Database Edge。
- 若要開始使用，請參閱下列各項：
  - [透過 Azure 入口網站部署 SQL Database Edge](deploy-portal.md)
