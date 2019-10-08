---
title: 教學課程：建立端對端擷取、轉換、載入 (ETL) 管線來衍生 Sales Insights
description: 了解如何搭配 Azure HDInsight 使用建立 ETL 管線，以使用 Spark 隨選叢集和 Power BI 從銷售資料衍生見解。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 09/30/2019
ms.author: hrasheed
ms.openlocfilehash: 52509f886ac9882c372de401ca163ccef418837f
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695703"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights"></a>教學課程：建立端對端資料管線以衍生 Sales Insights

在本教學課程中，您將建置端對端資料管線，以執行擷取、轉換和載入作業。 此管線會使用在 Azure HDInsight 上執行的 Apache Spark 和 Apache Hive 叢集來查詢和操作資料。 其他使用的技術包括用於資料儲存的 Data Lake Storage Gen2，以及用於視覺效果的 Power BI。

此資料管線會結合各種不同存放區中的資料、移除任何不必要的資料、附加新的資料，並將其重新載入至您的儲存體，以視覺化商業深入解析。 深入了解 ETL 管線的[大規模擷取、轉換和載入 (ETL)](./hadoop/apache-hadoop-etl-at-scale.md)。

![ETL 架構](./media/hdinsight-sales-insights-etl/architecture.png)

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

下載 [Power BI Desktop](https://www.microsoft.com/download/details.aspx?id=45331)，以在本教學課程結束時將商業深入解析視覺化。

## <a name="create-resources"></a>建立資源

### <a name="clone-the-repository-with-scripts-and-data"></a>複製含有指令碼和資料的存放庫

1. 登入 [Azure 入口網站](https://portal.azure.com)
1. 從頂端功能表列開啟 Cloud Shell。 如果 Azure Cloud Shell 發出提示，請選取您用來建立檔案共用的訂用帳戶。

    ![開啟 Azure Cloud Shell](./media/hdinsight-sales-insights-etl/hdinsight-sales-insights-etl-click-cloud-shell.png)
1. 從 [選取環境] 下拉式功能表中選取 [Bash]。
1. 登入您的 Azure 帳戶並設定訂用帳戶。 
1. 設定專案的資源群組。
    1. 挑選唯一的資源群組名稱。
    1. 在 Azure Cloud Shell 中執行下列程式碼片段，以設定將在後續步驟中使用的變數

        ```azurecli-interactive 
        resourceGroup="<RESOURCE GROUP NAME>"
        subscriptionID="<SUBSCRIPTION ID>"
        
        az account set --subscription $subscriptionID
        az group create --name $resourceGroup --location westus
        ```

1. 請在 Cloud Shell 上輸入下列命令，以從 [HDInsight Sales Insights ETL 存放庫](https://github.com/Azure-Samples/hdinsight-sales-insights-etl)下載本教學課程的資料和指令碼：

        ```azurecli-interactive 
        git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
        cd hdinsight-sales-insights-etl
        ```

1. 在殼層提示字元中輸入 `ls`，以確認下列檔案和目錄已建立：

    ```output
    /salesdata/
    /scripts/
    /templates/
    ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>部署管線所需的 Azure 資源 

1. 新增指令碼 `chmod +x scripts/*.sh` 的執行權限
1. 使用命令 `./scripts/resources.sh <RESOURCE_GROUP_NAME> <LOCATION>` 執行指令碼，以在 Azure 中部署下列資源：

    1. Azure Blob 儲存體帳戶 - 此帳戶會保存公司銷售資料
    2. Azure Data Lake Storage Gen2 帳戶 - 此帳戶將作為這兩個 HDInsight 叢集的儲存體帳戶。 在 [Azure HDInsight 與 Data Lake Storage Gen2 的整合](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)中深入了解 HDInsight 和 Data Lake Storage Gen2。
    3. 使用者指派的受控識別 - 此帳戶會為 HDInsight 叢集提供 Data Lake Storage Gen2 帳戶的存取權。
    4. Apache Spark 叢集 - 此叢集將用來清理和轉換原始資料
    5. Apache Hive 互動式查詢叢集 - 此叢集將允許查詢銷售資料並透過 Power BI 將其視覺化
    6. 網路安全性群組 (NSG) 規則所支援的 Azure 虛擬網路 - 此虛擬網路可讓叢集進行通訊，也可保護其通訊。 

建立叢集可能需要約 20 分鐘的時間。

`resources.sh` 指令碼包含下列命令，會使用 Resource Manager 範本 (`resourcestemplate.json`) 以所需的組態建立指定的資源。

```azurecli-interactive 
az group deployment create --name ResourcesDeployment \
    --resource-group $resourceGroup \
    --template-file resourcestemplate.json \
    --parameters "@resourceparameters.json"
```

`resources.sh` 指令碼也會使用下列命令，將銷售資料 csv 檔案上傳到新建立的 Blob 儲存體帳戶中：

```
az storage blob upload-batch -d rawdata \
    --account-name <BLOB STORAGE NAME> -s ./ --pattern *.csv
```

叢集的 ssh 存取所使用的預設密碼為 `Thisisapassword1`。 如果您想要變更密碼，請瀏覽至 `resourcesparameters.json` 檔案，然後變更 `sparksshPassword`、`sparkClusterLoginPassword`、`llapClusterLoginPassword` 和 `llapsshPassword` 參數的密碼。

### <a name="verify-deployment-and-collect-resource-information"></a>驗證部署並收集資源資訊

1. 如果您想要檢查部署的狀態，請瀏覽至 Azure 入口網站上的資源群組。 按一下 [設定]  底下的 [部署]  。 按一下部署 `ResourcesDeployment` 的名稱。 在此，您可以看到已成功部署的資源，以及仍在部署中的資源。
1. 部署完成後，請移至 Azure 入口網站 > [資源群組]  > <RESOURCE_GROUP_NAME>
1. 找出建立後用來儲存銷售檔案的新 Azure 儲存體帳戶。 儲存體帳戶名稱的開頭為 `blob`，且其後會包含一個隨機字串。 
    1. 請記下儲存體帳戶名稱以供後續使用。
    1. 按一下 Blob 儲存體帳戶的名稱。
    1. 在入口網站的左側的 [設定]  底下，按一下 [存取金鑰]  。
    1. 複製 **Key1** 方塊中的字串並加以儲存，以供後續使用。
1. 找出建立作為 HDInsight 叢集儲存體的 Data Lake Storage Gen2 帳戶。 此帳戶位於與 Blob 儲存體帳戶相同的資源群組中，但開頭為 `adlsgen2`。
    1. 請記下 Data Lake Storage Gen2 帳戶的名稱。
    1. 按一下 Data Lake Storage Gen2 帳戶的名稱。
    1. 在入口網站的左側的 [設定]  底下，按一下 [存取金鑰] 
    1. 複製 **Key1** 方塊中的字串並加以儲存，以供後續使用。

> [!Note]
> 在得知儲存體帳戶的名稱之後，您也可以在 Azure Cloud Shell 提示字元中使用下列命令取得帳戶金鑰：
> ```azurecli-interactive
> az storage account keys list \
>    --account-name <STORAGE NAME> \
>    --resource-group $rg \
>    --output table
> ```

### <a name="create-an-azure-data-factory"></a>建立 Azure Data Factory

Azure Data Factory 是有助於自動執行 Azure Pipelines 的工具。 它並非完成這些工作的唯一方法，但它是將這些程序自動化的絕佳途徑。 如需 Azure Data Factory 的詳細資訊，請深入了解 [Azure Data Factory 文件](https://azure.microsoft.com/services/data-factory/)。 

此 Azure Data Factory 會有一個具有兩項活動的管線： 

1. 第一個活動會將資料從 Azure Blob 儲存體複製到 Data Lake Storage Gen 2 儲存體帳戶，以模擬資料內嵌。
2. 第二個活動會轉換 Spark 叢集中的資料。 指令碼會藉由移除不必要的資料行，以及附加新資料行來計算單一交易所產生的收益，進行資料轉換。

若要設定您的 Azure Data Factory 管線，請執行指令碼 `adf.sh`：

1. 使用 `chmod +x adf.sh` 在檔案上新增執行權限
1. 使用 `./adf.sh` 執行指令碼 

此指令碼會執行下列作業︰

1. 在 Data Lake Storage Gen2 儲存體帳戶上建立具有 `Storage Blob Data Contributor` 權限的服務主體。
1. 取得驗證權杖，以授與對 [Data Lake Storage Gen2 FileSystem REST API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create) 的 POST 要求權限。
1. 在 `sparktransform.py` 和 `query.hql` 檔案中填入 Data Lake Storage Gen2 儲存體帳戶的實際名稱。
1. 取得 Data Lake Storage Gen2 和 Blob 儲存體帳戶的儲存體金鑰。
1. 建立另一個資源部署，以建立 Azure Data Factory 管線及其相關聯的連結服務和活動。 它會將儲存體金鑰當作參數傳至範本檔案，讓連結的服務可正確存取儲存體帳戶。

ADF 管線會使用下列命令進行部署：

```azurecli-interactive
az group deployment create --name ADFDeployment \
    --resource-group $resourceGroup \
    --template-file adftemplate.json \
    --parameters "@adfparameters.json"
```

## <a name="run-the-data-pipeline"></a>執行資料管線

### <a name="trigger-the-adf-activities"></a>觸發 ADF 活動

您在 ADF 管線中建立的第一個活動會將資料從 Blob 儲存體移至 Data Lake Storage Gen2。 第二個活動會在資料上套用 Spark 轉換，並將轉換後的 csv 檔案儲存到新的位置。 整個管線可能需要幾分鐘的時間才能完成。

若要觸發管線，您可以執行下列其中一項：

1. 在 PowerShell 中執行下列命令，以觸發 ADF 管線。 

    ```powershell
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "CopyPipeline_k8z" 
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "sparkTransformPipeline"
    ```

1. 您也可以開啟 Data Factory，選取 [撰寫和監視]，然後觸發複製管線，再從入口網站中觸發 Spark 管線。 如需透過入口網站觸發管線的詳細資訊，請參閱[使用 Azure Data Factory 在 HDInsight 中建立隨選 Apache Hadoop 叢集](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-create-linux-clusters-adf#trigger-a-pipeline)。

若要確認管線是否已執行，您可以執行下列其中一個步驟：

1. 透過入口網站，在您的 Azure Data Factory 中瀏覽至 [監視]  區段。
2. 移至 Data Lake Storage Gen 2 儲存體帳戶的儲存體總管、移至 `files` FileSystem，然後瀏覽至 `transformed` 資料夾並查看其內容，以確認管線是否成功。

如需使用 HDInsight 轉換資料的其他方式，請參閱這篇有關於使用 [Jupyter Notebook](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-load-data-run-query) 的文章

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>在互動式查詢叢集上建立資料表，以在 Power BI 上檢視資料

1. 使用 SCP 將 query.hql 檔案複製到 LLAP 叢集：

    ```
    scp scripts/query.hql sshuser@<clustername>-ssh.azurehdinsight.net:/home/sshuser/
    ```

2. 使用下列命令透過 SSH 連線至 LLAP 叢集，然後輸入您的密碼。 如果您並未改變 `resourcesparameters.json` 檔案，則密碼為 `Thisisapassword1`。

    ```
    ssh sshuser@<clustername>-ssh.azurehdinsight.net
    ```

3. 執行下列命令以執行指令碼

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

此指令碼會在互動式查詢叢集上建立受控資料表，供您從 Power BI 存取。 

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>從銷售資料建立 Power BI 儀表板

1. 開啟 Power BI Desktop
1. 選取 [取得資料]  。
1. 搜尋 **HDInsight 互動式查詢叢集**。
1. 在該處貼上您叢集的 URI。 其格式應為 `https://<LLAP CLUSTER NAME>.azurehdinsight.net`。輸入資料庫的 `default`。
1. 輸入您用來存取叢集的使用者名稱和密碼。

資料載入後，您可以用您想要建立的儀表板進行試驗。 請參閱下列連結，以了解如何開始使用 Power BI 儀表板：

* [Power BI 設計工具的儀表板簡介](https://docs.microsoft.com/power-bi/service-dashboards)
* [教學課程：開始使用 Power BI 服務](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，請使用下列步驟刪除所有資源，以免產生相關費用。

```azurecli-interactive 
az group delete -n $resourceGroup
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [大規模擷取、轉換和載入 (ETL)](./hadoop/apache-hadoop-etl-at-scale.md)
