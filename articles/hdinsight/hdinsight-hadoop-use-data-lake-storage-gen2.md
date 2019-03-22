---
title: 搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2
description: 了解如何搭配 Azure HDInsight 叢集使用 Azure Data Lake 儲存體 Gen2。
services: hdinsight
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: hrasheed
ms.openlocfilehash: 4e8649096d4f7de49c9cf0d569422919f865bb3b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/18/2019
ms.locfileid: "58094087"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2

Azure Data Lake 儲存體 Gen2 是專門用來建置在 Azure Blob 儲存體上的巨量資料分析的雲端儲存體服務。 Data Lake 儲存體 Gen2 結合 Azure Blob 儲存體和 Azure Data Lake 儲存體 Gen1 的功能。 最終的服務從 Azure Data Lake 儲存體 Gen1，提供功能，例如檔案系統語意、 目錄層級和檔案層級安全性及延展性，以及低成本、 分層式儲存體、 高可用性和災害復原功能從 Azure Blob 儲存體。

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 可用性

Data Lake 儲存體 Gen2 可從幾乎所有的 Azure HDInsight 叢集類型的儲存體選項做為預設值和其他儲存體帳戶。 不過，HBase，可以有只有一個 Data Lake 儲存體 Gen2 帳戶。

> [!Note] 
> 選取做為 Data Lake 儲存體 Gen2 之後您**主要儲存體類型**，您無法選取 Data Lake 儲存體 Gen1 帳戶作為其他儲存體。

## <a name="create-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>建立 HDInsight 叢集搭配 Data Lake 儲存體 Gen2

## <a name="use-the-azure-portal"></a>使用 Azure 入口網站

若要建立使用 Data Lake 儲存體 Gen2 儲存體的 HDInsight 叢集，請遵循下列步驟來設定 Data Lake 儲存體 Gen2 帳戶。

