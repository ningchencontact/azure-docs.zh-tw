---
title: 搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2
description: 了解如何将 Azure Data Lake Storage Gen2 与 Azure HDInsight 群集配合使用。
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: hrasheed
ms.openlocfilehash: 6452e73b2429ab89a466b1c3b59cc892eca31205
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766935"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2

Azure Data Lake Storage Gen2 是构建在 Azure Blob 存储基础之上的，专用于大数据分析的云存储服务。 Data Lake Storage Gen2 将 Azure Blob 存储和 Azure Data Lake Storage Gen1 的功能组合在一起。 组合后的服务既可提供 Azure Data Lake Storage Gen1 的功能（例如文件系统语义、目录级别和文件级别的安全性及可伸缩性），也可提供 Azure Blob 存储的高性价比、分层存储、高可用性和灾难恢复功能。

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage Gen2 可用性

Data Lake Storage Gen2 能够以默认存储和附加存储帐户的形式用作几乎所有 Azure HDInsight 群集类型的存储选项。 但是，HBase 只能有一个 Data Lake Storage Gen2 帐户。

> [!Note] 
> 选择 Data Lake Storage Gen2 作为**主要存储类型**后，无法选择 Data Lake Storage Gen1 帐户作为附加存储。

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>通过 Azure 门户创建使用 Data Lake Storage Gen2 的群集

若要创建将 Data Lake Storage Gen2 用作存储的 HDInsight 群集，请遵循以下步骤配置 Data Lake Storage Gen2 帐户。

### <a name="create-a-user-managed-identity"></a>创建用户托管标识

建立使用者指派的受控識別 (如果您還沒有的話)。 請參閱[使用 Azure 入口網站對使用者指派的受控識別建立、列出、刪除或指派角色](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)。 如需有關如何管理 Azure HDInsight 中的身分識別工作的詳細資訊，請參閱 <<c0> [ 管理 Azure HDInsight 中的身分識別](hdinsight-managed-identities.md)。

