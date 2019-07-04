---
title: 主要的 VHD 映像-Azure 上安裝 Office
description: 如何安裝及自訂 Windows 虛擬桌面預覽母片映像至 Azure 上的 Office。
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/02/2019
ms.author: v-chjenk
ms.openlocfilehash: 6d2bb7efdd5567da377e1e15fec4935b7d4a3a6f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444122"
---
# <a name="install-office-on-a-master-vhd-image"></a>在主要 VHD 映像上安裝 Office

這篇文章會告訴您如何上傳至 Azure 的主要虛擬硬碟 (VHD) 映像上安裝 Office 365 ProPlus、 OneDrive 和其他常見的應用程式。 如果您的使用者需要存取特定企業營運 (LOB) 應用程式，我們建議您完成這篇文章中的指示之後安裝。

本文假設您已經建立虛擬機器 (VM)。 如果沒有，請參閱[準備和自訂母片的 VHD 映像](set-up-customize-master-image.md#create-a-vm)

本文也假設您有更高的存取在 VM 上是否已在 Azure 或 HYPER-V 管理員中佈建。 如果沒有，請參閱[提高管理所有的 Azure 訂用帳戶和管理群組的存取權限](https://docs.microsoft.com/azure/role-based-access-control/elevate-access-global-admin)。

>[!NOTE]
>這些指示是針對適用於您的組織現有的程序的 Windows 虛擬桌面預覽專屬組態。

## <a name="install-office-in-shared-computer-activation-mode"></a>將 Office 安裝在共用的電腦啟用模式

共用的電腦啟用可讓您部署到多個使用者存取您組織中電腦的 Office 365 ProPlus。 如需有關共用的電腦啟用的詳細資訊，請參閱[的 Office 365 專業增強版的共用的電腦啟用概觀](https://docs.microsoft.com/DeployOffice/overview-of-shared-computer-activation-for-office-365-proplus)。

使用[Office 部署工具](https://www.microsoft.com/download/details.aspx?id=49117)安裝 Office。 Windows 10 企業版多重工作階段僅支援下列版本的 Office:
- Office 365 ProPlus
- Microsoft 365 商務版訂用帳戶隨附的 office 365 商務版

Office 部署工具需要的組態 XML 檔案。 若要自訂下列的範例，請參閱[Office 部署工具的設定選項](https://docs.microsoft.com/deployoffice/configuration-options-for-the-office-2016-deployment-tool)。

此範例組態中，我們提供的 XML 將會執行下列動作：

- 安裝 Office 的內部通道，並從測試人員通道傳遞更新，它們正在執行時。
- 使用 x64 架構。
- 停用自動更新。
- 安裝 Visio 及專案。
- 移除任何現有的 Office 安裝，並將其設定移轉。
- 啟用共用的電腦啟用。

>[!NOTE]
>在 Visio 中的樣板搜尋功能無法運作中 Windows 虛擬桌面預覽設定期間。

以下是此範例組態 XML 不會執行的動作：

- 安裝商務用 Skype for Business
- 在每個使用者模式下安裝 OneDrive。 若要進一步了解，請參閱[安裝在每台機器模式中的 OneDrive](#install-onedrive-in-per-machine-mode)。

>[!NOTE]
>共用的電腦啟用可以設定透過群組原則物件 (Gpo) 或登錄設定。 GPO 是位於**電腦組態\\原則\\系統管理範本\\Microsoft Office 2016 （電腦）\\授權設定**

Office 部署工具包含 setup.exe。 若要安裝 Office，請在命令列執行下列命令：

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>範例 configuration.xml

下列 XML 範例將會安裝測試人員版本。

```xml
<Configuration>
    <Add OfficeClientEdition="64" SourcePath="https://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f">
        <Product ID="O365ProPlusRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Groove" />
            <ExcludeApp ID="Lync" />
            <ExcludeApp ID="OneDrive" />
            <ExcludeApp ID="Teams" />
        </Product>
        <Product ID="VisioProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" /> 
        </Product>
        <Product ID="ProjectProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" />
        </Product>
    </Add>
    <RemoveMSI All="True" />
    <Updates Enabled="FALSE" UpdatePath="https://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f" />
    <Display Level="None" AcceptEULA="TRUE" />
    <Logging Level="Verbose" Path="%temp%\WVDOfficeInstall" />
    <Property Value="TRUE" Name="FORCEAPPSHUTDOWN"/>
    <Property Value="1" Name="SharedComputerLicensing"/>
    <Property Value="TRUE" Name="PinIconsToTaskbar"/>
</Configuration>
```

>[!NOTE]
>Office 小組建議使用 64 位元安裝，如**OfficeClientEdition**參數。

安裝 Office 之後, 您可以更新預設 Office 行為。 個別或批次檔來更新的行為，請執行下列命令。

```batch
rem Mount the default user registry hive
reg load HKU\TempDefault C:\Users\Default\NTUSER.DAT
rem Must be executed with default registry hive mounted.
reg add HKU\TempDefault\SOFTWARE\Policies\Microsoft\office\16.0\common /v InsiderSlabBehavior /t REG_DWORD /d 2 /f
rem Set Outlook's Cached Exchange Mode behavior
rem Must be executed with default registry hive mounted.
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v enable /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v syncwindowsetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSettingMonths  /t REG_DWORD /d 1 /f
rem Unmount the default user registry hive
reg unload HKU\TempDefault

rem Set the Office Update UI behavior.
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideupdatenotifications /t REG_DWORD /d 1 /f
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideenabledisableupdates /t REG_DWORD /d 1 /f
```

## <a name="install-onedrive-in-per-machine-mode"></a>在每台機器模式中安裝 OneDrive

OneDrive 是通常已安裝的每個使用者。 在此環境中，它應該安裝每台電腦。

以下是如何在每台機器模式中安裝 OneDrive:

1. 首先，建立暫存 OneDrive 安裝程式的位置。 本機磁碟資料夾或 [\\\\unc] (file://unc) 位置是沒問題。

2. 下載 OneDriveSetup.exe 至您的預備位置，此連結： <https://aka.ms/OneDriveWVD-Installer>

3. 如果您已安裝 office 使用 OneDrive 省略 **\<ExcludeApp 識別碼 = 「 OneDrive 」 /\>** ，執行下列命令從提升權限的命令提示字元解除安裝任何現有的 OneDrive 每位使用者安裝命令：
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. 執行此命令從提升權限的命令提示字元來設定**AllUsersInstall**登錄值：

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. 執行此命令，以在每台機器模式中安裝 OneDrive:

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. 執行此命令來設定開始所有使用者的登入 OneDrive:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. 啟用**設定使用者帳戶，以無訊息方式**藉由執行下列命令。

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. 重新導向，並移動 Windows 知道到 OneDrive 的資料夾，執行下列命令。

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>小組及 Skype

Windows 虛擬桌面不支援的商務和小組的 Skype。

## <a name="next-steps"></a>後續步驟

現在，Office 新增到映像之後，您可以繼續自訂主版 VHD 映像。 請參閱[準備和自訂母片的 VHD 映像](set-up-customize-master-image.md)。
