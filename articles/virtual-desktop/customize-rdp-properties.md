---
title: 使用 PowerShell 自訂 RDP 屬性-Azure
description: 如何使用 PowerShell Cmdlet 自訂 Windows 虛擬桌面的 RDP 屬性。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: fbc24705288d9bb4acf0d1b2d99dfbda32149614
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163564"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>自訂主機集區的遠端桌面通訊協定屬性

自訂主機集區的遠端桌面通訊協定 (RDP) 內容, 例如多監視器體驗和音訊重新導向, 可讓您根據自己的需求為使用者提供最佳的體驗。 您可以使用**RdsHostPool** Cmdlet 中的 **-CustomRdpProperty**參數, 在 WINDOWS 虛擬桌面中自訂 RDP 屬性。

如需支援的屬性及其預設值的完整清單, 請參閱[遠端桌面 RDP 檔案設定](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files)。

首先，[下載並匯入 Windows 虛擬桌面的 PowerShell 模組](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)，以在您的 PowerShell 工作階段中使用 (如果您還沒這麼做的話)。 之後, 請執行下列 Cmdlet 來登入您的帳戶:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="add-or-edit-a-single-custom-rdp-property"></a>新增或編輯單一自訂 RDP 屬性

若要新增或編輯單一自訂 RDP 屬性, 請執行下列 PowerShell Cmdlet:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```
![已反白顯示 [名稱] 和 [FriendlyName] 的 PowerShell Cmdlet RDSRemoteApp 螢幕擷取畫面。](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>新增或編輯多個自訂 RDP 屬性

若要新增或編輯多個自訂 RDP 屬性, 請將自訂 RDP 屬性提供為以分號分隔的字串, 以執行下列 PowerShell Cmdlet:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```
![已反白顯示 [名稱] 和 [FriendlyName] 的 PowerShell Cmdlet RDSRemoteApp 螢幕擷取畫面。](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>重設所有自訂 RDP 屬性

您可以遵循[新增或編輯單一自訂 rdp](#add-or-edit-a-single-custom-rdp-property)內容中的指示, 將個別的自訂 rdp 屬性重設為預設值, 或者, 您可以執行下列 PowerShell Cmdlet 來重設主機集區的所有自訂 rdp 屬性:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```
![已反白顯示 [名稱] 和 [FriendlyName] 的 PowerShell Cmdlet RDSRemoteApp 螢幕擷取畫面。](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>後續步驟

既然您已自訂指定主機集區的 RDP 內容, 您可以登入 Windows 虛擬桌面用戶端, 在使用者會話中測試這些屬性。 若要這麼做, 請繼續進行連線至 Windows 虛擬桌面的作法:

- [從 Windows 10 和 Windows 7 連接](connect-windows-7-and-10.md)
- [從網頁瀏覽器連線](connect-web.md)
