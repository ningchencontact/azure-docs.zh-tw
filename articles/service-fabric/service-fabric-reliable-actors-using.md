---
title: Azure Service Fabric 動作項目功能實作 | Microsoft Docs
description: 說明如何撰寫您自己的動作項目，以繼承 StatefulService 時的相同方式來實作服務層級功能。
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 60989825ecdefa853d0e2df99619e3cb350cb6bc
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="implementing-service-level-features-in-your-actor-service"></a>在動作項目服務中實作服務層級功能
如[服務分層](service-fabric-reliable-actors-platform.md#service-layering)所述，動作項目服務本身是可靠的服務。  您可以自行撰寫衍生自 `ActorService` 的服務，並以您在繼承 StatefulService 時所使用的相同方式來實作服務層級功能，例如︰

- 服務備份與還原。
- 適用於所有動作項目的共用功能，例如斷路器。
- 遠端處理程序會在動作項目服務本身，以及每個個別動作項目上進行呼叫。

## <a name="using-the-actor-service"></a>使用動作項目服務
動作項目執行個體具有其執行所在之動作項目服務的存取權。 透過動作項目服務，動作項目執行個體可以程式設計方式取得服務內容。 服務內容包含分割區識別碼、服務名稱、應用程式名稱及其他 Service Fabric 平台特定的資訊：

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```
```Java
CompletableFuture<?> MyActorMethod()
{
    UUID partitionId = this.getActorService().getServiceContext().getPartitionId();
    String serviceTypeName = this.getActorService().getServiceContext().getServiceTypeName();
    URI serviceInstanceName = this.getActorService().getServiceContext().getServiceName();
    String applicationInstanceName = this.getActorService().getServiceContext().getCodePackageActivationContext().getApplicationName();
}
```

就像所有 Reliable Services，動作項目服務必須在 Service Fabric 執行階段，利用某個服務類型來註冊。 為了讓動作項目服務執行您的動作項目執行個體，也必須向動作項目服務註冊動作項目類型。 `ActorRuntime` 註冊方法會替動作項目執行這項工作。 在最簡單的情況下，您只需註冊動作項目類型，並隱含地使用具備預設設定的動作項目服務︰

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>().GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```

或者，您可以使用註冊方法提供的 Lambda，來建構自己的動作項目服務。 您可以設定動作項目服務，以及明確地建構您的動作項目執行個體，您可以透過其建構函式，將相依性插入動作項目︰

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new ActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
static class Program
{
    private static void Main()
    {
      ActorRuntime.registerActorAsync(
              MyActor.class,
              (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
              timeout);

        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="actor-service-methods"></a>動作項目服務方法
動作項目服務會實作 `IActorService` (C#) 或 `ActorService` (Java)，而它會接著實作 `IService` (C#) 或 `Service` (Java)。 這是 Reliable Services 遠端處理功能所使用的介面，能夠在服務方法上進行遠端程序呼叫。 它包含的服務層級方法可使用服務遠端處理功能從遠端呼叫，並且可供您[列舉](service-fabric-reliable-actors-enumerate.md)和[刪除](service-fabric-reliable-actors-delete-actors.md)動作項目。


## <a name="custom-actor-service"></a>自訂動作項目服務
您可以使用動作項目註冊 lambda，註冊自 `ActorService` (C#) 和 `FabricActorService` (Java) 延伸的自訂動作項目服務。 在此自訂動作項目服務中，您可以撰寫繼承 `ActorService` (C#) 或 `FabricActorService` (Java) 的服務，以實作自己的服務層級功能。 自訂動作項目服務會繼承來自 `ActorService` (C#) 或 `FabricActorService` (Java) 的所有動作項目執行階段功能，並可用來實作您自己的服務方法。

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```
```Java
class MyActorService extends FabricActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, BiFunction<FabricActorService, ActorId, ActorBase> newActor)
    {
         super(context, typeInfo, newActor);
    }
}
```

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new MyActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="implementing-actor-backup-and-restore"></a>實作動作項目備份與還原
自訂動作項目服務會利用已經存在於 `ActorService`中的遠端處理接聽程式，藉以公開用來備份動作項目資料的方法。  如需範例，請參閱[備份與還原動作項目](service-fabric-reliable-actors-backup-and-restore.md)。

## <a name="actor-using-remoting-v2-stack"></a>使用 Remoting V2 堆疊的動作項目
透過 2.8 NuGet 套件，使用者現在可以使用 Remoting V2 堆疊，它的效能更好，並提供像是自訂序列化等功能。 Remoting V2 與現有的 Remoting 堆疊 (我們稱現在的是 V1 Remoting 堆疊) 不相容。

若要使用 Remoting V2 堆疊，必須進行下列變更。
 1. 在動作項目介面上新增下列組件屬性。
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
   ```

 2. 建置和升級 ActorService 和動作項目用戶端專案來開始使用 V2 堆疊。

### <a name="actor-service-upgrade-to-remoting-v2-stack-without-impacting-service-availability"></a>動作項目服務升級至 Remoting V2 堆疊，不會影響服務可用性。
此項變更是 2 個步驟的升級作業。 依照列出的步驟順序進行。

1.  在動作項目介面上新增下列組件屬性。 此屬性將會為 ActorService 啟動兩個接聽程式：V1 (現有) 與 V2 接聽程式。 透過下列變更升級 ActorService。

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.CompatListener,RemotingClient = RemotingClient.V2Client)]
  ```

2. 完成上述升級之後，升級 ActorClients。
此步驟可確保動作項目 Proxy 使用 Remoting V2 堆疊。

3. 此為選用步驟。 變更上述屬性以移除 V1 接聽程式。

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
    ```

## <a name="next-steps"></a>後續步驟
* [動作項目狀態管理](service-fabric-reliable-actors-state-management.md)
* [動作項目生命週期與記憶體回收](service-fabric-reliable-actors-lifecycle.md)
* [動作項目 API 參考文件](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [.NET 範例程式碼 (英文)](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java 範例程式碼 (英文)](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
