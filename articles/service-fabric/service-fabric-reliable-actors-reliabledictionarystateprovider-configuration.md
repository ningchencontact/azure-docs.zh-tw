---
title: 變更 Azure Service Fabric 動作項目中的 ReliableDictionaryActorStateProvider 設定 | Microsoft Docs
description: 了解設定 ReliableDictionaryActorStateProvider 類型的 Azure Service Fabric 可設定狀態的動作項目。
services: Service-Fabric
documentationcenter: .net
author: sumukhs
manager: chackdan
editor: ''
ms.assetid: 79b48ffa-2474-4f1c-a857-3471f9590ded
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/2/2017
ms.author: sumukhs
ms.openlocfilehash: 4e39357a765ec85aa64055b1aa422d8d7a01c116
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60727126"
---
# <a name="configuring-reliable-actors--reliabledictionaryactorstateprovider"></a>設定 Reliable Actors - ReliableDictionaryActorStateProvider
您可以在指定之動作項目的 Config 資料夾下，變更 Visual Studio 封裝根中所產生的 settings.xml，來修改 ReliableDictionaryActorStateProvider 的預設組態。

Azure Service Fabric 运行时在 settings.xml 文件中查找预定义的节名称，并在创建基础运行时组件时使用这些配置值。

> [!NOTE]
> 請 **不要** 刪除或修改在 Visual Studio 方案中產生之 settings.xml 檔案中的下列組態區段名稱。
> 
> 

也有一些全域設定會影響 ReliableDictionaryActorStateProvider 的組態。

## <a name="global-configuration"></a>全域組態
在 KtlLogger 區段下，叢集的叢集資訊清單中所指定的全域組態。 它可設定共用記錄檔位置和大小，加上記錄器所使用的全域記憶體限制。 請注意，叢集資訊清單中的變更會影響使用 ReliableDictionaryActorStateProvider 的所有服務，以及可靠的可設定狀態服務。

群集清单是单个 XML 文件，可保留适用于群集中所有节点和服务的设置与配置。 此檔案通常稱為 ClusterManifest.xml。 您可以查看叢集的叢集資訊清單使用 Get-ServiceFabricClusterManifest powershell 命令。

### <a name="configuration-names"></a>組態名稱
| 名稱 | 單位 | 預設值 | 備註 |
| --- | --- | --- | --- |
| WriteBufferMemoryPoolMinimumInKB |KB |8388608 |以核心模式配置給記錄器寫入緩衝區記憶體集區的最小 KB 數。 此記憶體集區用於在寫入至磁碟之前快取狀態資訊。 |
| WriteBufferMemoryPoolMaximumInKB |KB |沒有限制 |記錄器寫入緩衝區記憶體集區可以成長的的大小上限。 |
| SharedLogId |GUID |"" |指定用來識別預設共用記錄檔的唯一 GUID，用於叢集中所有節點上的所有 Reliable Services (不會在其服務特定組態中指定 SharedLogId)。 如果有指定 SharedLogId，則也必須指定 SharedLogPath。 |
| SharedLogPath |完整路徑名稱 |"" |指定完整路徑，其中共用記錄檔用於叢集中所有節點上的所有 Reliable Services (不會在其服務特定組態中指定 SharedLogPath)。 不過，如果有指定 SharedLogPath，則也必須指定 SharedLogId。 |
| SharedLogSizeInMB |MB |8192 |指定以靜態方式配置給共用記錄檔的磁碟空間 MB 數。 此值必須是 2048 或更大。 |

### <a name="sample-cluster-manifest-section"></a>範例叢集資訊清單一節
```xml
   <Section Name="KtlLogger">
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
     <Parameter Name="SharedLogSizeInMB" Value="16383"/>
   </Section>
```

### <a name="remarks"></a>備註
記錄器有配置自未分頁核心記憶體的記憶體全域集區，可供節點上的所有 Reliable Services 使用，在寫入至與 Reliable Service 複本相關聯的專用記錄檔之前快取狀態資料。 集區大小由 WriteBufferMemoryPoolMinimumInKB 和 WriteBufferMemoryPoolMaximumInKB 設定控制。 WriteBufferMemoryPoolMinimumInKB 指定此記憶體集區的初始大小，以及記憶體集區可能會縮小的大小下限。 WriteBufferMemoryPoolMaximumInKB 是記憶體集區可能會成長的大小上限。 每個開啟的 Reliable Service 複本都可能會增加記憶體集區的大小，增加幅度從系統決定的數量到 WriteBufferMemoryPoolMaximumInKB。 如果記憶體集區的記憶體需求大於可用的記憶體，會延遲記憶體要求，直到記憶體可供使用。 因此，如果寫入緩衝區記憶體集區對特定組態而言太小，則效能可能會受到影響。

SharedLogId 和 SharedLogPath 設定永遠會一起使用，以便為叢集中的所有節點定義 GUID 和預設共用記錄檔的位置。 預設共用記錄檔可用於不在特定服務 settings.xml 中指定設定的所有 Reliable Services。 為了達到最佳效能，共用記錄檔應該放在共用記錄檔專用的磁碟上，以減少爭用情形。

SharedLogSizeInMB 會指定要預先配置給所有節點上之預設共用記錄檔的磁碟空間數量。  若要指定SharedLogSizeInMB，不需要指定 SharedLogId 和 SharedLogPath。

