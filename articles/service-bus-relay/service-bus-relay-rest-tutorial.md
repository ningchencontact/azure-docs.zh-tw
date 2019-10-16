---
title: 使用 Azure 轉送的 REST 教學課程 | Microsoft Docs
description: 建立可公開 REST 型介面的 Azure 服務匯流排轉送主機應用程式。
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 1312b2db-94c4-4a48-b815-c5deb5b77a6a
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2019
ms.author: spelluru
ms.openlocfilehash: a3daa7847ef037f0276792bf8173ad55aba0a944
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212916"
---
# <a name="azure-wcf-relay-rest-tutorial"></a>Azure WCF 轉送的 REST 教學課程

本教學課程說明如何建立可公開 REST 型介面的 Azure 轉送主應用程式。 REST 可讓 Web 用戶端 (例如 Web 瀏覽器) 透過 HTTP 要求存取服務匯流排 API。

本教學課程會使用 Windows Communication Foundation (WCF) REST 程式設計模型，在 Azure 轉送上建構 REST 服務。 如需詳細資訊，請參閱[WCF REST 程式設計模型](/dotnet/framework/wcf/feature-details/wcf-web-http-programming-model)和[設計和執行服務](/dotnet/framework/wcf/designing-and-implementing-services)。

您會在本教學課程中執行下列工作：

