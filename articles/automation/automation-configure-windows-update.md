---
title: 設定 Windows Update 設定以使用 Azure 更新管理
description: 本文說明您設定要使用的 Windows Update 設定更新管理
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f50ca9515f12e8c9b5943904c4d0226f2ca3353c
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377560"
---
# <a name="configure-windows-update-settings-for-update-management"></a>設定更新管理的 Windows Update 設定

「更新管理」會倚賴 Windows Update 來下載及安裝 Windows Updates。 因此，我們會採用 Windows Update 所使用的許多設定。 如果您使用設定來啟用非 Windows 更新，「更新管理」也會管理這些更新。 如果您想要啟用在進行更新部署之前先下載更新的功能，則更新部署將可執行得更快，且更不容易超出維護時段。

## <a name="pre-download-updates"></a>預先下載更新

若要在「群組原則」中設定自動下載更新，您可以將[設定自動更新](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates)設定設為 **3**。 這會在背景中下載所需的更新，但不會進行安裝。 這會讓「更新管理」保有排程的控制權，但允許在「更新管理」維護時段外下載更新。 這可防止發生「更新管理」中的「已超過維護時段」錯誤。

您也可以使用 PowerShell 來進行這項設定，只要在您想要自動下載更新的系統上執行下列 PowerShell 即可。

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="disable-automatic-installation"></a>停用自動安裝

Azure Vm 預設會啟用自動安裝更新。 這可能會在您將更新排程為更新管理安裝之前，先進行安裝。 您可以藉由將 `NoAutoUpdate` 登錄機碼設定為 `1` 來停用此行為。 下列 PowerShell 程式碼片段顯示執行此動作的其中一種方式。

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

## <a name="configure-reboot-settings"></a>設定重新開機設定

[設定自動更新] 底下所列的登錄機碼，藉[由編輯](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-rej7uijui7jgistry)登錄和[用來管理重新開機](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)的登錄機碼，可能會導致機器重新開機，即使您已在更新部署設定中指定 [**永不重新開機**]. 您應該視需要為您的環境設定這些登錄機碼。

## <a name="enable-updates-for-other-microsoft-products"></a>啟用其他 Microsoft 產品的更新

Windows Update 預設只會為 Windows 提供更新。 如果您啟用 [**當我更新 Windows 時提供其他 Microsoft 產品的更新**]，系統就會提供其他產品的更新，包括 SQL Server 或其他第一方軟體的安全性修補程式。 此選項無法由「群組原則」設定。 請在您想要啟用其他第一方修補程式的系統上執行下列 PowerShell，「更新管理」將會採用此設定。

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>WSUS 設定

**更新管理**會遵循 WSUS 設定。 您可以設定來使用更新管理的 WSUS 設定清單如下所示。

### <a name="intranet-microsoft-update-service-location"></a>內部網路 Microsoft 更新服務位置

您可以在 [[內部網路 Microsoft Update 服務位置](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location)] 下指定掃描和下載更新的來源。

## <a name="next-steps"></a>後續步驟

設定 Windows Update 設定之後，您可以遵循[管理 Azure vm 的更新和修補程式](automation-tutorial-update-management.md)底下的指示來排程更新部署