---
title: 設定 Windows Update 設定以使用 Azure 更新管理
description: 本文說明您設定以使用 Azure 更新管理的 Windows Update 設定。
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 813d34f9c07e6c2909c483f040d4f3bf09b3ad24
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "72690846"
---
# <a name="configure-windows-update-settings-for-update-management"></a>設定更新管理的 Windows Update 設定

Azure 更新管理依賴 Windows Update 下載並安裝 Windows 更新。 因此，更新管理會遵循 Windows Update 所使用的許多設定。 如果您使用 [設定] 來啟用非 Windows 更新，更新管理也會管理這些更新。 如果您想要在進行更新部署之前啟用下載更新，則更新部署的速度會更快、更有效率，且較不可能超過維護時段。

## <a name="pre-download-updates"></a>預先下載更新

若要在群組原則中設定自動下載更新，請將[configure 自動更新設定](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates)設為**3**。 此設定可讓您在背景下載必要的更新，但不會進行安裝。 如此一來，更新管理仍然可以控制排程，但可在更新管理維護期間以外下載更新。 此行為可防止更新管理中的「維護視窗超過」錯誤。

您也可以在要設定自動下載更新的系統上執行下列 PowerShell 命令，以開啟這項設定：

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="disable-automatic-installation"></a>停用自動安裝

根據預設，Azure 虛擬機器（Vm）上會啟用自動安裝更新。 這可能會導致在您將更新排程以進行安裝之前，更新管理進行安裝。 您可以藉由將 `NoAutoUpdate` 登錄機碼設定為 `1` 來停用此行為。 下列 PowerShell 程式碼片段顯示如何執行這項操作：

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

## <a name="configure-reboot-settings"></a>設定重新開機設定

藉[由編輯](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)登錄和[用來管理重新開機](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)的登錄機碼，在設定自動更新中所列出的登錄機碼，可能會導致您的電腦重新開機，即使您在**更新部署**設定中指定 [**永不重新開機**]. 您應該將這些登錄機碼設定為最符合您的環境。

## <a name="enable-updates-for-other-microsoft-products"></a>啟用其他 Microsoft 產品的更新

根據預設，Windows Update 只會提供 Windows 的更新。 如果您啟用 [**當我更新 Windows 時提供其他 microsoft 產品的更新**] 設定，您也會收到其他產品的更新，包括 Microsoft SQL Server 和其他 Microsoft 軟體的安全性修補程式。 此選項無法由「群組原則」設定。 在您要啟用其他 Microsoft 更新的系統上執行下列 PowerShell 命令。 更新管理將符合此設定。

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>WSUS 設定

更新管理符合 Windows Server Update Services （WSUS）設定。 以下列出您可以設定來使用更新管理的 WSUS 設定。

### <a name="intranet-microsoft-update-service-location"></a>內部網路 Microsoft 更新服務位置

您可以在 [[指定內部網路 Microsoft Update 服務位置](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location)] 底下，指定掃描和下載更新的來源。

## <a name="next-steps"></a>後續步驟

設定 Windows Update 設定之後，您可以遵循[管理 Azure vm 的更新和修補程式](automation-tutorial-update-management.md)中的指示來排程更新部署。