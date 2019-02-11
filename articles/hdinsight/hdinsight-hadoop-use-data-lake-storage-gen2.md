---
title: 搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2
description: 了解如何建立搭配 Azure HDInsight 叢集的 Data Lake Storage Gen2。
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: howto
ms.date: 01/10/2019
ms.author: hrasheed
ms.openlocfilehash: a44e53d7a32ab151fa951d1bc89b741390a70dfb
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2019
ms.locfileid: "55464784"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2

Azure Data Lake Storage (Data Lake Storage) Gen2 是一組巨量資料分析的專屬功能，內建於 Azure Blob 儲存體。 Data Lake Storage Gen2 是由 Azure Blob 儲存體和 Azure Data Lake Storage Gen1 兩者的功能結合而成。 結合而成的服務可提供 Azure Data Lake Storage Gen1 的功能 (例如檔案系統語意、目錄及檔案層級安全性和級別)，以及 Azure Blob 儲存體的低成本、分層式儲存體、高可用性/災害復原功能。

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 可用性

Azure Data Lake Storage Gen2 對於絕大多數的 Azure HDInsight 叢集類型而言都是可用的儲存體選項，可作為預設和其他儲存體帳戶。 不過，HBase 只能有一個 Data Lake Storage Gen2 帳戶。

> [!Note] 
> 一旦您選取 Data Lake Storage Gen2 作為**主要儲存體類型**，就無法選取 Data Lake Storage Gen1 帳戶作為其他儲存體。

## <a name="creating-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>建立搭配 Data Lake Storage Gen2 的 HDInsight 叢集

## <a name="using-the-azure-portal"></a>使用 Azure 入口網站

若要建立以 Data Lake Storage Gen2 作為儲存體的 HDInsight 叢集，請使用下列步驟來建立正確設定的 Data Lake Storage Gen2 帳戶。