![建立使用者指派的受控識別](./media/hdinsight-hadoop-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>建立 Data Lake Storage Gen2 帳戶

建立 Azure Data Lake Storage Gen2 儲存體帳戶。 确保已启用“分层命名空间”选项。 如需詳細資訊，請參閱[快速入門：建立 Azure Data Lake Storage Gen2 儲存體帳戶](../storage/blobs/data-lake-storage-quickstart-create-account.md)。

![顯示在 Azure 入口網站建立儲存體帳戶的螢幕擷取畫面](./media/hdinsight-hadoop-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="setup-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>在 Data Lake Storage Gen2 帐户中设置托管标识的权限

指派至受管理的身分識別**儲存體 Blob 資料擁有者**儲存體帳戶上的角色。 如需詳細資訊，請參閱[使用 RBAC 來管理 Azure Blob 和佇列資料的存取權限 (預覽)](../storage/common/storage-auth-aad-rbac.md)。

1. 在 [Azure 入口網站](https://portal.azure.com)中，移至您的儲存體帳戶。
1. 选择存储帐户，然后选择“访问控制(IAM)”以显示该帐户的访问控制设置。 選取 [角色指派] 索引標籤，以查看角色指派的清單。
    
    ![顯示儲存體存取控制設定的螢幕擷取畫面](./media/hdinsight-hadoop-data-lake-storage-gen2/portal-access-control.png)
    
1. 选择“+ 添加角色分配”按钮以添加一个新角色。
1. 在 **新增的角色指派**視窗中，選取**儲存體 Blob 資料擁有者**角色。 然後，選取包含受控識別和儲存體帳戶的訂用帳戶。 接著，搜尋並找出您先前建立的使用者指派受控識別。 最后，选择托管标识，它将在“选定成员”下列出。
    
    ![顯示如何指派 RBAC 角色的螢幕擷取畫面](./media/hdinsight-hadoop-data-lake-storage-gen2/add-rbac-role3.png)
    
1. 選取 [ **儲存**]。 现在，选定的用户分配的标识会列在选定的角色下。
1. 完成此初始設定後，您可以透過入口網站建立叢集。 此叢集必須與儲存體帳戶位在相同的 Azure 區域中。 在叢集建立功能表的 [儲存體] 區段中，選取下列選項︰
        
    * 对于“主要存储类型”，请选择“Azure Data Lake Storage Gen2”。
    * 在“选择存储帐户”下，搜索并选择新建的 Data Lake Storage Gen2 存储帐户。
        
        ![搭配 Azure HDInsight 使用 Data Lake Storage Gen2 的儲存體設定](./media/hdinsight-hadoop-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)
    
    * 在“标识”下，选择正确的订阅和新建的用户分配的托管标识。
        
        ![搭配 Azure HDInsight 使用 Data Lake Storage Gen2 的身分識別設定](./media/hdinsight-hadoop-data-lake-storage-gen2/managed-identity-cluster-creation.png)
        
> [!Note]
> 若要新增次要 Data Lake 儲存體 Gen2 帳戶，儲存體帳戶層級，只要指派新的 Data Lake 儲存體 Gen2 儲存體帳戶，您想要新增至稍早建立的受管理身分識別。請注意，不支援在 HDInsight 上加入次要 Data Lake 儲存體 Gen2 帳戶透過 [額外的儲存體帳戶] 刀鋒視窗。 

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>通过 Azure CLI 创建使用 Data Lake Storage Gen2 的群集

可以[下载示例模板文件](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json)并[下载示例参数文件](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json)。 使用模板之前，请将字符串 `<SUBSCRIPTION_ID>` 替换为实际的 Azure 订阅 ID。 此外，请将字符串 `<PASSWORD>` 替换为所选的密码，以设置用于登录到群集的密码以及 SSH 密码。

以下代码片段将会执行下述初始步骤：

1. 登录到 Azure 帐户。
1. 设置要在其中执行创建操作的活动订阅。
1. 为新的部署活动创建名为 `hdinsight-deployment-rg` 的新资源组。
1. 创建名为 `test-hdinsight-msi` 的用户分配的托管标识。
1. 将一个扩展添加到 Azure CLI，以使用 Data Lake Storage Gen2 的功能。
1. 使用 `--hierarchical-namespace true` 标志创建名为 `hdinsightadlsgen2` 的新 Data Lake Storage Gen2 帐户。

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

接下来，登录到门户。 加入新使用者指派給受控身分識別來**儲存體 Blob 資料參與者**儲存體帳戶下的步驟 3 中所述的角色[使用 Azure 入口網站](hdinsight-hadoop-use-data-lake-storage-gen2.md)。

为用户分配的托管标识分配角色后，使用以下代码片段部署模板。

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group hdinsight-deployment-rg \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>HDInsight 中 Data Lake Storage Gen2 的访问控制

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Data Lake Storage Gen2 支援哪些種類的權限？

Data Lake Storage Gen2 使用一个支持基于角色的访问控制 (RBAC) 和类似于 POSIX 的访问控制列表 (ACL) 的访问控制模型。 Data Lake Storage Gen1 仅支持用于控制数据访问的访问控制列表。

RBAC 使用角色分配有效地将权限集应用到 Azure 资源的用户、组和服务主体。 一般情况下，这些 Azure 资源限制为顶级资源（例如 Azure 存储帐户）。 对于 Azure 存储以及 Azure Data Lake Storage Gen2，此机制已扩展到文件系统资源。

 有关使用 RBAC 分配文件权限的详细信息，请参阅 [Azure 基于角色的访问控制 (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac)。

有关使用 ACL 分配文件权限的详细信息，请参阅[对文件和目录应用访问控制列表](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)。

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>如何在 Data Lake Storage Gen2 中控制对数据的访问？

HDInsight 群集在 Data Lake Storage Gen2 中访问文件的能力通过托管标识进行控制。 托管标识是在 Azure Active Directory (Azure AD) 中注册的标识，其凭据由 Azure 管理。 使用托管标识，无需在 Azure AD 中注册服务主体，也无需维护证书等凭据。

Azure 服务有两种类型的托管标识：系统分配的托管标识和用户分配的托管标识。 HDInsight 使用用户分配的托管标识来访问 Data Lake Storage Gen2。 使用者指派的受控識別會以獨立 Azure 資源的形式建立。 透過建立程序，Azure 會在所使用訂用帳戶信任的 Azure AD 租用戶中建立身分識別。 建立身分識別之後，即可將它指派給一個或多個 Azure 服務執行個體。

使用者指派的身分識別與獲指派此身分識別的 Azure 服務執行個體，兩者的生命週期分開管理。 如需有關受管理的身分識別的詳細資訊，請參閱[如何針對 Azure 資源的工作管理的身分識別？](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)。

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>如何设置 Azure AD 用户的权限，以使用 Hive 或其他服务在 Data Lake Storage Gen2 中查询数据？

若要为用户设置权限以查询数据，请将 Azure AD 安全组用作 ACL 中分配的主体。 不要直接向单个用户或服务主体分配文件访问权限。 使用 Azure AD 安全组控制权限流时，可以添加和删除用户或服务主体，而无需将 ACL 重新应用到整个目录结构。 您只需要從適當的 Azure AD 安全性群組新增或移除使用者即可。 ACL 不可继承，因此，重新应用 ACL 需要更新针对每个文件和子目录应用的 ACL。

## <a name="next-steps"></a>後續步驟

* [Azure HDInsight 與 Data Lake Storage Gen2 預覽版的整合 - ACL 和安全性更新](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Azure Data Lake Storage Gen2 簡介](../storage/blobs/data-lake-storage-introduction.md)