1. 建立使用者指派的受控識別 (如果您還沒有的話)。 請參閱[使用 Azure 入口網站對使用者指派的受控識別建立、列出、刪除或指派角色](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)。

    ![建立使用者指派的受控識別](./media/hdinsight-hadoop-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

1. 建立 Azure Data Lake Storage Gen2 儲存體帳戶。 請確定**階層式命名空間**啟用選項。 如需詳細資訊，請參閱[快速入門：建立 Azure Data Lake Storage Gen2 儲存體帳戶](../storage/blobs/data-lake-storage-quickstart-create-account.md)。

    ![顯示在 Azure 入口網站建立儲存體帳戶的螢幕擷取畫面](./media/hdinsight-hadoop-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)
 
1. 將受控識別指派給儲存體帳戶上的**儲存體 Blob 資料擁有者 (預覽)** 角色。 如需詳細資訊，請參閱[使用 RBAC 來管理 Azure Blob 和佇列資料的存取權限 (預覽)](../storage/common/storage-auth-aad-rbac.md)。

    1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您的儲存體帳戶。
    1. 選取儲存體帳戶，然後選取**存取控制 (IAM)** 顯示帳戶的存取控制設定。 選取 [角色指派] 索引標籤，以查看角色指派的清單。
    
        ![顯示儲存體存取控制設定的螢幕擷取畫面](./media/hdinsight-hadoop-data-lake-storage-gen2/portal-access-control.png)
    
    1. 選取  **+ 新增角色指派**按鈕以新增新的角色。
    1. 在 [新增角色指派] 視窗中，選取 [儲存體 Blob 資料擁有者 (預覽)] 角色。 然後，選取包含受控識別和儲存體帳戶的訂用帳戶。 接著，搜尋並找出您先前建立的使用者指派受控識別。 最後，選取 受管理的身分識別，並將會列在**選取的成員**。
    
        ![顯示如何指派 RBAC 角色的螢幕擷取畫面](./media/hdinsight-hadoop-data-lake-storage-gen2/add-rbac-role3.png)
    
    1. 選取 [ **儲存**]。 您選取的使用者指派身分識別此時會列在 [參與者] 角色下方。

    1. 完成此初始設定後，您可以透過入口網站建立叢集。 此叢集必須與儲存體帳戶位在相同的 Azure 區域中。 在叢集建立功能表的 [儲存體] 區段中，選取下列選項︰
        
        * 針對**主要儲存體類型**，選取**Azure Data Lake 儲存體 Gen2**。
        * 底下**選取儲存體帳戶**，搜尋並選取新建立的 Data Lake 儲存體 Gen2 儲存體帳戶。
        
            ![搭配 Azure HDInsight 使用 Data Lake Storage Gen2 的儲存體設定](./media/hdinsight-hadoop-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
        
        * 底下**識別**選取正確的訂用帳戶，新建立的使用者指派受管理的識別。
        
            ![搭配 Azure HDInsight 使用 Data Lake Storage Gen2 的身分識別設定](./media/hdinsight-hadoop-data-lake-storage-gen2/managed-identity-cluster-creation.png)

### <a name="use-an-azure-resource-manager-template-deployed-with-the-azure-cli"></a>使用 Azure Resource Manager 範本部署使用 Azure CLI

您可以[下載範例範本檔案](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json)並[下載範例參數檔](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json)。 之前使用範本時，取代字串`<SUBSCRIPTION_ID>`並提供您實際的 Azure 訂用帳戶識別碼。 此外，取代字串`<PASSWORD>`具有您所選的密碼，可以設定兩個登入到您的叢集，您將使用的密碼和 SSH 密碼。

下列程式碼片段會執行下列的初始步驟：

1. 登入您的 Azure 帳戶。
1. 設定作用中的訂用帳戶的建立作業將會完成。
1. 建立新的資源群組，名為新的部署活動`hdinsight-deployment-rg`。
1. 建立使用者指派給受控身分識別名為`test-hdinsight-msi`。
1. 將 Azure CLI 來使用 Data Lake 儲存體 Gen2 功能延伸模組。
1. 建立新的 Data Lake 儲存體 Gen2 帳戶名為`hdinsightadlsgen2`，使用`--hierarchical-namespace true`旗標。

```azurecli
az login
az account set --subscription <subscription_id>

# Create resource group
az group create --name hdinsight-deployment-rg --location eastus

# Create managed identity
az identity create -g hdinsight-deployment-rg -n test-hdinsight-msi

az extension add --name storage-preview

az storage account create --name hdinsightadlsgen2 \
    --resource-group hdinsight-deployment-rg \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

接下來，登入入口網站。 加入新使用者指派給受控身分識別來**儲存體 Blob 資料參與者 （預覽）** 儲存體帳戶下的步驟 3 中所述的角色[使用 Azure 入口網站](hdinsight-hadoop-use-data-lake-storage-gen2.md)。

指派的角色指派給使用者的受管理身分識別之後，請使用下列程式碼部署範本。

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group hdinsight-deployment-rg \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>在 HDInsight 中的 Data Lake 儲存體 Gen2 的存取控制

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Data Lake Storage Gen2 支援哪些種類的權限？

Data Lake 儲存體 Gen2 使用支援角色型存取控制 (RBAC) 和 POSIX 型存取控制清單 (Acl) 的存取控制模型。 Data Lake 儲存體 Gen1 支援存取控制清單，只是用來控制資料的存取權。

RBAC 會有效地套用至使用者、 群組和服務主體，適用於 Azure 資源的 權限集合中使用角色指派。 一般而言，這些 Azure 資源受限為最上層資源 （例如 Azure 儲存體帳戶）。 Azure 儲存體，以及在 Data Lake 儲存體 Gen2，這項機制已擴充到檔案系統資源。

 如需有關使用 RBAC 的檔案權限的詳細資訊，請參閱[Azure 角色型存取控制 (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac)。

如需有關使用 Acl 的檔案權限的詳細資訊，請參閱[上的檔案和目錄存取控制清單](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)。

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>如何在我的資料，在 Data Lake 儲存體 Gen2 控制存取權？

在 Data Lake 儲存體 Gen2 中存取檔案的 HDInsight 叢集的能力被透過受控身分識別。 受控身分識別是在 Azure Active Directory (Azure AD) 認證並由 Azure 管理中註冊的身分識別。 使用受管理的身分識別，您不需要在 Azure AD 中註冊服務主體，或維護認證，例如憑證。

Azure 服務都有兩種類型的受管理的身分識別： 系統指派與指派給使用者。 HDInsight 會使用指派給使用者來存取 Data Lake 儲存體 Gen2 的受管理身分識別。 使用者指派的受控識別會以獨立 Azure 資源的形式建立。 透過建立程序，Azure 會在所使用訂用帳戶信任的 Azure AD 租用戶中建立身分識別。 建立身分識別之後，即可將它指派給一個或多個 Azure 服務執行個體。

使用者指派的身分識別與獲指派此身分識別的 Azure 服務執行個體，兩者的生命週期分開管理。 如需有關受管理的身分識別的詳細資訊，請參閱[如何針對 Azure 資源的工作管理的身分識別？](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)。

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>如何設定 Azure AD 使用者的權限來查詢資料，Data Lake 儲存體 Gen2 中使用 Hive 或其他服務？

若要設定使用者的權限來查詢資料，作為在 Acl 中指定的主體中的 Azure AD 安全性群組。 不直接將檔案存取權限指派給個別使用者或服務主體。 當您使用 Azure AD 安全性群組來控制的權限的流程時，您可以新增和移除使用者或服務主體，而不重新 Acl 套用至整個目錄結構。 您只需要從適當的 Azure AD 安全性群組新增或移除使用者即可。 Acl 不繼承，所以重新套用 Acl 需要更新每個檔案和子目錄的 ACL。

## <a name="next-steps"></a>後續步驟

* [搭配 Azure HDInsight 叢集使用 Azure Data Lake Storage Gen2 預覽](../storage/blobs/data-lake-storage-use-hdi-cluster.md)
* [Azure HDInsight 與 Data Lake Storage Gen2 預覽版的整合 - ACL 和安全性更新](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Azure Data Lake Storage Gen2 預覽版簡介](../storage/blobs/data-lake-storage-introduction.md)
