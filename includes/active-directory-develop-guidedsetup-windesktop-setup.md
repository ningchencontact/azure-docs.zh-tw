---
title: 包含檔案
description: 包含檔案
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: ae6d590cdada24638ec2d24c83609b8e6addfaf0
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121639"
---
## <a name="set-up-your-project"></a>設定專案

您會在本節中建立新的專案來示範如何整合 Windows Desktop .NET 應用程式 (XAML) 與使用 Microsoft 登入，以便應用程式可以查詢需要權杖的 Web API。

您使用本指南所建立的應用程式會顯示用來呼叫圖表的按鈕、在畫面上顯示結果的區域，以及登出按鈕。

> [!NOTE]
> 想要改為下載此範例的 Visual Studio 專案嗎？ [下載專案](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)並跳至[設定步驟](#register-your-application)，以在執行之前先設定程式碼範例。
>

若要建立應用程式，請執行下列動作：

1. 在 Visual Studio 中，選取 [檔案]  >  [新增]  >  [專案]。
2. 在 [範本] 底下，選取 [Visual C#]。
3. 選取 [WPF 應用程式 (.NET Framework)]，視您所使用的 Visual Studio 版本而定。

## <a name="add-msal-to-your-project"></a>將 MSAL 新增至您的專案

1. 在 Visual Studio 中，選取 [工具]  >  [NuGet 套件管理員] >  [套件管理員主控台]。
2. 在 [套件管理器主控台] 視窗中，貼上下列 Azure PowerShell 命令：

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE] 
    > 此命令會安裝 Microsoft 驗證程式庫。 MSAL 會處理使用者權杖的取得、快取及重新整理作業，而這些權杖用來存取受 Azure Active Directory v2.0 保護的 API。
    >

## <a name="add-the-code-to-initialize-msal"></a>新增程式碼以初始化 MSAL

在這個步驟中，您建立類別來處理和 MSAL 的互動，例如處理權杖。

1. 開啟 App.xaml.cs 檔案，然後將 MSAL 的參考新增至類別：

    ```csharp
    using Microsoft.Identity.Client;
    ```
   <!-- Workaround for Docs conversion bug -->

2. 將應用程式類別更新至下面這樣：

    ```csharp
    public partial class App : Application
    {
        static App()
        {
            _clientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .Build();
        }

        // Below are the clientId (Application Id) of your app registration and the tenant information. 
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - the content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use `organizations`
        //   - for any Work or School accounts, or Microsoft personal account, use `common`
        //   - for Microsoft Personal account, use consumers
        private static string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";

        private static string Tenant = "common";

        private static IPublicClientApplication _clientApp ;

        public static IPublicClientApplication PublicClientApp { get { return _clientApp; } }
    }
    ```

## <a name="create-the-application-ui"></a>建立應用程式 UI

本節會說明應用程式如何查詢受保護的後端伺服器 (例如 Microsoft Graph)。 

系統應會自動建立 MainWindow.xaml 檔案，作為專案範本的一部分。 開啟此檔案，然後將應用程式的 \<Grid> 節點取代為下列程式碼：

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```
