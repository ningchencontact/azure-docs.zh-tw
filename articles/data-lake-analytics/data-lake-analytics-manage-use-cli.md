---
title: 使用 Azure 命令列介面管理 Azure Data Lake Analytics | Microsoft Docs
description: 了解如何使用 Azure CLI 管理 Data Lake Analytics 帳戶、資料來源、工作和使用者
services: data-lake-analytics
documentationcenter: ''
author: SnehaGunda
manager: Kfile
ms.assetid: 4e5a3a0a-6d7f-43ed-aeb5-c3b3979a1e0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/29/2018
ms.author: sngun
ms.openlocfilehash: a78a7ea619be28f01372a7b80d3cb4a5d35bd50e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
---
# <a name="manage-azure-data-lake-analytics-using-azure-command-line-interface-cli"></a>使用 Azure 命令列介面 (CLI) 管理 Azure Data Lake Analytics

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

了解如何使用 Azure CLI 管理 Azure Data Lake Analytics 帳戶、資料來源、使用者和工作。 若要使用其他工具查看管理主題，請按一下上方的索引標籤選取器。


**先決條件**

開始進行本教學課程之前，您必須具備下列資源：

* Azure 訂用帳戶。 請參閱[取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

* Azure CLI。 請參閱 [安裝和設定 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

   * 下載並安裝 **Azure CLI 工具** [發行前版本](https://github.com/MicrosoftBigData/AzureDataLake/releases) ，才能完成這個示範。

* 使用 `az login` 命令進行驗證，並且選取您想要使用的訂用帳戶。 如需使用公司或學校帳戶驗證的詳細資訊，請參閱 [從 Azure CLI 連線至 Azure 訂用帳戶](/cli/azure/authenticate-azure-cli)。

   ```azurecli
   az login
   az account set --subscription <subscription id>
   ```

   您現在可以存取 Data Lake Analytics 和 Data Lake Store 命令。 執行下列命令以列出 Data Lake Store 和 Data Lake Analytics 命令：

   ```azurecli
   az dls -h
   az dla -h
   ```

## <a name="manage-accounts"></a>管理帳戶

您必須擁有 Data Lake Analytics 帳戶，才能執行任何 Data Lake Analytics 工作。 與 Azure HDInsight 不同的是，分析帳戶未執行工作時，您無需支付該帳戶的費用。 您只需支付執行工作時的費用。  如需詳細資訊，請參閱 [Azure Data Lake Analytics 概觀](data-lake-analytics-overview.md)。  

### <a name="create-accounts"></a>建立帳戶

執行下列命令來建立 Data Lake 帳戶。 

   ```azurecli
   az dla account create --account "<Data Lake Analytics account name>" --location "<Location Name>" --resource-group "<Resource Group Name>" --default-data-lake-store "<Data Lake Store account name>"
   ```

### <a name="update-accounts"></a>更新帳戶

下列命令會更新現有 Data Lake Analytics 帳戶的屬性

   ```azurecli
   az dla account update --account "<Data Lake Analytics Account Name>" --firewall-state "Enabled" --query-store-retention 7
   ```

### <a name="list-accounts"></a>列出帳戶

列出特定資源群組內的 Data Lake Analytics 帳戶

   ```azurecli
   az dla account list "<Resource group name>"
   ```

## <a name="get-details-of-an-account"></a>取得帳戶的詳細資料

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

### <a name="delete-an-account"></a>刪除帳戶

   ```azurecli
   az dla account delete --account "<Data Lake Analytics account name>" --resource-group "<Resource group name>"
   ```

## <a name="manage-data-sources"></a>管理資料來源

Data Lake Analytics 目前支援以下兩個資料來源：

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure 儲存體](../storage/common/storage-introduction.md)

當您建立分析帳戶時，必須指定 Azure Data Lake 儲存體帳戶作為預設的儲存體帳戶。 預設的 Data Lake 儲存體帳戶是用來儲存作業中繼資料與作業稽核記錄。 建立分析帳戶後，就可以新增其他 Azure Data Lake 儲存體帳戶和/或 Azure 儲存體帳戶。 

### <a name="find-the-default-data-lake-store-account"></a>尋找預設的 Data Lake Store 帳戶

您可以執行 `az dla account show` 命令來檢視所使用的預設 Data Lake Store 帳戶。 預設帳戶名稱列在 defaultDataLakeStoreAccount 屬性底下。

   ```azurecli
   az dla account show --account "<Data Lake Analytics account name>"
   ```

### <a name="add-additional-blob-storage-accounts"></a>新增其他 Blob 儲存體帳戶

   ```azurecli
   az dla account blob-storage add --access-key "<Azure Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Storage account name>"
   ```

> [!NOTE]
> 僅支援 Blob 儲存體簡短名稱。 請勿使用 FQDN，例如 "myblob.blob.core.windows.net"。
> 

### <a name="add-additional-data-lake-store-accounts"></a>新增其他的 Data Lake Store 帳戶

下列命令會使用其他 Data Lake Store 帳戶更新指定的 Data Lake Analytics 帳戶：

   ```azurecli
   az dla account data-lake-store add --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Data Lake Store account name>"
   ```

### <a name="update-existing-data-source"></a>更新現有的資料來源

若要更新現有的 Blob 儲存體帳戶金鑰：

   ```azurecli
   az dla account blob-storage update --access-key "<New Blob Storage Account Key>" --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

### <a name="list-data-sources"></a>列出資料來源：

若要列出 Data Lake Store 帳戶：

   ```azurecli
   az dla account data-lake-store list --account "<Data Lake Analytics account name>"
   ```

若要列出 Blob 儲存體帳戶：

   ```azurecli
   az dla account blob-storage list --account "<Data Lake Analytics account name>"
   ```

![Data Lake Analytics 會列出資料來源](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>刪除資料來源：
刪除 Data Lake Store 帳戶：

   ```azurecli
   az dla account data-lake-store delete --account "<Data Lake Analytics account name>" --data-lake-store-account-name "<Azure Data Lake Store account name>"
   ```

刪除 Blob 儲存體帳戶：

   ```azurecli
   az dla account blob-storage delete --account "<Data Lake Analytics account name>" --storage-account-name "<Data Lake Store account name>"
   ```

## <a name="manage-jobs"></a>管理工作
您必須擁有 Data Lake Analytics 帳戶，才能建立工作。  如需詳細資訊，請參閱 [管理 Data Lake Analytics 帳戶](#manage-accounts)。

### <a name="list-jobs"></a>列出工作

   ```azurecli
   az dla job list --account "<Data Lake Analytics account name>"
   ```

   ![Data Lake Analytics 會列出資料來源](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>取得工作詳細資料

   ```azurecli
   az dla job show --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

### <a name="submit-jobs"></a>提交工作

> [!NOTE]
> 工作的預設優先順序為 1000，工作的平行處理原則預設程度是 1。
> 
   ```azurecli
   az dla job submit --account "<Data Lake Analytics account name>" --job-name "<Name of your job>" --script "<Script to submit>"
   ```

### <a name="cancel-jobs"></a>取消工作
使用 list 命令來尋找工作識別碼，然後使用 cancel 來取消工作。

   ```azurecli
   az dla job cancel --account "<Data Lake Analytics account name>" --job-identity "<Job Id>"
   ```

## <a name="use-azure-resource-manager-groups"></a>使用 Azure 資源管理員群組
應用程式通常由許多元件組成，例如 Web 應用程式、資料庫、資料庫伺服器、儲存體及協力廠商服務。 Azure Resource Manager 可讓您將應用程式中的資源做為群組使用，稱為 Azure 資源群組。 您可以透過單一、協調的作業來部署、更新、監視或刪除應用程式的所有資源。 您會使用部署的範本，且該範本可以用於不同的環境，例如測試、預備和生產環境。 您可以檢視整個群組的彙總成本，為您的組織釐清計費。 如需詳細資訊，請參閱 [Azure 資源管理員概觀](../azure-resource-manager/resource-group-overview.md)。 

Data Lake Analytics 服務可包含下列元件：

* Azure Data Lake Analytics 帳戶
* 必要的預設 Azure Data Lake 儲存體帳戶
* 其他 Azure Data Lake 儲存體帳戶
* 其他 Azure 儲存體帳戶

您可以在某個資源管理員群組下建立上述所有元件，這樣更容易管理。

![Azure Data Lake Analytics 帳戶與儲存體](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Data Lake Analytics 帳戶和相依的儲存體帳戶必須位於相同的 Azure 資料中心。
但資源管理員群組可位在不同的資料中心內。  

## <a name="see-also"></a>另請參閱
* [Microsoft Azure Data Lake Analytics 概觀](data-lake-analytics-overview.md)
* [透過 Azure 入口網站開始使用 Data Lake Analytics](data-lake-analytics-get-started-portal.md)
* [使用 Azure 入口網站管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)
* [使用 Azure 入口網站監視和疑難排解 Azure Data Lake Analytics 作業](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

