---
title: 在 Azure Service Fabric 中還原備份 | Microsoft Docs
description: 使用 Service Fabric 的定期備份與還原功能，從您應用程式資料的備份還原資料。
services: service-fabric
documentationcenter: .net
author: aagup
manager: timlt
editor: aagup
ms.assetid: 802F55B6-6575-4AE1-8A8E-C9B03512FF88
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 69604decab354368f336b85bfa1497671f0c3101
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730503"
---
#  <a name="restoring-backup-in-azure-service-fabric"></a>在 Azure Service Fabric 中還原備份


Service Fabric 中的可靠具狀態服務和 Reliable Actors 可維護要求與回應或完整交易以外的可變動授權狀態。 如果具狀態服務長時間停止運作，或因為災害而遺失資訊，可能就需要將它還原至其狀態的最近可接受備份，才能在恢復運作後繼續提供服務。

例如，服務可以備份其資料，以針對下列情況提供防護：

- 發生整個 Service Fabric 叢集永久遺失的情況。 **(災害復原的情況 - DR)**
- 永久遺失多數的服務分割區複本。 **(資料遺失的情況)**
- 不小心刪除或損毀狀態的系統管理錯誤。 例如，具備足夠權限的系統管理員錯誤地刪除服務。**(資料遺失的情況)**
- 服務中造成資料損毀的錯誤。 例如，當服務程式碼升級而開始將錯誤資料寫入「可靠的集合」時，就可能發生資料損毀。 在這種情況下，可能必須將程式碼和資料還原成先前的狀態。 **(資料損毀的情況)**


## <a name="prerequisites"></a>必要條件
* 若要觸發，應該對叢集啟用還原「錯誤分析服務 (FAS)」
* 「備份還原服務 (BRS)」 應該已取得要還原的備份
* 還原只能在分割區觸發。

## <a name="triggering-restore"></a>觸發還原

還原可適用於下列任何情況 
* 「災害復原」(DR) 事件中的資料還原
* 「資料損毀 / 資料遺失」事件中的資料還原



### <a name="data-restore-in-the-event-of-disaster-recovery-dr"></a>「災害復原」(DR) 事件中的資料還原
在遺失整個 Service Fabric 叢集的事件中，可以將可靠具狀態服務和 Reliable Actors 的分割區資料還原至替代叢集。 可以從[具有備份儲存體詳細資料的 GetBackupAPI](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation) 列舉中選取所需的備份。 備份列舉適用於應用程式、服務或分割區。

假設遺失的叢集是[啟用可靠具狀態服務和 Reliable Actor 的定期備份](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors)中所提到的叢集，該叢集已部署 `SampleApp`，其中的分割區已啟用備份原則，並且在 Azure 儲存體中進行備份。 


請執行下列 PowerShell 指令碼來叫用 REST API，以列舉針對遺失的 Service Fabric 叢集中 `SampleApp` 應用程式內所有分割區建立的備份。 列舉 API 需要儲存體資訊，其中會儲存應用程式的備份，以便列舉可用的備份。 

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$BackupEntity = @{
    EntityKind = 'Application'
    ApplicationName='fabric:/SampleApp'
}

$BackupLocationAndEntityInfo = @{
    Storage = $StorageInfo
    BackupEntity = $BackupEntity
}

$body = (ConvertTo-Json $BackupLocationAndEntityInfo)
$url = "https://myalternatesfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```
上述執行的範例輸出：

```
BackupId                : b9577400-1131-4f88-b309-2bb1e943322c
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 20.55.16.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3334
CreationTimeUtc         : 2018-04-06T20:55:16Z
FailureError            : 
*
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            : 
*
BackupId                : 69436834-c810-4163-9386-a7a800f78359
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.25.36.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3764
CreationTimeUtc         : 2018-04-06T21:25:36Z
FailureError            : 
```



