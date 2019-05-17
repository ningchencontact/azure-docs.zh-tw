---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6882c46ec0e4925c42de86c87225e9509c84df84
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "65757906"
---
## <a name="set-up-your-development-environment"></a>設定開發環境
接下來，在 Visual Studio 中設定您的開發環境，以便您開始嘗試本指南中的程式碼範例。

### <a name="create-a-windows-console-application-project"></a>创建 Windows 控制台应用程序项目
在 Visual Studio 中，建立新的 Windows 主控台應用程式。 下列步驟示範如何在 Visual Studio 2017 中建立主控台應用程式。 這些步驟類似其他 Visual Studio 版本中的步驟。

1. 選取 [檔案] > [新增] > [專案]。
2. 選取 [已安裝] > [範本] > [Visual C#] > [Windows 傳統桌面]。
3. 選取 **主控台應用程式 (.NET Framework)**。
4. 在 [名稱] 欄位中，輸入應用程式的名稱。
5. 選取 [確定] 。

![Visual Studio 中新增專案對話方塊的螢幕擷取畫面](./media/storage-development-environment-include/storage-development-environment-include-1.png)

本教學課程中的所有程式碼範例均可新增至您主控台應用程式的 `Program.cs` 檔案中的 `Main()` 方法。

您可以在任何類型的 .NET 應用程式 (包括 Azure 雲端服務或 Web 應用程式和桌面與行動應用程式) 中使用 Azure Storage Client Library。 在本指南中，為求簡化，我們會使用主控台應用程式。

### <a name="use-nuget-to-install-the-required-packages"></a>使用 NuGet 安装所需包
您必須在您的專案中參考下列兩個套件，才能完成本教學課程︰

* [適用於 .NET 的 Microsoft Azure 儲存體用戶端程式庫](https://www.nuget.org/packages/WindowsAzure.Storage/)：此套件可讓您以程式設計方式存取儲存體帳戶中的資料資源。
* [適用於 .NET 的 Microsoft Azure Configuration Manager 程式庫](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)：此套件提供一個類別，無論您的應用程式於何處執行，均可用來剖析組態檔中的連接字串。

您可以使用 NuGet 來取得這兩個封裝。 請遵循下列步驟：

1. 在 [方案總管] 中以滑鼠右鍵按一下專案，然後選擇 [管理 NuGet 套件]。
2. 在線上搜尋 "WindowsAzure.Storage"，然後選取 [安裝] 以安裝 Storage Client Library 與其相依項目。
3. 在線上搜尋 "WindowsAzure.ConfigurationManager"，然後選取 [安裝] 以安裝 Azure Configuration Manager。

> [!NOTE]
> Storage Client Library 封裝也包含在 [適用於 .NET 的 Azure SDK](https://azure.microsoft.com/downloads/)中。 但是我们建议同时从 NuGet 安装存储客户端库，以确保始终使用客户端库的最新版本。
> 
> Storage Client Library for .NET 中的 ODataLib 相依性現由 ODataLib 套件解決，該套件是在 NuGet 上而非從 WCF 資料服務提供。 您可以直接下載 ODataLib 程式庫，或是由您的程式碼專案透過 NuGet 參照這些程式庫。 Storage Client Library 使用的特定 ODataLib 封裝有 [OData](http://nuget.org/packages/Microsoft.Data.OData/)、[Edm](http://nuget.org/packages/Microsoft.Data.Edm/)，以及 [Spatial](http://nuget.org/packages/System.Spatial/)。 這些程式庫雖由 Azure 資料表儲存體類別使用，它們同時也是使用 Storage Client Library 進行程式設計的必要相依項目。
> 
> 

### <a name="determine-your-target-environment"></a>決定您的目標環境
有兩個環境選項可供您執行本指南中的範例︰

* 可针对云中的 Azure 存储帐户运行代码。 
* 可针对 Azure 存储模拟器运行代码。 儲存體模擬器是模擬雲端中 Azure 儲存體帳戶的本機環境。 模擬器是一個免費選項，在開發您的應用程式時可用於測試和偵錯您的程式碼。 模擬器會使用已知的帳戶和金鑰。 如需詳細資訊，請參閱[使用 Azure 儲存體模擬器進行開發和測試](../articles/storage/common/storage-use-emulator.md)。

如果您要選擇以雲端中的儲存體帳戶為目標，請從 Azure 入口網站複製您的儲存體帳戶的主要存取金鑰。 如需詳細資訊，請參閱[存取金鑰](../articles/storage/common/storage-account-manage.md#access-keys)。

> [!NOTE]
> 您可以選擇以儲存體模擬器為目標，以避免產生與 Azure 儲存體相關聯的任何費用。 不過，如果您選擇以雲端中的 Azure 儲存體帳戶為目標，則執行本教學課程的費用可以忽略不計。
> 
> 

### <a name="configure-your-storage-connection-string"></a>設定儲存體連接字串
用于 .NET 的 Azure 存储客户端库支持使用存储连接字符串来配置终结点和用于访问存储服务的凭据。 在組態檔中維護儲存體連接字串是最佳方式。 

如需有關連接字串的詳細資訊，請參閱[設定 Azure 儲存體的連接字串](../articles/storage/common/storage-configure-connection-string.md)。

> [!NOTE]
> 儲存體帳戶金鑰很類似儲存體帳戶的根密碼。 請務必小心保護您的儲存體帳戶金鑰。 請避免轉發給其他使用者、進行硬式編碼，或將它儲存在其他人可以存取的純文字檔案。 如果您認為金鑰可能遭到破解，請使用 Azure 入口網站重新產生金鑰。
> 
> 

若要設定連接字串，請從 Visual Studio 中的 [方案總管] 開啟 `app.config` 檔案。 如下所示，加入 `<appSettings>` 元素的內容。 将 `account-name` 替换为存储帐户名称，将 `account-key` 替换为存储帐户密钥：

```xml
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

例如，組態設定會如下顯示：

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==" />
```

若要以存储模拟器为目标，可使用映射到已知帐户名称和密钥的快捷方式。 在此情況下，您的連接字串設定會是︰

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

