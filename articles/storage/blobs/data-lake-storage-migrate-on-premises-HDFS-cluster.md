---
title: 使用 Azure 資料箱，將資料從內部部署 HDFS 儲存至 Azure 儲存體
description: 將資料從內部部署 HDFS 存放區移轉到 Azure 儲存體
services: storage
author: normesta
ms.service: storage
ms.date: 03/01/2019
ms.author: normesta
ms.topic: article
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 841193305556e3bc9e543abc47974c7b91b0cea1
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518027"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>使用 Azure 資料箱來將資料從內部部署 HDFS 存放區移轉到 Azure 儲存體

您可以使用資料箱裝置，從內部部署 HDFS 存放區到 Azure 儲存體 （blob 儲存體或 Data Lake 儲存體 Gen2） Hadoop 叢集的移轉資料。

這篇文章可協助您完成這些工作：

:heavy_check_mark:將資料複製到資料箱裝置。

:heavy_check_mark:資料箱裝置寄給 Microsoft。

:heavy_check_mark:移動資料至 Data Lake 儲存體 Gen2 儲存體帳戶。

## <a name="prerequisites"></a>必要條件

您需要下列步驟來完成移轉。

* Azure 儲存體帳戶**不**有在其上啟用的階層式命名空間。

* 如果您想要使用 Azure Data Lake 儲存體 Gen2 帳戶 (儲存體帳戶**沒有**有在其上啟用的階層式命名空間)，則您可能想要在此時建立它。

* 在內部部署 Hadoop 叢集，其中包含您的來源資料。

* [Azure 資料箱裝置](https://azure.microsoft.com/services/storage/databox/)。 

    - [訂購資料箱](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered)。 時排序您的 Box，請務必選擇 儲存體帳戶**不**有在其上啟用的階層式命名空間。 這是因為資料箱尚不支援直接擷取到 Azure Data Lake 儲存體 Gen2。 您必須複製到儲存體帳戶，然後執行 ADLS Gen2 考量的第二個複本。 下列步驟提供相關指示。
    - [纜線，並連接您的資料箱](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up)到內部部署網路。

如果您準備好，讓我們開始。

## <a name="copy-your-data-to-a-data-box-device"></a>將資料複製到資料箱裝置

若要您的內部部署 HDFS 存放區的資料複製到資料箱裝置，您會設定幾件事，，然後使用[DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)工具。

如果您要複製的資料量超過一個單一的 [資料] 方塊的容量，您必須中斷您的資料集到符合您的資料方塊的大小。

請遵循下列步驟來透過 REST Api 的 Blob/物件儲存體的資料複製到資料箱。 REST API 介面將會顯示為您叢集的 HDFS 存放區的 [資料] 方塊。 


1. 您將透過 REST 資料複製之前，找出的安全性和連線的基本連接到資料箱上的 REST 介面。 登入本機 web UI 的資料 方塊中，並移至**連線 和 複製**頁面。 對 Azure 儲存體帳戶資料方塊中，如底下**存取設定**，找出並選取**REST(Preview)**。

    ![「 連線並複製 」 的頁面](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. 在存取儲存體帳戶和上傳 [資料] 對話方塊中，複製**Blob 服務端點**並**儲存體帳戶金鑰**。 從 blob 服務端點中，省略`https://`和結尾的斜線。

    在此案例中，端點是： `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`。 您將使用的 URI 主機部分是： `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`。 如需範例，請參閱如何[連接到 REST over http](/azure/databox/data-box-deploy-copy-data-via-rest)。 

     ![「 存取儲存體帳戶和將資料上傳 」 對話方塊](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. 將端點和資料方塊的 IP 位址，以新增`/etc/hosts`每個節點上。

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```
    如果您使用 dns 的另一種機制，您應該確保能解析端點的 [資料] 方塊。
    
3. 設定的殼層變數`azjars`指向`hadoop-azure`而`microsoft-windowsazure-storage-sdk`jar 檔案。 這些檔案是 Hadoop 安裝目錄下 (您可以檢查這些檔案是否存在使用此命令`ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`其中`<hadoop_install_dir>`是 Hadoop 的安裝所在的目錄) 使用完整路徑。 
    
    ```
    # azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar
    # azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar
    ```

4. 將資料從 Hadoop HDFS 複製到資料中的 Blob 儲存體。

    ```
    # hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.[blob_service_endpoint]=[account_key] \
    -strategy dynamic -m 4 -update \
    [source_directory] \
           wasb://[container_name]@[blob_service_endpoint]/[destination_folder]       
    ```
   `-libjars`選項用來進行`hadoop-azure*.jar`及相關`azure-storage*.jar`可檔案`distcp`。 這可能已經發生某些叢集。
   
   下列範例顯示如何`distcp`命令用來將資料複製。
   
   ```
   # hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -strategy dynamic -m 4 -update \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/testfiles
   ```
  
若要改善複製速度：
- 請嘗試變更的對應程式數目。 (上述範例使用`m`= 4 的對應程式。)
- 嘗試執行多個`distcp`以平行方式。
- 請記住大型檔案的執行較小的檔案。       
    
## <a name="ship-the-data-box-to-microsoft"></a>出貨給 Microsoft 的 [資料] 方塊

請遵循下列步驟來準備並寄送至 Microsoft 資料箱裝置。

1. 將資料複製完成之後，執行[準備寄送](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest)資料方塊上。 裝置準備完成之後，下載 BOM 的檔案。 您將會使用這些 BOM 或資訊清單檔案，稍後若要確認資料上傳至 Azure。 關閉裝置，並移除纜線。 
2.  排定取貨與 UPS[寄送回給 Azure 您資料箱](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)。 
3.  當 Microsoft 收到您的裝置連線到網路的資料中心的資料上傳至您指定 （使用停用的階層式命名空間） 的儲存體帳戶後，您所訂購資料箱。 驗證您的資料上傳至 Azure 的 BOM 的檔案。 您現在可以繼續這項資料的 Data Lake 儲存體 Gen2 儲存體帳戶。

## <a name="move-the-data-onto-your-data-lake-storage-gen2-storage-account"></a>移動資料至 Data Lake 儲存體 Gen2 儲存體帳戶

如果您使用 Azure Data Lake 儲存體 Gen2 作為您的資料存放區，則需要這個步驟。 如果您使用只是 blob 儲存體帳戶而不需要階層式命名空間做為您的資料存放區，您不需要執行此步驟。

您可以在 2 種方式可以執行這項操作。 

- 使用[Azure Data Factory 將資料移至 ADLS Gen2](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2)。 您必須指定**Azure Blob 儲存體**做為來源。

- 使用 Azure 為基礎的 Hadoop 叢集。 您可以執行此 DistCp 命令：

    ```bash
    hadoop distcp -Dfs.azure.account.key.{source_account}.dfs.windows.net={source_account_key} abfs://{source_container} @{source_account}.dfs.windows.net/[path] abfs://{dest_container}@{dest_account}.dfs.windows.net/[path]
    ```

此命令將資料和中繼資料到您的 Data Lake 儲存體 Gen2 儲存體帳戶複製從儲存體帳戶。

## <a name="next-steps"></a>後續步驟

了解 Data Lake 儲存體 Gen2 與 HDInsight 叢集的運作方式。 請參閱[搭配 Azure HDInsight 叢集使用 Azure Data Lake Storage Gen2](https://docs.microsoft.com/Azure/storage/blobs/data-lake-storage-use-hdi-cluster?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
