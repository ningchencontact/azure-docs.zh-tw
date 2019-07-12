---
title: 使用 Azure 資料箱，將資料從內部部署 HDFS 儲存至 Azure 儲存體
description: 將資料從內部部署 HDFS 存放區移轉到 Azure 儲存體
services: storage
author: normesta
ms.service: storage
ms.date: 06/11/2019
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 4445a8566c04d30cfb8743cbd33623f2e23f0dde
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595404"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>使用 Azure 資料箱來將資料從內部部署 HDFS 存放區移轉到 Azure 儲存體

您可以使用資料箱裝置，從內部部署 HDFS 存放區到 Azure 儲存體 （blob 儲存體或 Data Lake 儲存體 Gen2） Hadoop 叢集的移轉資料。 您可以選擇從 80 TB 的資料 方塊中選取或 770 TB 資料方塊沈重。

這篇文章可協助您完成這些工作：

> [!div class="checklist"]
> * 準備移轉您的資料。
> * 將資料複製到資料箱 」 或 「 大量資料方塊中的裝置。
> * 將裝置寄回給 Microsoft。
> * 移動資料至 Data Lake 儲存體 Gen2。

## <a name="prerequisites"></a>先決條件

您需要下列步驟來完成移轉。

* 兩個儲存體帳戶;一個具有已啟用，階層式命名空間，另一個不。

* 在內部部署 Hadoop 叢集，其中包含您的來源資料。

