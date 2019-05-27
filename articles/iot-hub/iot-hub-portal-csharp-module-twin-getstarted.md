---
title: 開始使用 Azure IoT 中樞模組身分識別和模組對應項 (入口網站和 .NET) | Microsoft Docs
description: 了解如何使用入口網站和 .NET，建立模組身分識別及更新模組對應項。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.openlocfilehash: 3b1872699b8b3ac72424f00cd74bb90b8b7be87f
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873165"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>透過入口網站和 .NET 裝置開始使用 IoT 中樞模組身分識別和模組對應項

> [!NOTE]
> [模組身分識別與模組對應項](iot-hub-devguide-module-twins.md)類似於 Azure IoT 中樞裝置身分識別與裝置對應項，但提供更精細的細微性。 雖然 Azure IoT 中樞裝置身分識別與裝置對應項可讓後端應用程式設定裝置並提供裝置狀況的可見性，但模組身分識別和模組對應項可提供裝置個別元件的上述功能。 在具備多個元件的可用裝置 (例如以作業系統為基礎的裝置或韌體裝置) 上，它允許每個元件有獨立的組態和狀況。
>

在本教學課程中，您將了解：

1. 如何在入口網站中建立模組身分識別。

2. 如何使用.NET 裝置 SDK 更新模組對應項，從您的裝置。

> [!NOTE]
> 如需可用來建置兩個應用程式，以在裝置上與解決方案後端執行的 Azure IoT SDK 詳細資訊，請參閱 [Azure IoT SDK](iot-hub-devguide-sdks.md)。
>

若要完成此教學課程，您需要下列項目：

* Visual Studio。
* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。)

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>擷取 IoT 中樞的連接字串

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中樞註冊新的裝置

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>在入口網站中建立模組身分識別

在一個裝置身分識別內，您可以建立最多 20 個模組身分識別。 按一下上方的 [新增模組身分識別] 按鈕，建立名稱為 **myFirstModule** 的第一個模組身分識別。

  ![裝置詳細資料](./media/iot-hub-portal-csharp-module-twin-getstarted/create-module-id.png)

儲存並按一下剛建立的模組身分識別。 您可以看到模組身分識別詳細資料。 儲存連接字串 - 主索引鍵。 在下一節中，它會使用於在裝置上設定您的模組。

  ![裝置詳細資料](./media/iot-hub-portal-csharp-module-twin-getstarted/module-details.png)

## <a name="update-the-module-twin-using-net-device-sdk"></a>使用 .NET 裝置 SDK 更新模組對應項

您已成功在 IoT 中樞中建立模組識別身分。 讓我們嘗試從您的模擬裝置與雲端通訊。 一旦建立模組身分識別，模組對應項會隱含地建立於 IoT 中樞中。 在這一節中，您會在模擬裝置上建立 .NET 主控台應用程式，以便更新模組對應項報告的屬性。

## <a name="create-a-visual-studio-project"></a>建立 Visual Studio 專案

在 Visual Studio 中，使用 [主控台應用程式 (.NET Framework)] 專案範本，將 Visual C# Windows 傳統桌面專案新增至現有的解決方案。 確定 .NET Framework 為 4.6.1 或更新版本。 將專案命名為 **UpdateModuleTwinReportedProperties**。

  ![建立 Visual Studio 專案](./media/iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.png)

## <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>安裝最新的 Azure IoT Hub .NET 裝置 SDK

模組身分識別與模組對應項都處於公開預覽階段。 您僅供以 IoT 中樞裝置的發行前版本 Sdk。 在 Visual Studio 中，開啟[工具] > [Nuget 套件管理員] > [管理解決方案的 Nuget 套件]。 搜尋 Microsoft.Azure.Devices.Client。 請確定已核取 [包含發行前版本] 核取方塊。 選取最新版本並安裝。 您現在可以存取所有的模組功能。

  ![安裝 Azure IoT Hub .NET 服務 SDK V1.16.0-preview-005](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

## <a name="get-your-module-connection-string"></a>取得模組連接字串

登入[Azure 入口網站](https://portal.azure.com/)。 瀏覽至您的 IoT 中樞並按一下 IoT 裝置。 尋找 myFirstDevice 並加以開啟，您會看到已成功建立 myFirstModule。 複製模組連接字串。 在下一個步驟中需要用到它。

  ![Azure 入口網站模組詳細資料](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.png)

## <a name="create-updatemoduletwinreportedproperties-console-app"></a>建立 UpdateModuleTwinReportedProperties 主控台應用程式

在 **Program.cs** 檔案開頭處新增下列 `using` 陳述式：

```csharp
using Microsoft.Azure.Devices.Client;
using Microsoft.Azure.Devices.Shared;
```

將下列欄位新增到 **Program** 類別。 使用模組連接字串取代預留位置值。

```csharp
private const string ModuleConnectionString = "<Your module connection string>";
private static ModuleClient Client = null;
```

將下列方法 **OnDesiredPropertyChanged** 新增至 **Program** 類別：

```csharp
private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
    {
        Console.WriteLine("desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
        Console.WriteLine("Sending current time as reported property");
        TwinCollection reportedProperties = new TwinCollection
        {
            ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
        };

        await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
    }
```

最後，將下列幾行新增至 **Main** 方法：

```csharp
static void Main(string[] args)
{
    Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;

    try
    {
        Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
        Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

        Console.WriteLine("Retrieving twin");
        var twinTask = Client.GetTwinAsync();
        twinTask.Wait();
        var twin = twinTask.Result;
        Console.WriteLine(JsonConvert.SerializeObject(twin));

        Console.WriteLine("Sending app start time as reported property");
        TwinCollection reportedProperties = new TwinCollection();
        reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

        Client.UpdateReportedPropertiesAsync(reportedProperties);
    }
    catch (AggregateException ex)
    {
        Console.WriteLine("Error in sample: {0}", ex);
    }

    Console.WriteLine("Waiting for Events.  Press enter to exit...");
    Console.ReadKey();
    Client.CloseAsync().Wait();
}

private static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
{
    Console.WriteLine($"Status {status} changed: {reason}");
}
```

此程式碼範例顯示如何擷取模組對應項，並使用 AMQP 通訊協定更新報告的屬性。 在公開預覽中，我們只支援 AMQP 進行模組對應項作業。

## <a name="run-the-apps"></a>執行應用程式

您現在可以開始執行應用程式。 在 Visual Studio 的 [方案總管] 中以滑鼠右鍵按一下您的方案，然後按一下 [設定啟始專案]。 選取 [多個啟始專案]，然後選取 [啟動] 作為主控台應用程式的動作。 然後按 F5 來啟動執行這兩個應用程式。

## <a name="next-steps"></a>後續步驟

若要繼續開始使用 IoT 中樞並瀏覽其他 IoT 案例，請參閱︰

* [開始使用 IoT 中樞模組身分識別和模組對應項使用.NET 的備份和.NET 裝置](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [開始使用 IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)