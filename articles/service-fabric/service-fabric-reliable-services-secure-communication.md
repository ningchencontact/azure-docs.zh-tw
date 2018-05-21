---
title: 保護 Azure Service Fabric 中服務的遠端作業通訊 | Microsoft Docs
description: 瞭解如何保護在 Azure Service Fabric 叢集中所執行之可靠服務的服務遠端作業通訊。
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: timlt
editor: vturecek
ms.assetid: fc129c1a-fbe4-4339-83ae-0e69a41654e0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: suchiagicha
ms.openlocfilehash: cd7211ecda61ab2cca0f97e292d9ce2c47ed6933
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2018
---
# <a name="secure-service-remoting-communications-for-a-service"></a>保護服務的服務遠端作業通訊
> [!div class="op_single_selector"]
> * [Windows 上的 C# ](service-fabric-reliable-services-secure-communication.md)
> * [在 Linux 上使用 Java](service-fabric-reliable-services-secure-communication-java.md)
>
>

安全性是通訊最為重視的其中一個部分。 Reliable Services 應用程式架構會提供可用來改善安全性的一些預先建置通訊堆疊和工具。 本文探討如何改善使用服務遠端作業時的安全性。

我們將使用現有[範例](service-fabric-reliable-services-communication-remoting.md)說明如何設定 Reliable Services 的遠端處理功能。 若要協助保護使用服務遠端處理時的服務安全，請遵循下列步驟︰

1. 建立 `IHelloWorldStateful`介面，這個介面會定義將在您的服務上用於遠端程序呼叫的方法。 您的服務將使用在 `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` 命名空間中宣告的 `FabricTransportServiceRemotingListener`。 這是提供遠端功能的 `ICommunicationListener` 實作。

    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }

    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }

        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```
2. 新增接聽程式設定和安全性認證。

    確定您想要用來協助保護服務通訊安全的憑證已安裝在叢集的所有節點上。 有兩種方式可提供接聽程式設定和安全性認證：

   1. 直接在服務程式碼中提供它們：

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           FabricTransportRemotingListenerSettings  listenerSettings = new FabricTransportRemotingListenerSettings
           {
               MaxMessageSize = 10000000,
               SecurityCredentials = GetSecurityCredentials()
           };
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(context,this,listenerSettings))
           };
       }

       private static SecurityCredentials GetSecurityCredentials()
       {
           // Provide certificate details.
           var x509Credentials = new X509Credentials
           {
               FindType = X509FindType.FindByThumbprint,
               FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
               StoreLocation = StoreLocation.LocalMachine,
               StoreName = "My",
               ProtectionLevel = ProtectionLevel.EncryptAndSign
           };
           x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
           x509Credentials.RemoteCertThumbprints.Add("9FEF3950642138446CC364A396E1E881DB76B483");
           return x509Credentials;
       }
       ```
   2. 使用 [組態封裝](service-fabric-application-and-service-manifests.md)提供它們：

       在 settings.xml 檔案中新增 `TransportSettings` 區段。

       ```xml
       <Section Name="HelloWorldStatefulTransportSettings">
           <Parameter Name="MaxMessageSize" Value="10000000" />
           <Parameter Name="SecurityCredentialsType" Value="X509" />
           <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
           <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
           <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
           <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
           <Parameter Name="CertificateStoreName" Value="My" />
           <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
           <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
       </Section>
       ```

       在此情況下， `CreateServiceReplicaListeners` 方法看起來像這樣：

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(
                       context,this,FabricTransportRemotingListenerSettings .LoadFrom("HelloWorldStatefulTransportSettings")))
           };
       }
       ```

        如果您在 settings.xml 檔案中新增 `TransportSettings` 區段，則 `FabricTransportRemotingListenerSettings ` 預設會載入此區段中的所有設定。

        ```xml
        <!--"TransportSettings" section .-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        在此情況下， `CreateServiceReplicaListeners` 方法看起來像這樣：

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                return new[]{
                        new ServiceReplicaListener(
                            (context) => new FabricTransportServiceRemotingListener(context,this))};
            };
        }
        ```
3. 如果在安全服務上使用遠端堆疊來呼叫方法，而不是使用 `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` 類別來建立服務 Proxy，請使用 `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`。 傳入包含 `SecurityCredentials` 的 `FabricTransportRemotingSettings`。

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "9FEF3950642138446CC364A396E1E881DB76B483",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
    x509Credentials.RemoteCertThumbprints.Add("4FEF3950642138446CC364A396E1E881DB76B48C");

    FabricTransportRemotingSettings transportSettings = new FabricTransportRemotingSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    如果用戶端程式碼正在當作服務一部分執行，則可以從 settings.xml 檔案中載入 `FabricTransportRemotingSettings` 。 建立與服務程式碼類似的 HelloWorldClientTransportSettings 區段，如前所示。 對用戶端程式碼進行下列變更：

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    如果用戶端未當作服務一部分執行，則您可以在 client_name.exe 所在的同一位置中建立 client_name.settings.xml 檔案。 然後在該檔案中建立 TransportSettings 區段。

    與此服務類似，如果您在用戶端 settings.xml/client_name.settings.xml 中新增 `TransportSettings` 區段，則 `FabricTransportRemotingSettings` 預設會載入此區段中的所有設定。

    在該情況下，先前的程式碼甚至會更進一步地簡化：  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

接著請參閱[在 Reliable Services 中搭配 OWIN 使用 Web API](service-fabric-reliable-services-communication-webapi.md)。
