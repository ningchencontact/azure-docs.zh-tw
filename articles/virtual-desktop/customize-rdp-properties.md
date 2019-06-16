---
title: 自訂 RDP 屬性與 PowerShell-Azure
description: 如何使用 PowerShell cmdlet 自訂 RDP 屬性的 Windows 虛擬桌面。
services: virtual-desktop
author: v-hevem
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 06/03/2019
ms.author: v-hevem
ms.openlocfilehash: 21d0b45b974f4bc806b26423b7deaef96e4bf350
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082647"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>自訂主應用程式集區的遠端桌面通訊協定屬性

自訂主應用程式集區的遠端桌面通訊協定 (RDP) 的屬性，例如多重監視器的使用經驗和音訊重新導向，可讓您提供您的使用者根據其需求的最佳體驗。 您可以自訂 RDP 屬性中使用 Windows 的虛擬桌上型電腦 **-CustomRdpProperty**中的參數**組 RdsHostPool** cmdlet。

請參閱[遠端桌面 RDP 檔案設定](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files)支援的屬性及其預設值的完整清單。

首先，[下載並匯入 Windows 虛擬桌面的 PowerShell 模組](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)，以在您的 PowerShell 工作階段中使用 (如果您還沒這麼做的話)。

## <a name="add-or-edit-a-single-custom-rdp-property"></a>新增或編輯單一的自訂 RDP 屬性

若要新增或編輯單一的自訂 RDP 屬性，請執行下列 PowerShell cmdlet:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```
![使用名稱和 FriendlyName 反白顯示的 PowerShell cmdlet 取得 RDSRemoteApp 螢幕擷取畫面。](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>新增或編輯多個自訂 RDP 屬性

若要新增或編輯多個自訂 RDP 屬性，執行下列 PowerShell cmdlet 以分號分隔的字串形式提供自訂 RDP 屬性：

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```
![使用名稱和 FriendlyName 反白顯示的 PowerShell cmdlet 取得 RDSRemoteApp 螢幕擷取畫面。](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>重設所有的自訂 RDP 屬性

您可以重設個別的自訂 RDP 屬性設為預設值中的指示[新增或編輯單一的自訂 RDP 屬性](#add-or-edit-a-single-custom-rdp-property)，或您可以執行下列命令來重設所有的自訂 RDP 屬性，主應用程式集區PowerShell cmdlet:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```
![使用名稱和 FriendlyName 反白顯示的 PowerShell cmdlet 取得 RDSRemoteApp 螢幕擷取畫面。](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>後續步驟

既然您已自訂 RDP 屬性，指定主應用程式集區，您可以登入 Windows 虛擬桌面用戶端來測試這些使用者工作階段的一部分。 若要這樣做，請繼續 連接到 Windows 虛擬桌面操作說明：

- [從 Windows 10 和 Windows 7 連線](connect-windows-7-and-10.md)
- [從網頁瀏覽器連線](connect-web.md)