1. 建立使用者指派的受控識別 (如果您還沒有的話)。 請參閱[使用 Azure 入口網站對使用者指派的受控識別建立、列出、刪除或指派角色](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)。

    ![建立使用者指派的受控識別](./media/hdinsight-hadoop-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

1. 建立 Azure Data Lake Storage Gen2 儲存體帳戶。 請確定 [階層式檔案系統] 選項已啟用。 請參閱[快速入門：建立 Azure Data Lake Storage Gen2 儲存體帳戶](../storage/blobs/data-lake-storage-quickstart-create-account.md)，以取得詳細資訊。

    ![顯示在 Azure 入口網站建立儲存體帳戶的螢幕擷取畫面](./media/hdinsight-hadoop-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)
 
1. 將受控識別指派給儲存體帳戶上的**儲存體 Blob 資料參與者 (預覽)** 角色。 請參閱[使用 RBAC 來管理 Azure Blob 和佇列資料的存取權限 (預覽)](../storage/common/storage-auth-aad-rbac.md#assign-a-role-scoped-to-the-storage-account-in-the-azure-portal)

    1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的儲存體帳戶。
    1. 選取儲存體帳戶，然後選取 [存取控制 (IAM)]，以顯示帳戶的存取控制設定。 選取 [角色指派] 索引標籤，以查看角色指派的清單。
    
        ![顯示儲存體存取控制設定的螢幕擷取畫面](./media/hdinsight-hadoop-data-lake-storage-gen2/portal-access-control.png)
    
    1. 按一下 [新增角色指派] 按鈕新增角色。
    1. 在 [新增角色指派] 視窗中，選取 [儲存體 Blob 資料參與者 (預覽)] 角色。 然後，選取包含受控識別和儲存體帳戶的訂用帳戶。 接著，搜尋並找出您先前建立的使用者指派受控識別。 最後，選取受控識別，此項目將會列在 [選取的成員] 下方。
    
        ![顯示如何指派 RBAC 角色的螢幕擷取畫面](./media/hdinsight-hadoop-data-lake-storage-gen2/add-rbac-role2.png)
    
    1. 按一下 [檔案] 。 您選取的使用者指派身分識別此時會列在 [參與者] 角色下方。

    1. 完成此初始設定後，您可以透過入口網站建立叢集。 此叢集必須與儲存體帳戶位在相同的 Azure 區域中。 在叢集建立功能表的 [儲存體] 區段中，選取下列選項︰
        
        * 針對 [主要儲存體類型]，請按一下 [Azure Data Lake Storage Gen2]。
        * 在 [選取儲存體帳戶] 下方搜尋新建的 Data Lake Storage Gen2 儲存體帳戶，並加以選取。
        
            ![搭配 Azure HDInsight 使用 Data Lake Storage Gen2 的儲存體設定](./media/hdinsight-hadoop-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
        
        * 在 [身分識別] 下方選取正確的訂用帳戶，和新建的使用者指派受控識別。
        
            ![搭配 Azure HDInsight 使用 Data Lake Storage Gen2 的身分識別設定](./media/hdinsight-hadoop-data-lake-storage-gen2/managed-identity-cluster-creation.png)

### <a name="using-a-resource-manager-template-deployed-with-azure-cli"></a>使用以 Azure CLI 部署的 Resource Manager 範本

您可以[在這裡下載範例範本檔案](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json)，以及[在這裡下載範例參數檔案](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json)。 使用範本之前，請將字串 `<SUBSCRIPTION_ID>` 取代為您實際的 Azure 訂用帳戶識別碼。 此外，請將字串 `<PASSWORD>` 取代為您所選擇的密碼，以設定您將用來登入叢集的登入密碼，以及 SSH 密碼。

下方的程式碼片段會執行下列初始步驟：

1. 登入您的 Azure 帳戶。
1. 設定將用來執行建立作業的使用中訂用帳戶。
1. 為新的部署活動 `hdinsight-deployment-rg` 建立新的資源群組。
1. 建立使用者受控服務識別 (MSI) `test-hdinsight-msi`。
1. 在 Azure CLI 中新增擴充功能，以使用 Data Lake Storage Gen2 的功能。
1. 使用 `--hierarchical-namespace true` 旗標建立新的 Data Lake Storage Gen2 帳戶 `hdinsightadlsgen2`。

```azurecli
az login
az account set --subscription <subscription_id>

#create resource group
az group create --name hdinsight-deployment-rg --location eastus

# Create managed identity
az identity create -g hdinsight-deployment-rg -n test-hdinsight-msi

az extension add --name storage-preview

az storage account create --name hdinsightadlsgen2 \
    --resource-group hdinsight-deployment-rg \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

接著，登入入口網站，並將新的 MSI 新增至儲存體帳戶的**儲存體 Blob 資料參與者 (預覽)** 角色，如前述位於[使用 Azure 入口網站](hdinsight-hadoop-use-data-lake-storage-gen2.md#using-the-azure-portal)下的步驟 3 所說明。

在入口網站中完成 MSI 角色指派之後，請使用下列程式碼片段繼續部署範本。

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group hdinsight-deployment-rg \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>HDInsight 中的 Data Lake Storage Gen2 存取控制

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Data Lake Storage Gen2 支援哪些種類的權限？

Azure Data Lake Storage Gen2 可實作支援 Azure 角色型存取控制 (RBAC) 和 POSIX 型存取控制清單 (ACL) 的存取控制模型。 Data Lake Storage Gen1 僅支援用來控制資料存取的存取控制清單。

Azure 角色型存取控制 (RBAC) 會使用角色指派將權限集有效地套用至 Azure 資源的使用者、群組和服務主體。 一般而言，這些 Azure 資源會限定於最上層資源 (例如 Azure 儲存體帳戶)。 就 Azure 儲存體和 Azure Data Lake Storage Gen2 而言，這項機制已擴充至檔案系統資源。

 如需與 RBAC 的檔案權限有關的詳細資訊，請參閱 [Azure 角色型存取控制 (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac)。

如需與 ACL 的檔案權限有關的詳細資訊，請參閱[檔案和目錄的存取控制清單](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)。


### <a name="how-do-i-control-access-to-my-data-in-gen2"></a>在 Gen2 中如何對我的資料進行存取控制？

您的 HDInsight 叢集對於 Data Lake Storage Gen2 中所含檔案的存取能力，可透過受控識別來控制。 受控識別是在 Azure AD 中註冊的身分識別，其認證由 Azure 所管理。 您不需要在 Azure AD 中註冊服務主體以及維護認證 (例如憑證)。

Azure 服務有兩種受控識別：系統指派和使用者指派。 Azure HDInsight 會利用使用者指派的受控識別來存取 Azure Data Lake Storage Gen2。 使用者指派的受控識別會以獨立 Azure 資源的形式建立。 透過建立程序，Azure 會在所使用訂用帳戶信任的 Azure AD 租用戶中建立身分識別。 建立身分識別之後，即可將它指派給一個或多個 Azure 服務執行個體。 使用者指派的身分識別與獲指派此身分識別的 Azure 服務執行個體，兩者的生命週期分開管理。 如需受控識別的詳細資訊，請參閱[適用於 Azure 資源的受控識別如何運作](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-worka-namehow-does-it-worka)。

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-using-hive-or-other-services"></a>如何為 Azure AD 使用者設定使用 Hive 或其他服務在 Data Lake Storage Gen2 中查詢資料的權限？

請使用 Azure AD 安全性群組作為 ACL 中指派的主體。 請勿直接以檔案存取權限指派個別的使用者或服務主體。 使用 AD 安全性群組來控制權限流程時，您可以直接新增和移除使用者或服務主體，而不需要將 ACL 重新套用至整個目錄結構。 您只需要從適當的 Azure AD 安全性群組新增或移除使用者即可。 ACL 是不可繼承的，因此若要重新套用 ACL，必須更新每個檔案和子目錄的 ACL。

## <a name="next-steps"></a>後續步驟

* [搭配 Azure HDInsight 叢集使用 Azure Data Lake Storage Gen2 預覽](../storage/blobs/data-lake-storage-use-hdi-cluster.md)
* [Azure HDInsight 與 Data Lake Storage Gen2 預覽版的整合 - ACL 和安全性更新](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Azure Data Lake Storage Gen2 預覽版簡介](../storage/blobs/data-lake-storage-introduction.md)