如需觸發還原，我們需要選擇所需的備份。 讓目前災害復原 (DR) 的所需備份成為下列備份

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            : 
```

對於還原 API，我們需要提供 __BackupId__ 和 __BackupLocation__ 詳細資料。 需要根據[分割區配置](service-fabric-concepts-partitioning.md#get-started-with-partitioning)來選擇替代叢集中的分割區。 使用者需負責根據原始遺失叢集中的分割區配置，選擇要從替代叢集還原備份的目標分割區。

假設替代叢集上的分割區識別碼為 `1c42c47f-439e-4e09-98b9-88b8f60800c6`，其藉由比較「定界分割 (UniformInt64Partition)」的高鍵值和低鍵值對應至原始叢集分割區識別碼`974bd92a-b395-4631-8a7f-53bd4ae9cf22`。

對於「具名分割」，比較名稱值可找出替代叢集中的目標分割區。

下列[還原 API](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition) 會要求對備份叢集的分割區進行還原

```powershell 
$RestorePartitionReference = @{ 
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' 
} 
 
$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 
 
Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 
還原的進度可以是 [TrackRestoreProgress](service-fabric-backup-restore-service-trigger-restore.md#tracking-restore-progress)

### <a name="data-restore-in-the-event-of-data-corruption--data-loss"></a>「資料損毀 / 資料遺失」事件中的資料還原

在「資料遺失」或「資料損毀」的情況下，可以將可靠具狀態服務和 Reliable Actors 的分割區資料還原至任何所選的備份。 下列案例接續[啟用可靠具狀態服務和 Reliable Actor 的定期備份](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors)中所提的範例，其中的分割區已啟用備份原則，並以所需的頻率在 Azure 儲存體中進行備份。 

已從 [GetBackupAPI](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups) 的輸出中選取所需的備份。 在此案例中，會從過去的相同叢集中產生備份。
如需觸發還原，我們需要從清單中選擇所需的備份。 讓目前「資料遺失」 / 「資料損毀」的所需備份成為下列備份

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            : 
```

對於還原 API，我們需要提供 __BackupId__ 和 __BackupLocation__ 詳細資料。 因為叢集已啟用備份，所以 Service Fabric 的「備份還原服務 (BRS)」會依據相關聯的備份原則找出正確的儲存體位置。

```powershell
$RestorePartitionReference = @{ 
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4', 
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' 
} 
 
$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4" 
 
Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

還原的進度可以是 [TrackRestoreProgress](service-fabric-backup-restore-service-trigger-restore.md#tracking-restore-progress)


## <a name="tracking-restore-progress"></a>追蹤還原進度

可靠具狀態服務或 Reliable Actor 的分割區一次只接受一個還原要求。 只有當目前的還原要求完成時，才可以接受另一個要求。 同時在不同的分割區上可以觸發多個還原要求。

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4" 
 
$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
 
$restoreResponse = (ConvertFrom-Json $response.Content) 
$restoreResponse | Format-List
```

還原要求會依下列順序進行

1. __Accepted__ - Accepted 還原狀態表示已使用正確的要求參數觸發所要求的還原。
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
2. __InProgress__ - InProgress 還原狀態表示分割區將會經歷要求中所提備份的還原。 分割區將會回報 dataloss 狀態。
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. __Success__/ __Failure__/ __Timeout__ - 要求的還原可以下列任何狀態完成。 每個狀態都有下列重要性和回應詳細資料。
       
    * __Success__ - Success 還原狀態表示已重新取得分割區狀態。 回應會提供分割區的 RestoreEpoch 和 RestordLSN 以及 UTC 時間。 
    
        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```
        
    *. __Failure__ - Failure  還原狀態表示還原要求失敗。 要求中會陳述失敗的原因。
        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    *. __Timeout__ - _Timeout_ 還原狀態表示要求已逾時。 建議使用具有較大 [RestoreTimeout](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout) 的新還原要求；預設逾時為 10 分鐘。 建議您先確定分割區是脫離資料遺失狀態，再重新要求還原。
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="auto-restore"></a>自動還原

您可針對「自動還原」設定 Service Fabric 叢集中可靠具狀態服務和 Reliable Actors 的分割區。 建立備份原則時，原則可將 `AutoRestore` 設定為 true。  啟用分割區的「自動還原」，可在回報資料遺失時，從最新備份還原資料。
 
 [備份原則中的自動還原啟用](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)


[RestorePartition API 參考](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-restorepartition)
[GetPartitionRestoreProgress API 參考](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress)

## <a name="next-steps"></a>後續步驟
- [了解定期備份組態](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [備份還原 REST API 參考](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)