> [!div class="checklist"]
>
> * 安裝本教學課程的必要條件。
> * 建立轉送命名空間。
> * 定義以 REST 為基礎的 WCF 服務合約。
> * 執行以 REST 為基礎的 WCF 合約。
> * 裝載並執行以 REST 為基礎的 WCF 服務。
> * 執行並測試服務。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，您需要下列必要條件：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶](https://azure.microsoft.com/free/)。
* [Visual Studio 2015 或更新版本](https://www.visualstudio.com)。 本教學課程中的範例使用 Visual Studio 2019。
* 適用於 .NET 的 Azure SDK。 從 [SDK 下載頁面](https://azure.microsoft.com/downloads/)進行安裝。

## <a name="create-a-relay-namespace"></a>建立轉送命名空間

若要開始在 Azure 中使用轉送功能，首先必須建立服務命名空間。 命名空間提供範圍容器，可在應用程式內進行 Azure 資源定址。 請依照[這裡的指示](relay-create-namespace-portal.md)來建立轉送命名空間。

## <a name="define-a-rest-based-wcf-service-contract-to-use-with-azure-relay"></a>定義 REST 架構的 WCF 服務合約以與 Azure 轉送搭配使用

當您建立 WCF REST 樣式服務時，必須定義合約。 合約會指定主機支援哪些作業。 服務作業類似于 web 服務方法。 定義具有C++、 C#或 Visual Basic 介面的合約。 介面中的每個方法會對應一個特定服務作業。 將[ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute)屬性套用至每個介面，並將[OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute)屬性套用至每個作業。 

> [!TIP]
> 如果介面中具有[ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute)的方法不具有[OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute)，則不會公開該方法。 這項工作所使用的程式碼會出現在程式後面的範例中。

WCF 合約與 REST 樣式合約之間的主要差異在於，為 [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) 新增了一個屬性：[WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute)。 這個屬性可讓您將介面的方法對應至介面另一端的方法。 這個範例會使用[WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute)屬性，將方法連結至`HTTP GET`。 這種方法可讓服務匯流排正確地抓取和解讀傳送至介面的命令。

### <a name="to-create-a-contract-with-an-interface"></a>使用介面建立合約

1. 以系統管理員身分啟動 Microsoft Visual Studio。 若要這麼做，請以滑鼠右鍵按一下 [Visual Studio 程式] 圖示，然後選取 [以**系統管理員身分執行**]。
1. 在 Visual Studio 中，選取 [**建立新專案**]。
1. 在 [**建立新專案**] 中，選擇 [**主控台應用程式（.NET Framework）** ]， C#然後選取 **[下一步]** 。
1. 將專案命名為*ImageListener*。 使用預設**位置**，然後選取 [**建立**]。

   若為C#專案，Visual Studio 會建立*Program.cs*檔案。 這個類別包含空的 `Main()` 方法，即正確建置主控台應用程式專案所需的方法。

1. 在**方案總管**中，以滑鼠右鍵按一下**ImageListener**專案，然後選取 [**管理 NuGet 套件**]。
1. 選取 **[流覽]** ，然後搜尋並選擇 [ **windowsazure.storage**]。 選取 [**安裝**]，並接受使用規定。

    此步驟會將參考新增至服務匯流排和*system.servicemodel .dll*。 此套件會自動新增服務匯流排程式庫和 WCF `System.ServiceModel`的參考。

1. 將的參考`System.ServiceModel.Web.dll`明確加入至專案。 在**方案總管**中，以滑鼠右鍵按一下專案資料夾底下的 [**參考**]，然後選取 [**加入參考**]。
1. 在 [**新增參考**] 中選取 [**架構**]，然後在 [**搜尋**] 中輸入*system.servicemodel。* 選取 [System.ServiceModel.Web] 核取方塊，然後按一下 [確定]。

接下來，對專案進行下列程式碼變更：

1. 在 Program.cs 檔案`using`的頂端新增下列語句。

    ```csharp
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    * [System.ServiceModel](/dotnet/api/system.servicemodel) 是可讓您以程式設計方式存取 WCF 基本功能的命名空間。 WCF 轉送會使用 WCF 的許多物件和屬性來定義服務合約。 您會在大部分轉送應用程式中使用此命名空間。
    * [System.servicemodel](/dotnet/api/system.servicemodel.channels)可協助定義通道，這是您用來與 Azure 轉送和用戶端網頁瀏覽器通訊的物件。
    * [System.servicemodel](/dotnet/api/system.servicemodel.web)包含的類型可讓您建立 Web 應用程式。

1. 將命名空間重新`Microsoft.ServiceBus.Samples`命名為。 `ImageListener`

    ```csharp
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. 直接在命名空間宣告的左大括弧後面，定義名為`IImageContract`的新介面，並`ServiceContractAttribute`將屬性套用至具有值的`https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1`介面。 

    ```csharp
    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

    命名空間值與您的整個程式碼範圍中使用的命名空間不同。 命名空間值是此合約的唯一識別碼，而且應該有版本資訊。 如需詳細資訊，請參閱[服務版本設定](/dotnet/framework/wcf/service-versioning)。 明確指定命名空間可避免將預設命名空間值新增至合約名稱。

1. 在介面中，針對`IImageContract`合約在介面中公開的單一作業宣告方法，並將`OperationContract`屬性套用至您想要公開為公用服務匯流排合約一部分的方法。 `IImageContract`

    ```csharp
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

1. 在屬性中， `WebGet`加入值。 `OperationContract`

    ```csharp
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

   加入值可讓轉送服務將 HTTP GET 要求路由傳送至`GetImage`，以及將的傳回值`GetImage` `HTTP GETRESPONSE`轉譯為回復。 `WebGet` 稍後在本教學課程中，您將使用網頁瀏覽器來存取此方法，並在瀏覽器中顯示影像。

1. 緊接著 `IImageContract` 定義後面，宣告從 `IImageContract` 和 `IClientChannel` 介面繼承的通道。

    ```csharp
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

   通道是 WCF 物件，服務和用戶端可透過它彼此傳遞資訊。 稍後，會在主機應用程式中建立通道。 Azure 轉送接著會使用此通道，將 HTTP GET 要求從瀏覽器傳遞至`GetImage`您的執行。 轉送也會使用通道來取得`GetImage`傳回值，並將它轉譯`HTTP GETRESPONSE`成用戶端瀏覽器的。

1. 選取 [**組建** > **組建方案**]，以確認您的工作到目前為止是否正確無誤。

### <a name="example-that-defines-a-wcf-relay-contract"></a>定義 WCF 轉送合約的範例

下列程式碼示範定義 WCF 轉送合約的基本介面。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="implement-the-rest-based-wcf-service-contract"></a>實作 REST 型 WCF 服務合約

若要建立 REST 樣式 WCF 轉送服務，請先使用介面建立合約。 下一步是實作介面。 此程式牽涉到建立名為`ImageService`的類別，以執行使用者`IImageContract`定義的介面。 在您執行合約之後，接著就可以使用*app.config*檔案來設定介面。 此設定檔包含應用程式的必要資訊。 這項資訊包括服務的名稱、合約的名稱，以及用來與轉送服務通訊的通訊協定類型。 這項工作所使用的程式碼會出現在程式後面的範例中。

如同先前的步驟，在執行 REST 樣式合約和 WCF 轉送合約之間沒有什麼差異。

### <a name="to-implement-a-rest-style-service-bus-contract"></a>實作 REST 樣式服務匯流排合約

1. 緊接在 `IImageContract` 介面的定義之後，建立名為 `ImageService` 的新類別。 `ImageService` 類別會實作 `IImageContract` 介面。

    ```csharp
    class ImageService : IImageContract
    {
    }
    ```

    類似於其他介面實作，您可以在不同的檔案中實作定義。 不過，在此教學課程中，實作會出現在與介面定義和 `Main()` 方法相同的檔案中。

1. 將[ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute)屬性套用至`IImageService`類別，以指出類別是 WCF 合約的執行。

    ```csharp
    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    如先前所述，此命名空間不是傳統的命名空間。 它是識別合約的 WCF 架構的一部分。 如需詳細資訊，請參閱[資料合約名稱](/dotnet/framework/wcf/feature-details/data-contract-names/)。

1. 將 *.jpg*影像新增至您的專案。 此檔案是服務在接收瀏覽器中顯示的圖片。

   1. 以滑鼠右鍵按一下您的專案，然後選取 [**新增**]。
   1. 然後選取 [**現有專案**]。
   1. 使用 [**加入現有專案**] 流覽至適當的 .jpg，然後選取 [**新增**]。 新增檔案時，請從 [**檔案名**] 旁的下拉式清單中選取 [**所有**檔案]。

   本教學課程的其餘部分會假設影像的名稱是*image .jpg*。 如果您有不同的檔案，您必須重新命名映像，或變更您的程式碼來彌補。

1. 若要確定執行中的服務可以找到該影像檔案，請在**方案總管**以滑鼠右鍵按一下該影像檔，然後選擇 [**屬性**]。 在 [**屬性**] 中，將 [**複製到輸出目錄**] 設定為 [**更新時複製**]。

1. 使用中的程式[來建立具有介面的合約](#to-create-a-contract-with-an-interface)，以便將對*system.web*元件的參考加入至專案。

1. 新增下列相關聯`using`的語句：

    ```csharp
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

1. `ImageService`在類別中，新增下列會載入點陣圖並準備將它傳送至用戶端瀏覽器的函式：

    ```csharp
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";
   
        Image bitmap;
   
        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```

1. 直接在先前的程式碼之後，在`GetImage` `ImageService`類別中新增下列方法，以傳回包含該影像的 HTTP 訊息。

    ```csharp
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);
   
        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";
   
        return stream;
    }
    ```

    這個執行會`MemoryStream`使用來抓取映射，並準備將它串流處理至瀏覽器。 它會從零開始資料流程位置、將資料流程內容宣告為 *.jpg*，然後串流處理資訊。

1. 選取 [**組建** > ] [**組建方案**]。

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>為服務匯流排上執行的 Web 服務定義組態

1. 在**方案總管**中，按兩下**app.config**以在 [Visual Studio 編輯器] 中開啟檔案。

    *App.config*檔案包含服務名稱、端點和系結。 端點是 Azure 轉送公開的位置，可供用戶端和主機彼此通訊。 系結是用來通訊的通訊協定類型。 此處的主要差異是設定的服務端點會參考 [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) 繫結。

1. `<system.serviceModel>` XML 元素是定義一或多個服務的 WCF 元素。 在這裡，它是用來定義服務名稱和端點。 在`<system.serviceModel>`元素的底部，但仍在內`<system.serviceModel>`，新增`<bindings>`具有下列內容的元素：

    ```xml
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```

    此內容會定義應用程式中使用的系結。 您可以定義多個系結，但在本教學課程中，您只會定義一個系結。

    先前的程式碼會定義 WCF 轉送的 [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding)`relayClientAuthenticationType`系結`None`，並將設為。 此設定表示使用此系結的端點不需要用戶端認證。

1. 在 `<bindings>` 元素後方加入 `<services>` 元素。 類似於繫結，您可以在單一組態檔中定義多個服務。 不過，您在本教學課程中只會定義一個。

    ```xml
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```

    此內容會設定服務，以使用先前定義的`webHttpRelayBinding`預設值。 它也會使用在`sbTokenProvider`下一個步驟中定義的預設。

1. 在專案之後， `<behaviors>`建立具有下列內容的元素，並將`SAS_KEY`取代為共用存取簽章（SAS）金鑰。 `<services>` 若要從[Azure 入口網站][Azure portal]取得 SAS 金鑰，請參閱[取得管理認證](service-bus-relay-tutorial.md#get-management-credentials)。

    ```xml
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```

1. 仍然在*app.config*的`<appSettings>`元素中，將整個連接字串值取代為您先前從入口網站取得的連接字串。

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY"/>
    </appSettings>
    ```

1. 選取 [**組建** > **組建方案**] 以建立整個解決方案。

### <a name="example-that-implements-the-rest-based-wcf-service-contract"></a>執行以 REST 為基礎之 WCF 服務合約的範例

下列程式碼顯示使用`WebHttpRelayBinding`系結在服務匯流排上執行之 REST 架構服務的合約和服務實現。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

下列範例顯示與服務相關聯的*app.config*檔案。

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app settings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY>"/>
    </appSettings>
</configuration>
```

## <a name="host-the-rest-based-wcf-service-to-use-azure-relay"></a>裝載 REST 架構的 WCF 服務以使用 Azure 轉送

本節說明如何使用 WCF 轉送的主控台應用程式來執行 web 服務。 這一節中所撰寫程式碼的完整清單會出現在程式後面的範例中。

### <a name="to-create-a-base-address-for-the-service"></a>建立服務的基底位址

1. 在 `Main()` 函式宣告中，建立變數來儲存專案的命名空間。 務必以您先前建立的轉送命名空間名稱來取代 `yourNamespace`。

    ```csharp
    string serviceNamespace = "yourNamespace";
    ```

    服務匯流排會使用您的命名空間名稱來建立唯一 URI。

1. 為根據命名空間之服務的基底位址建立 `Uri` 執行個體。

    ```csharp
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>建立和設定 Web 服務主機

仍然在`Main()`中，使用本節稍早建立的 URI 位址來建立 web 服務主機。
  
```csharp
WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
```

服務主機是具現化主機應用程式的 WCF 物件。 這個範例會將您要建立的主機類型（也就是`ImageService`），以及您想要公開主機應用程式的位址傳遞給它。

### <a name="to-run-the-web-service-host"></a>執行 Web 服務主機

1. 仍然在`Main()`中，新增下列程式程式碼以開啟服務。

    ```csharp
    host.Open();
    ```

    服務現在正在執行。

1. 顯示訊息，指出服務正在執行，以及如何停止服務。

    ```csharp
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. 完成時，關閉服務主機。

    ```csharp
    host.Close();
    ```

### <a name="example-of-the-service-contract-and-implementation"></a>服務合約和執行範例

下列範例包括教學課程先前步驟的服務合約和實作，以及在主控台應用程式中主控服務。 將下列程式碼編譯到名為*ImageListener*的可執行檔中。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

## <a name="run-and-test-the-service"></a>執行及測試服務

建置解決方案後，請執行下列命令以執行應用程式：

1. 選取 F5，或流覽至可執行檔位置*ImageListener\bin\Debug\ImageListener.exe*，以執行服務。 讓應用程式保持執行狀態，因為下一個步驟中需要它。
1. 將位址從命令提示字元複製並貼到瀏覽器以查看映像。
1. 當您完成時，請在 [命令提示字元] 視窗中選取 Enter 鍵以關閉應用程式。

## <a name="next-steps"></a>後續步驟

既然您已經建置了使用 Azure 轉送服務的應用程式，請參閱下列文章以進一步了解：

* [什麼是 Azure 轉送？](relay-what-is-it.md)
* [使用 Azure WCF 轉送將內部部署 WCF REST 服務公開給外部用戶端](service-bus-relay-tutorial.md)

[Azure portal]: https://portal.azure.com
