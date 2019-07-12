---
title: 自訂 Windows 虛擬桌面使用者-Azure 摘要
description: 如何自訂摘要，可使用 PowerShell cmdlet 的 Windows 虛擬桌面使用者。
services: virtual-desktop
author: v-hevem
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: v-hevem
ms.openlocfilehash: 5fe2a8b8ee5870ff7986ca2d91739f82a5128882
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618962"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>適用於 Windows 虛擬桌面使用者的自訂摘要

因此 RemoteApp 和桌面的遠端資源會出現在可辨識的方式，讓您的使用者，您可以自訂摘要。

首先，[下載並匯入 Windows 虛擬桌面的 PowerShell 模組](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)，以在您的 PowerShell 工作階段中使用 (如果您還沒這麼做的話)。

## <a name="customize-the-display-name-for-a-remoteapp"></a>自訂 RemoteApp 的顯示名稱

您可以變更已發佈的 RemoteApp 的顯示名稱所設定的易記名稱。 根據預設，易記的名稱會是與 RemoteApp 程式的名稱相同。

若要擷取的已發佈的 Remoteapp 應用程式群組清單，請執行下列 PowerShell cmdlet:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![使用名稱和 FriendlyName 反白顯示的 PowerShell cmdlet 取得 RDSRemoteApp 螢幕擷取畫面。](media/get-rdsremoteapp.png)

若要將易記名稱指派至 RemoteApp，請執行下列 PowerShell cmdlet:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![使用名稱和新的 FriendlyName 反白顯示的 PowerShell cmdlet 設定 RDSRemoteApp 螢幕擷取畫面。](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>自訂遠端桌面的顯示名稱

您可以變更已發佈的遠端桌面的顯示名稱所設定的易記名稱。 如果您以手動方式建立主應用程式集區和透過 PowerShell 的傳統型應用程式群組，預設的易記名稱是 「 工作階段桌面 」。 如果集區和傳統型應用程式群組，透過 GitHub 的 Azure Resource Manager 範本或 Azure Marketplace 供應項目，您會建立主應用程式，預設的易記名稱會是主應用程式集區名稱相同。

若要擷取遠端桌面資源，請執行下列 PowerShell cmdlet:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![使用名稱和 FriendlyName 反白顯示的 PowerShell cmdlet 取得 RDSRemoteApp 螢幕擷取畫面。](media/get-rdsremotedesktop.png)

若要將易記名稱指派至遠端桌面資源，請執行下列 PowerShell cmdlet:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![使用名稱和新的 FriendlyName 反白顯示的 PowerShell cmdlet 設定 RDSRemoteApp 螢幕擷取畫面。](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>後續步驟

既然您已自訂的使用者摘要，您可以登入 Windows 虛擬桌面用戶端加以測試。若要這樣做，請繼續 連接到 Windows 虛擬桌面操作說明：
    
 * [從 Windows 10 或 Windows 7 連線](connect-windows-7-and-10.md)
 * [從網頁瀏覽器連線](connect-web.md) 
