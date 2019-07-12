---
title: 準備和自訂母片的 VHD 映像-Azure
description: 如何準備、 自訂及 Windows 的虛擬桌面的預覽母片映像上傳至 Azure。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: helohr
ms.openlocfilehash: 2413a380adf32755452482d2b68d2055f7db666d
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620427"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>準備和自訂主要 VHD 映像

這篇文章會告訴您如何準備上傳至 Azure，包括如何建立虛擬機器 (Vm)，並在其上安裝軟體的主要虛擬硬碟 (VHD) 映像。 這些指示適用於可搭配您組織現有程序使用的 Windows 虛擬桌面預覽版特定設定。

## <a name="create-a-vm"></a>建立 VM

Windows 10 企業版多重工作階段可由 Azure 映像庫。 有兩個選項可自訂此映像。

第一個選項是佈建虛擬機器 (VM) 在 Azure 中的指示[從受控映像建立 VM](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)，然後直接跳至[軟體準備及安裝](set-up-customize-master-image.md#software-preparation-and-installation)。

第二個選項是在本機建立映像，下載映像、 佈建為 HYPER-V VM，並自訂它以符合您的需求，我們在下一節中討論。

### <a name="local-image-creation"></a>建立本機映像

一旦您已下載的映像至本機位置，開啟**HYPER-V 管理員**與您所複製的 VHD 建立 VM。 下列指示的簡單版本，但您可以找到更詳細的指示，在[建立虛擬機器在 HYPER-V 中](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v)。

若要複製的 VHD 建立 VM:

1. 開啟**新的虛擬機器精靈**。

2. 在 [Specify Generation] 頁面中，選取**第 1 代**。

    ![[Specify Generation] 頁面的螢幕擷取畫面。 會選取 「 第 1 代 」 選項。](media/a41174fd41302a181e46385e1e701975.png)

3. 在檢查點類型 下停用核取方塊取消核取的檢查點。

    ![檢查點 頁面的 檢查點類型 區段的螢幕擷取畫面。](media/20c6dda51d7cafef33251188ae1c0c6a.png)

您也可以執行下列 cmdlet，在 PowerShell 中停用檢查點。

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>固定的磁碟

如果您從現有的 VHD 建立 VM，它會建立預設的動態磁碟。 它可以藉由選取變更成固定磁碟**編輯磁碟...** 如下圖所示。 如需詳細指示，請參閱 <<c0> [ 準備 Windows VHD 或 VHDX 以上傳至 Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image)。

![編輯磁碟 選項的螢幕擷取畫面。](media/35772414b5a0f81f06f54065561d1414.png)

您也可以執行下列 PowerShell cmdlet 來將磁碟變更為固定磁碟。

```powershell
Convert-VHD –Path c:\\test\\MY-VM.vhdx –DestinationPath c:\\test\\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>軟體準備及安裝

本章節涵蓋如何準備及安裝 FSLogix、 Windows Defender 和其他常見的應用程式。 

如果您要在 VM 上安裝 Office 365 ProPlus 和 OneDrive，請參閱[主要的 VHD 映像上安裝的 Office](install-office-on-wvd-master-image.md)。 請遵循該文章，回到這篇文章，並完成主要的 VHD 程序的下一個步驟中的連結。

如果您的使用者需要存取特定的 LOB 應用程式，我們建議您完成本章節的指示進行之後，安裝它們。

### <a name="disable-automatic-updates"></a>停用自動更新

若要停用自動更新，透過本機群組原則：

1. 開啟**本機群組原則編輯器\\系統管理範本\\Windows 元件\\Windows Update**。
2. 以滑鼠右鍵按一下**設定自動更新**並將它設定為**停用**。

您也可以執行下列命令，在命令提示字元，以停用自動更新。

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>適用於 Windows 10 電腦 （選擇性） 指定 [開始] 配置

執行此命令來指定適用於 Windows 10 電腦的 [開始] 配置。

```batch
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-user-profile-container-fslogix"></a>設定使用者設定檔的容器 (FSLogix)

若要納入 FSLogix 容器映像，請依照中的指示[設定主應用程式集區的使用者設定檔共用](create-host-pools-user-profile.md#configure-the-fslogix-profile-container)。 您可以測試的 FSLogix 容器與功能[本快速入門](https://docs.fslogix.com/display/20170529/Profile+Containers+-+Quick+Start)。

### <a name="configure-windows-defender"></a>設定 Windows Defender

如果 Windows Defender 會在 VM 中設定，請確定其設定為不掃描 VHD 和 VHDX 檔案的整個內容期間附件。

這項設定只會移除掃描期間的附件，VHD 和 VHDX 檔案，但不會影響即時掃描。

如需詳細說明如何在 Windows Server 上設定 Windows Defender，請參閱[Windows Server 上的設定 Windows Defender 防毒軟體排除項目](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus)。

若要深入了解如何設定 Windows Defender 從掃描排除特定檔案，請參閱[設定，並驗證檔案的副檔名和資料夾的位置為基礎的排除項目](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus)。

### <a name="configure-session-timeout-policies"></a>設定工作階段逾時原則

可以在 群組原則層級上強制執行遠端工作階段原則，因為主應用程式集區中的所有 Vm 都皆屬於相同的安全性群組。

若要設定遠端工作階段原則：

1. 瀏覽至**系統管理範本** > **Windows 元件** > **的遠端桌面服務** >  **遠端桌面工作階段主機** > **工作階段時間限制**。
2. 在右側窗格中，選取**設定為作用中但閒置的遠端桌面服務工作階段的時間限制**原則。
3. 強制回應視窗出現之後，請變更 [原則] 選項，從**未設定**要**已啟用**啟用原則。
4. 在 [原則] 選項下方下拉式選單中，設定的時間量**4 小時**。

手動設定遠端工作階段原則，您也可以執行下列命令：

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v RemoteAppLogoffTimeLimit /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fResetBroken /t REG_DWORD /d 1 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxConnectionTime /t REG_DWORD /d 10800000 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v RemoteAppLogoffTimeLimit /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxDisconnectionTime /t REG_DWORD /d 5000 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxIdleTime /t REG_DWORD /d 7200000 /f
```

### <a name="set-up-time-zone-redirection"></a>設定時區重新導向

時區重新導向可以強制執行群組原則層級，因為主應用程式集區中的所有 Vm 都皆屬於相同的安全性群組。

若要重新導向的時區：

1. 在 Active Directory 伺服器上，開啟**群組原則管理主控台**。
2. 展開您的網域和群組原則物件。
3. 以滑鼠右鍵按一下**群組原則物件**您建立的群組原則設定，然後選取**編輯**。
4. 在 **群組原則管理編輯器**，瀏覽至**電腦設定** > **原則** > **系統管理範本** > **Windows 元件** > **遠端桌面服務** > **遠端桌面工作階段主機**  > **裝置及資源重新導向**。
5. 啟用**允許時區重新導向**設定。

您也可以在主要映像來重新導向時間區域上執行此命令：

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>停用儲存空間感知器

使用 Windows 10 企業版或 Windows 10 企業版多重工作階段的 Windows 虛擬桌面工作階段主機，建議您停用儲存空間感知器。 您可以在 [設定] 功能表中停用儲存體 Sense**儲存體**，如下列螢幕擷取畫面所示：

![[設定] 下的 [儲存體] 功能表的螢幕擷取畫面。 「 儲存空間感知器 」 選項已關閉。](media/storagesense.png)

您也可以執行下列命令，以變更與登錄設定：

```batch
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>包含其他語言支援

本文不涵蓋如何設定語言和地區的支援。 如需詳細資訊，請參閱下列文章：

- [將語言新增至 Windows 映像](https://docs.microsoft.com/windows-hardware/manufacture/desktop/add-language-packs-to-windows)
- [依需求的功能](https://docs.microsoft.com/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities)
- [語言和地區的功能，隨 (FOD)](https://docs.microsoft.com/windows-hardware/manufacture/desktop/features-on-demand-language-fod)

### <a name="other-applications-and-registry-configuration"></a>其他應用程式和登錄設定

本章節涵蓋應用程式和作業系統組態。 在本節中的所有組態都透過可以由命令列執行的登錄項目和 regedit 工具。

>[!NOTE]
>您可以使用群組原則物件 (Gpo) 或登錄匯入的組態中實作最佳作法。 系統管理員可以選擇其中一個選項，根據其組織的需求。

意見反應中樞收集遙測資料的 Windows 10 企業版多的工作階段上，執行下列命令：

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

執行下列命令，以修正 Watson 當機：

```batch
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

若要修正 5 k 解決支援在登錄編輯器中輸入下列命令。 您可以啟用並排顯示堆疊之前，您必須執行命令。

```batch
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>準備的映像上傳至 Azure

您已完成設定，並已安裝所有應用程式之後，請依照中的指示[準備 Windows VHD 或 VHDX 以上傳至 Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image)準備映像。

之後準備用於映像上傳，請確定 VM 會維持關閉或已取消配置狀態。

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>上傳至 Azure 中的儲存體帳戶的主要映像

在本機建立母片映像時，僅適用於這一節。

下列指示會告訴您如何將您的主要映像上傳至 Azure 儲存體帳戶。 如果您還沒有 Azure 儲存體帳戶，請依照下列中的指示[這篇文章](https://code.visualstudio.com/tutorials/static-website/create-storage)建立一個。

1. 如果您還沒有這麼做，請將 VM 映像 (VHD) 轉換為 已修正。 如果您不要將映像轉換成固定，您無法成功建立映像。

2. 將 VHD 上傳至儲存體帳戶中的 blob 容器。 您可以使用快速上載[儲存體總管工具](https://azure.microsoft.com/features/storage-explorer/)。 若要深入了解儲存體總管工具，請參閱[這篇文章](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)。

    ![Microsoft Azure 儲存體總管工具的 [搜尋] 視窗的螢幕擷取畫面。 選取 [上傳的.vhd 或 vhdx 檔案，做為分頁 blob （建議選項）] 核取方塊。](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. 接下來，移至 Azure 入口網站中您的瀏覽器並搜尋 「 映像。 」 您的搜尋應該會讓您**建立映像**頁面上，如下列螢幕擷取畫面所示：

    ![在 Azure 入口網站中，[建立映像] 頁面的螢幕擷取畫面填滿影像的範例值。](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. 一旦您已建立映像，您應該會看到類似下列螢幕擷取畫面中的通知：

    ![「 已成功建立映像 」 通知的螢幕擷取畫面。](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>後續步驟

有映像之後，您可以建立或更新主應用程式集區。 若要深入了解如何建立和更新主應用程式集區，請參閱下列文章：

- [使用 Azure Resource Manager 範本建立主機集區](create-host-pools-arm-template.md)
- [教學課程：透過 Azure Marketplace 建立主機集區](create-host-pools-azure-marketplace.md)
- [使用 PowerShell 建立主機集區](create-host-pools-powershell.md)
- [設定主應用程式集區的使用者設定檔共用](create-host-pools-user-profile.md)
- [設定 Windows 虛擬桌面負載平衡方法](configure-host-pool-load-balancing.md)
