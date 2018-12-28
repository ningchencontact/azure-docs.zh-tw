---
title: Azure Service Fabric 中的隨選備份 | Microsoft Docs
description: 使用 Service Fabric 的備份與還原功能，依照需求備份您的應用程式資料。
services: service-fabric
documentationcenter: .net
author: aagup
manager: timlt
editor: aagup
ms.assetid: 02DA262A-EEF6-4F90-842E-FFC4A09003E5
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 35d97f1da6b5d1c75073264c70e1cd1607d5fe0d
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730498"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Azure Service Fabric 中的隨選備份

您可備份可靠具狀態服務和 Reliable Actors 的資料，以處理災害或資料遺失情況。

Service Fabric 具備各項功能，可供[定期備份資料](service-fabric-backuprestoreservice-quickstart-azurecluster.md)及依照需求備份資料。 隨選備份很實用，因為它可防範由於基礎服務或其環境中的計劃性變更所造成的_資料遺失_/_資料損毀_。

在與服務或服務環境相關的任何手動觸發作業之前，隨選備份功能對於擷取服務狀態很有幫助。 如同變更服務二進位檔，也就是服務的升級或降級；因為這保護資料，以防範應用程式程式碼中的 Bug 造成資料損毀。

## <a name="triggering-on-demand-backup"></a>觸發隨選備份

隨選備份需要儲存體詳細資料以便上傳備份檔案。 隨選備份會發生於定期備份原則中指定的儲存體，或隨選備份要求中指定的儲存體。

### <a name="on-demand-backup-to-storage-specified-by-periodic-backup-policy"></a>定期備份原則所指定的儲存體隨選備份

您可針對要備份至定期備份原則中指定的儲存體的額外隨需基礎備份，要求可靠具狀態服務或 Reliable Actor 的分割區。 

下列案例接續[啟用可靠具狀態服務和 Reliable Actors 的定期備份](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors)中所提的範例，其中的分割區已啟用備份原則，並以所需的頻率在 Azure 儲存體中進行備份。

[BackupPartition] (https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) API 可以觸發分割區識別碼 `974bd92a-b395-4631-8a7f-53bd4ae9cf22` 的隨選備份。 

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

[GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API 可追蹤[隨選備份進度](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress)。

### <a name="on-demand-backup-to-specified-storage"></a>隨選備份至指定的儲存體

您可針對可靠具狀態服務或 Reliable Actor 的分割區要求隨選備份以及儲存體資訊。 儲存體資訊應當作隨選備份要求的一部分提供。

[BackupPartition] (https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition) API 可以使用如下所示的 Azure 儲存體資訊，觸發分割區識別碼 `974bd92a-b395-4631-8a7f-53bd4ae9cf22` 的隨選備份。

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$OnDemandBackupRequest = @{
    BackupStorage = $StorageInfo
}

$body = (ConvertTo-Json $OnDemandBackupRequest)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

[GetBackupProgress](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress) API 可追蹤[隨選備份進度](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress)。


## <a name="tracking-on-demand-backup-progress"></a>追蹤隨選備份進度

可靠具狀態服務或 Reliable Actor 的分割區一次只接受一個隨選備份要求。 只有當目前的隨選備份要求完成時，才可以接受另一個要求。 

同時在不同的分割區上可以觸發多個隨選備份要求。

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4" 
 
$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

隨選備份要求的進度可以是下列其中一種狀態

* **Accepted** - 備份已在分割區上啟動且正在進行中。
    ```
    BackupState             : Accepted
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          : 
    EpochOfLastBackupRecord : 
    LsnOfLastBackupRecord   : 0
    FailureError            : 
    ```
    
* **Success/ Failure/ Timeout** - 要求的隨選備份可以下列任何狀態完成。 每個狀態都有下列重要性和回應詳細資料。

    * **Success** - Success 備份狀態表示已成功備份分割區狀態。 回應會提供分割區的 __BackupEpoch__ 和 __BackupLSN__ 以及 UTC 時間。
        ```
        BackupState             : Success
        TimeStampUtc            : 2018-11-21T20:00:01Z
        BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
        BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
        EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
        LsnOfLastBackupRecord   : 36
        FailureError            : 
        ```
        
    * **Failure** - Failure 備份狀態表示在分割區狀態的備份期間發生失敗。 回應中會陳述失敗的原因。
        ```
        BackupState             : Failure
        TimeStampUtc            : 0001-01-01T00:00:00Z
        BackupId                : 00000000-0000-0000-0000-000000000000
        BackupLocation          : 
        EpochOfLastBackupRecord : 
        LsnOfLastBackupRecord   : 0
        FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
        ```
       
    * **Timeout** - Timeout 備份狀態表示無法在指定的時間範圍中建立分割區狀態備份，預設逾時值為 10 分鐘。 在此案例中，建議您以更大的隨選備份要求 [BackupTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) 起始新的備份要求。

        ```
        BackupState             : Timeout
        TimeStampUtc            : 0001-01-01T00:00:00Z
        BackupId                : 00000000-0000-0000-0000-000000000000
        BackupLocation          : 
        EpochOfLastBackupRecord : 
        LsnOfLastBackupRecord   : 0
        FailureError            : @{Code=FABRIC_E_TIMEOUT; Message=The request of backup has timed out.}
        ```

## <a name="next-steps"></a>後續步驟
- [了解定期備份組態](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [備份還原 REST API 參考](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