## <a name="replicator-security-configuration"></a>複寫器安全性組態
复制器安全配置用于保护在复制过程中使用的通信通道的安全。 這表示服務將無法看到彼此的複寫流量，並且也會確保高度可用資料的安全。
依預設，空白的安全性組態區段會妨礙複寫安全性。

> [!IMPORTANT]
> 在 Linux 節點上，憑證必須是 PEM 格式。 若要深入了解如何尋找和設定適用於 Linux 的憑證，請參閱[在 Linux 上設定憑證](./service-fabric-configure-certificates-linux.md)。 
> 

### <a name="section-name"></a>區段名稱
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>复制器配置
複寫器組態用來設定負責將狀態複寫和保存至本機，讓動作項目狀態提供者變得高度可靠的複寫器。
默认配置由 Visual Studio 模板生成，并应已足够。 本部分介绍了可用于调整复制器的其他配置。

### <a name="section-name"></a>區段名稱
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>組態名稱
| 名稱 | 單位 | 預設值 | 備註 |
| --- | --- | --- | --- |
| BatchAcknowledgementInterval |秒 |0.015 |次要複寫器收到作業後，將通知傳回給主要複寫器前所等待的時間間隔。 任何要在此間隔內傳送給作業處理的其他通知，會集中以一個回應傳送。 |
| ReplicatorEndpoint |N/A |無預設值--必要的參數 |主要/次要複寫器將用於與複本集中其他複寫器通訊的 IP 位址與連接埠。 這應該參考服務資訊清單中的 TCP 資源端點。 請參閱 [服務資訊清單資源](service-fabric-service-manifest-resources.md) ，深入了解如何在服務資訊清單中定義端點資源。 |
| MaxReplicationMessageSize |位元組 |50 MB |單一訊息可傳輸的複寫資料大小上限。 |
| MaxPrimaryReplicationQueueSize |操作的数量 |8192 |主要佇列中作業數目上限。 主要複寫器收到所有次要複寫器的通知後，系統便會釋放作業。 此值必須大於 64 且為 2 的乘冪。 |
| MaxSecondaryReplicationQueueSize |作業數目 |16384 |次要佇列中作業數目上限。 透過持續性讓狀態成為高可用性後，系統便會釋放作業。 此值必須大於 64 且為 2 的乘冪。 |
| CheckpointThresholdInMB |MB |200 |创建状态检查点后的日志文件空间量。 |
| MaxRecordSizeInKB |KB |1024 |複寫器可以寫入記錄檔中的最大記錄大小。 此值必須是 4 的倍數且大於 16。 |
| OptimizeLogForLowerDiskUsage |布尔 |true |為 true 時會設定記錄檔，以便使用 NTFS 疏鬆檔案來建立複本的專用記錄檔。 這會降低檔案實際使用的磁碟空間量。 為 false 時，將以固定配置建立檔案，以提供最佳寫入效能。 |
| SharedLogId |GUID |"" |指定用於識別此複本共用記錄檔的唯一 GUID。 服務通常不應使用此設定。 不過，如果有指定 SharedLogId，則也必須指定 SharedLogPath。 |
| SharedLogPath |完整路徑名稱 |"" |指定建立此複本共用記錄檔的完整路徑。 服務通常不應使用此設定。 不過，如果有指定 SharedLogPath，則也必須指定 SharedLogId。 |

## <a name="sample-configuration-file"></a>示例配置文件
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="180" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```

## <a name="remarks"></a>備註
BatchAcknowledgementInterval 參數會控制複寫延遲性。 值為 '0' 時延遲可能性最低，但代價是降低輸送量 (隨著必須傳送與處理的通知訊息增加，每個訊息包含的通知會變少)。
BatchAcknowledgementInterval 的值越大，整體複寫輸送量越高，代價是作業延遲變高。 這會直接轉換成交易認可的延遲。

CheckpointThresholdInMB 參數可控制複寫器可用來將狀態資訊儲存在複本專用記錄檔中的磁碟空間量。 若此值大於預設值，可能會在將複本新增至集合時，加速重新設定的時間。 這是因為記錄中具有更多可用的歷程記錄作業而造成部分狀態傳送的緣故。 這可能會在當機之後增加複本的復原時間。

如果您將 OptimizeForLowerDiskUsage 設定為 true，將會過度佈建記錄檔空間，讓作用中的複本可以在其記錄檔中儲存較多狀態資訊，而非作用中的複本則會使用較少的磁碟空間。 如此可以在節點上裝載多個複本。 如果您將 OptimizeForLowerDiskUsage 設定為 false，狀態資訊會更快寫入記錄檔。

MaxRecordSizeInKB 設定會定義複寫器可以寫入記錄檔的記錄大小上限。 在大部分情況下，預設的 1024 KB 記錄大小是最佳作法。 不過，如果服務造成更大的資料項目成為狀態資訊的一部分，則可能需要增加此值。 讓 MaxRecordSizeInKB 小於 1024 的好處不大，因為較小的記錄只會使用較小記錄所需的空間。 預期此值只會在極少數的情況下需要變更。

SharedLogId 和 SharedLogPath 設定永遠會一起使用，以便讓服務使用與節點的預設共用記錄檔不同的共用記錄檔。 如需最佳效率，請儘可能讓所有服務指定相同的共用記錄檔。 共用記錄檔應該放在共用記錄檔專用的磁碟上，以減少磁頭移動爭用情形。 預期這些值只會在極少數的情況下需要變更。

