---
title: 使用 .NET 建立帳戶 SAS-Azure 儲存體
description: 瞭解如何使用 .NET 用戶端程式庫建立帳戶共用存取簽章 (SAS)。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f75b472a49dfb360ccda21e73286880fcd506153
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990763"
---
# <a name="create-an-account-sas-with-net"></a>使用 .NET 建立帳戶 SAS

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

本文說明如何使用儲存體帳戶金鑰, 搭配[適用于 .net 的 Azure 儲存體用戶端程式庫](/dotnet/api/overview/azure/storage/client)來建立帳戶 SAS。

## <a name="create-an-account-sas"></a>建立帳戶 SAS

若要建立容器的帳戶 SAS, 請呼叫[CloudStorageAccount. cloudblobcontainer.getsharedaccesssignature](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.getsharedaccesssignature)方法。

下列程式碼範例會建立適用於 Blob 和檔案服務的帳戶 SAS，並提供用戶端權限讀取、寫入和列出權限來存取服務層級 API。 帳戶 SAS 會將通訊協定限制為 HTTPS，因此必須使用 HTTPS 提出要求。 請記得使用您自己的值來取代角括弧中的預留位置值:

```csharp
static string GetAccountSASToken()
{
    // To create the account SAS, you need to use Shared Key credentials. Modify for your account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<storage-account>;AccountKey=<account-key>";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create a new access policy for the account.
    SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
        {
            Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
            Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
            ResourceTypes = SharedAccessAccountResourceTypes.Service,
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Protocols = SharedAccessProtocol.HttpsOnly
        };

    // Return the SAS token.
    return storageAccount.GetSharedAccessSignature(policy);
}
```

## <a name="use-an-account-sas-from-a-client"></a>從用戶端使用帳戶 SAS

若要使用帳戶 SAS 來存取 Blob 服務的服務層級 Api, 請使用 SAS 和儲存體帳戶的 Blob 儲存體端點來建立 Blob 服務用戶端物件。 請記得使用您自己的值來取代角括弧中的預留位置值:

```csharp
static void UseAccountSAS(string sasToken)
{
    // Create new storage credentials using the SAS token.
    StorageCredentials accountSAS = new StorageCredentials(sasToken);
    // Use these credentials and the account name to create a Blob service client.
    CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
    CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

    // Now set the service properties for the Blob client created with the SAS.
    blobClientWithSAS.SetServiceProperties(new ServiceProperties()
    {
        HourMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        MinuteMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        Logging = new LoggingProperties()
        {
            LoggingOperations = LoggingOperations.All,
            RetentionDays = 14,
            Version = "1.0"
        }
    });

    // The permissions granted by the account SAS also permit you to retrieve service properties.
    ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
    Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
    Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
    Console.WriteLine(serviceProperties.HourMetrics.Version);
}
```

## <a name="see-also"></a>另請參閱

- [使用共用存取簽章 (SAS) 授與 Azure 儲存體資源的有限存取權](storage-sas-overview.md)
- [建立帳戶 SAS](/rest/api/storageservices/create-account-sas)