* [Azure 資料箱裝置](https://azure.microsoft.com/services/storage/databox/)。

  * [訂購資料箱](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered)或是[資料箱繁重](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered)。 在排序您的裝置，請務必選擇 儲存體帳戶**不**有在其上啟用的階層式命名空間。 這是因為資料箱裝置尚不支援直接擷取到 Azure Data Lake 儲存體 Gen2。 您必須複製到儲存體帳戶，然後執行 ADLS Gen2 考量的第二個複本。 下列步驟提供相關指示。

  * 纜線，並連接您[Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up)或[大量資料方塊](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up)到內部部署網路。

如果您準備好，讓我們開始。

## <a name="copy-your-data-to-a-data-box-device"></a>將資料複製到資料箱裝置

如果您的資料融入單一的資料箱裝置，您會將資料複製到資料箱裝置。 

如果您的資料大小超過資料箱裝置的容量，然後使用[選用的程序來將資料分割到多個資料箱裝置](#appendix-split-data-across-multiple-data-box-devices)，然後執行此步驟。 

若要您的內部部署 HDFS 存放區的資料複製到資料箱裝置，您會設定幾件事，，然後使用[DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)工具。

請遵循下列步驟來透過 REST Api 的 Blob/物件儲存體的資料複製到您的資料箱裝置。 REST API 介面將會顯示為您叢集的 HDFS 存放裝置。

1. 您將透過 REST 資料複製之前，找出的安全性和連線的基本連接到資料箱 」 或 「 大量資料方塊上的 REST 介面。 登入本機 web UI 的資料 方塊中，並移至**連線 和 複製**頁面。 對 Azure 儲存體帳戶為您的裝置，低於**存取設定**，找出並選取**REST**。

    ![「 連線並複製 」 的頁面](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. 在存取儲存體帳戶和上傳 [資料] 對話方塊中，複製**Blob 服務端點**並**儲存體帳戶金鑰**。 從 blob 服務端點中，省略`https://`和結尾的斜線。

    在此案例中，端點是： `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`。 您將使用的 URI 主機部分是： `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`。 如需範例，請參閱如何[連接到 REST over http](/azure/databox/data-box-deploy-copy-data-via-rest)。 

     ![「 存取儲存體帳戶和將資料上傳 」 對話方塊](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. 將端點和資料 方塊或資料方塊大量節點 IP 位址，以新增`/etc/hosts`每個節點上。

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    如果您使用 dns 的另一種機制，您應該確保能解析端點的 [資料] 方塊。

4. 設定殼層變數`azjars`的位置`hadoop-azure`和`azure-storage`jar 檔案。 您可以找到這些 Hadoop 安裝目錄下的檔案。

    若要判斷這些檔案是否存在，請使用下列命令： `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`。 取代`<hadoop_install_dir>`預留位置取代為您安裝 Hadoop 的目錄的路徑。 請務必使用完整的路徑。

    範例：

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. 建立您想要用於複製資料的儲存體容器。 您也應該指定目的地目錄，此命令的一部分。 這可能在此時是空的目的地目錄。

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * 取代`<blob_service_endpoint>`預留位置取代為您的 blob 服務端點的名稱。

    * 取代`<account_key>`預留位置取代為您的帳戶的存取金鑰。

    * 取代`<container-name>`預留位置取代為您的容器名稱。

    * 取代`<destination_directory>`預留位置取代為您想要複製您的資料目錄的名稱。

6. 執行清單命令，以確認您建立您的容器和目錄。

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * 取代`<blob_service_endpoint>`預留位置取代為您的 blob 服務端點的名稱。

   * 取代`<account_key>`預留位置取代為您的帳戶的存取金鑰。

   * 取代`<container-name>`預留位置取代為您的容器名稱。

7. 從 HDFS 複製資料的 Hadoop 資料方塊中的 Blob 儲存體、 加入至您稍早建立的容器。 如果找不到您要複製到目錄，此命令會自動建立它。

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * 取代`<blob_service_endpoint>`預留位置取代為您的 blob 服務端點的名稱。

    * 取代`<account_key>`預留位置取代為您的帳戶的存取金鑰。

    * 取代`<container-name>`預留位置取代為您的容器名稱。

    * 取代`<exlusion_filelist_file>`預留位置取代為包含您的檔案排除項目清單的檔案名稱。

    * 取代`<source_directory>`預留位置取代為包含您想要複製的資料目錄的名稱。

    * 取代`<destination_directory>`預留位置取代為您想要複製您的資料目錄的名稱。

    `-libjars`選項用來進行`hadoop-azure*.jar`及相關`azure-storage*.jar`可檔案`distcp`。 這可能已經發生某些叢集。

    下列範例顯示如何`distcp`命令用來將資料複製。

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    若要改善複製速度：

    * 請嘗試變更的對應程式數目。 (上述範例使用`m`= 4 的對應程式。)

    * 請嘗試執行多個`distcp`以平行方式。

    * 請記住大型檔案的執行較小的檔案。

## <a name="ship-the-data-box-to-microsoft"></a>出貨給 Microsoft 的 [資料] 方塊

請遵循下列步驟來準備並寄送至 Microsoft 資料箱裝置。

1. 首先，[準備寄送您的資料方塊 」 或 「 大量資料方塊上](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest)。

2. 裝置準備完成之後，下載 BOM 的檔案。 您將會使用這些 BOM 或資訊清單檔案，稍後若要確認資料上傳至 Azure。

3. 關閉裝置，並移除纜線。

4. 與 UPS 排定取貨時間。

    * 資料箱裝置，請參閱 <<c0> [ 寄送您的資料箱](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)。

    * 對於大量資料方塊中的裝置，請參閱[寄送您的資料方塊大量](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up)。

5. Microsoft 收到您的裝置連線到資料中心網路將資料上傳至您指定 （使用停用的階層式命名空間） 的儲存體帳戶後，當您放置裝置順序。 驗證您的資料上傳至 Azure 的 BOM 的檔案。 您現在可以繼續這項資料的 Data Lake 儲存體 Gen2 儲存體帳戶。

## <a name="move-the-data-into-azure-data-lake-storage-gen2"></a>將資料移至 Azure Data Lake 儲存體 Gen2

您已經有資料到您的 Azure 儲存體帳戶。 現在您將資料複製到您的 Azure Data Lake 儲存體帳戶，並套用檔案和目錄的存取權限。

> [!NOTE]
> 如果您使用 Azure Data Lake 儲存體 Gen2 作為您的資料存放區，則需要這個步驟。 如果您使用只是 blob 儲存體帳戶而不需要階層式命名空間做為您的資料存放區，您可以略過本節。

### <a name="copy-data-to-the-azure-data-lake-storage-gen-2-account"></a>將資料複製到 Azure Data Lake 儲存體 Gen 2 帳戶

使用 Azure Data Factory，或使用您的 Azure 為基礎的 Hadoop 叢集，您可以複製資料。

* 若要使用 Azure Data Factory，請參閱[Azure Data Factory 將資料移至 ADLS Gen2](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2)。 請務必指定**Azure Blob 儲存體**做為來源。

* 若要使用您的 Azure 為基礎的 Hadoop 叢集，執行此 DistCp 命令：

    ```bash
    hadoop distcp -Dfs.azure.account.key.<source_account>.dfs.windows.net=<source_account_key> abfs://<source_container> @<source_account>.dfs.windows.net/<source_path> abfs://<dest_container>@<dest_account>.dfs.windows.net/<dest_path>
    ```

    * 取代`<source_account>`和`<dest_account>`預留位置取代為來源和目的地儲存體帳戶的名稱。

    * 取代`<source_container>`和`<dest_container>`預留位置取代為來源和目的地容器的名稱。

    * 取代`<source_path>`和`<dest_path>`預留位置取代為來源和目的地目錄路徑。

    * 取代`<source_account_key>`預留位置取代為包含資料的儲存體帳戶的存取金鑰。

    此命令將資料和中繼資料到您的 Data Lake 儲存體 Gen2 儲存體帳戶複製從儲存體帳戶。

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>建立您的 Azure Data Lake 儲存體 Gen2 帳戶的服務主體

若要建立服務主體，請參閱[How to:使用入口網站來建立可存取資源的 Azure AD 應用程式和服務主體](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。

* 在執行該文章的[將應用程式指派給角色](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role)一節中的步驟時，請確實將 [儲存體 Blob 資料參與者]  角色指派給服務主體。

* 當執行中的步驟[取得值，以進行登入](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)文件、 應用程式識別碼和文字檔案，用戶端祕密值 區段。 您很快就會用到這些資料。

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>產生複製的檔案及其權限的清單

從內部部署 Hadoop 叢集，執行下列命令：

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

此命令會產生一份已複製的檔案及其權限。

> [!NOTE]
> 根據在 HDFS 的檔案數目，此命令可能需要很長的時間來執行。

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>產生的身分識別清單，並將它們對應到 Azure Active Directory （新增） 身分識別

1. 下載`copy-acls.py`指令碼。 請參閱[下載協助程式指令碼，並設定您的邊緣節點執行這些](#download-helper-scripts)一節。

2. 執行此命令來產生唯一的身分識別的清單。

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   此指令碼會產生名為`id_map.json`，其中包含您要新增為基礎的身分識別對應的身分識別。

3. 在文字編輯器中開啟 `id_map.json` 檔案。

4. 每個 JSON 物件出現在檔案中，更新`target`由 AAD 使用者主體名稱 (UPN) 或 ObjectId (OID)，以適當的屬性對應身分識別。 完成後，儲存檔案。 您將需要此檔案的下一個步驟。

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>將權限套用至複製的檔案，並套用身分識別對應

執行此套用至您複製到 Data Lake 儲存體 Gen2 帳戶資料的權限的命令：

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* 使用您的儲存體帳戶名稱取代 `<storage-account-name>` 預留位置。

* 取代`<container-name>`預留位置取代為您的容器名稱。

* 取代`<application-id>`和`<client-secret>`預留位置取代為您建立服務主體時，您會收集應用程式識別碼和用戶端祕密。

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>附錄：將資料分割到多個資料箱裝置

移動資料到資料箱裝置之前，您必須下載一些協助程式指令碼，請確定您的資料組織支應資料箱裝置，並排除任何不必要的檔案。

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>下載協助程式指令碼，並設定您的邊緣節點，並執行

1. 從您的邊緣或在內部部署 Hadoop 叢集的前端節點，執行下列命令：

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   此命令會複製 GitHub 存放庫，其中包含協助程式指令碼。

2. 請確定具有[jq](https://stedolan.github.io/jq/)您本機電腦上安裝的封裝。

   ```bash
   
   sudo apt-get install jq
   ```

3. 安裝[要求](http://docs.python-requests.org/en/master/)python 套件。

   ```bash
   
   pip install requests
   ```

4. 設定執行必要的指令碼的權限。

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>請確定您的資料會組織成到資料箱裝置

如果您的資料大小超過單一資料箱裝置的大小，您可以分割檔案分組，您可以儲存至多個資料箱裝置。

如果您的資料未超過大小上限為 100tb 資料箱裝置，您可以繼續下一節。

1. 以提高的權限，執行`generate-file-list`您依照上一節中的指導方針所下載的指令碼。

   以下是命令參數的描述：

   ```
   sudo -u hdfs ./generate-file-list.py [-h] [-s DATABOX_SIZE] [-b FILELIST_BASENAME]
                    [-f LOG_CONFIG] [-l LOG_FILE]
                    [-v {DEBUG,INFO,WARNING,ERROR}]
                    path

   where:
   positional arguments:
   path                  The base HDFS path to process.

   optional arguments:
   -h, --help            show this help message and exit
   -s DATABOX_SIZE, --databox-size DATABOX_SIZE
                        The size of each Data Box in bytes.
   -b FILELIST_BASENAME, --filelist-basename FILELIST_BASENAME
                        The base name for the output filelists. Lists will be
                        named basename1, basename2, ... .
   -f LOG_CONFIG, --log-config LOG_CONFIG
                        The name of a configuration file for logging.
   -l LOG_FILE, --log-file LOG_FILE
                        Name of file to have log output written to (default is
                        stdout/stderr)
   -v {DEBUG,INFO,WARNING,ERROR}, --log-level {DEBUG,INFO,WARNING,ERROR}
                        Level of log information to output. Default is 'INFO'.
   ```

2. 複製所產生的檔案，使其可存取 HDFS 清單[DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)作業。

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>排除不必要的檔案

您必須從 DisCp 作業中排除某些目錄。 比方說，排除包含狀態資訊可讓叢集中執行的目錄。

在您計劃用來起始 DistCp 作業在內部部署 Hadoop 叢集上，建立檔案，指定您想要排除的目錄清單。

以下為範例：

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>後續步驟

了解 Data Lake 儲存體 Gen2 與 HDInsight 叢集的運作方式。 請參閱[搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)。
