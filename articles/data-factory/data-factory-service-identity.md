---
title: Data factory 受控身分識別 |Microsoft Docs
description: 針對 Azure Data Factory，以了解受管理的身分識別。
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: jingwang
ms.openlocfilehash: 3c1bb38eb12ce77d172257706cd458cebda4bd8c
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59260743"
---
# <a name="managed-identity-for-data-factory"></a>適用於 Data Factory 的受控身分識別

這篇文章可協助您了解什麼是受管理的身分識別 （前身為受控服務身分識別/MSI） 的資料處理站和其運作方式。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>概觀

在建立資料處理站時，就可以建立受控身分識別以及建立 factory。 受管理的身分識別是向 Azure Active Directory，在 managed 應用程式，並代表這個特定的 data factory。

Data factory 受控身分識別有益於下列功能：

- [在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)，在此情況下 data factory 受控身分識別使用於 Azure 金鑰保存庫驗證。
- 連接器，包括 [Azure Blob 儲存體](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、[Azure Data Lake Storage Gen2 ](connector-azure-data-lake-storage.md)、[Azure SQL Database](connector-azure-sql-database.md)，以及 [Azure SQL 資料倉儲](connector-azure-sql-data-warehouse.md)。
- [網路活動](control-flow-web-activity.md)。

## <a name="generate-managed-identity"></a>產生受控身分識別

Data factory 受控身分識別的產生過程如下所示：

- 建立資料處理站，透過時**Azure 入口網站或 PowerShell**、 受管理身分識別一律會自動建立。
- 時建立資料處理站，透過**SDK**、 受管理會建立身分識別，只有當您指定"Identity = new factoryidentity （）"中建立的 factory 物件。 請參閱 [.NET 快速入門 - 建立資料處理站](quickstart-create-data-factory-dot-net.md#create-a-data-factory)中的範例。
- 建立資料處理站，透過時**REST API**、 受管理將會建立身分識別，只有當您在要求主體中指定"identity"區段。 請參閱 [REST 快速入門 - 建立資料處理站](quickstart-create-data-factory-rest-api.md#create-a-data-factory)中的範例。

如果您發現您的 data factory 沒有受控身分識別相關聯遵循[擷取受管理的身分識別](#retrieve-managed-identity)指令，您可以明確地產生一個，方法是以程式設計方式透過身分識別啟動器更新資料處理站：

- [產生使用 PowerShell 的受管理身分識別](#generate-managed-identity-using-powershell)
- [產生受控身分識別，使用 REST API](#generate-managed-identity-using-rest-api)
- [產生受控身分識別使用 Azure Resource Manager 範本](#generate-managed-identity-using-an-azure-resource-manager-template)
- [產生使用 SDK 的受管理身分識別](#generate-managed-identity-using-sdk)

>[!NOTE]
>- 無法修改受管理的身分識別。 更新已有受控身分識別的資料處理站不會有任何影響，受管理的身分識別會維持不變。
>- 如果您更新 data factory 已有受控身分識別，但未指定處理站物件中的"identity"參數，或未在 REST 要求本文中指定"identity"區段時，您會收到錯誤。
>- 當您刪除資料處理站時，相關聯的受管理身分識別會一併刪除。

### <a name="generate-managed-identity-using-powershell"></a>產生使用 PowerShell 的受管理身分識別

呼叫**組 AzDataFactoryV2**同樣地，命令就會看到新產生的"Identity"欄位：

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-managed-identity-using-rest-api"></a>產生受控身分識別，使用 REST API

在要求本文中以 "identity" 區段呼叫下面的 API：

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**要求本文**：新增 "identity": { "type":"SystemAssigned" }。

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**回應**： 受管理的身分識別會自動建立，並據此填入"identity"區段。

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>產生受控身分識別使用 Azure Resource Manager 範本

**範本**：新增 "identity": { "type":"SystemAssigned" }。

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```

### <a name="generate-managed-identity-using-sdk"></a>產生使用 SDK 的受管理身分識別

以 Identity=new FactoryIdentity() 呼叫資料處理站 create_or_update 函式。 使用 .NET 的範例程式碼：

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>擷取受管理的身分識別

您可以擷取受管理的身分識別，從 Azure 入口網站或以程式設計的方式。 下列各節會顯示一些範例。

>[!TIP]
> 如果您沒有看到受管理的身分識別[產生受控身分識別](#generate-managed-identity)藉由更新您的處理站。

### <a name="retrieve-managed-identity-using-azure-portal"></a>使用 Azure 入口網站的擷取受控身分識別

您可以找到受管理的身分識別資訊，從 Azure 入口網站]-> [您的 data factory]-> [屬性：

- 受控識別物件識別碼
- 受控識別租用戶
- **受管理身分識別應用程式識別碼**> 複製此值

![擷取受管理的身分識別](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-managed-identity-using-powershell"></a>使用 PowerShell 的擷取受控身分識別

當您取得特定的 data factory，如下所示，將會傳回受管理的身分識別主體識別碼和租用戶識別碼：

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

複製主體識別碼，然後以主體識別碼作為參數來執行下面的 Azure Active Directory 命令，以取得您可用於授與存取權的 **ApplicationId**：

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>後續步驟
請參閱下列主題介紹何時及如何使用 data factory 受控身分識別：

- [在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)
- [來回複製資料從 Azure Data Lake Store 使用 Azure 資源驗證的受管理的身分識別](connector-azure-data-lake-store.md)

請參閱[管理 Azure 資源概觀的身分識別](/azure/active-directory/managed-identities-azure-resources/overview)的背景資訊管理的身分識別用於 data factory 管理身分識別的 Azure 資源為基礎。 
