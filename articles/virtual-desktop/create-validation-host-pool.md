---
title: 建立 Windows 虛擬桌面預覽主機集區來驗證服務更新 - Azure
description: 將更新推展到生產環境之前，如何建立驗證主機集區來監視服務更新。
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 05/08/2019
ms.author: v-chjenk
ms.openlocfilehash: c9b2a593a6943fe2e9577acc61b1d5a7bcd98607
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070652"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>教學課程：建立主機集區以驗證服務更新

主機集區是 Windows 虛擬桌面預覽版租用戶環境中一或多個相同虛擬機器的集合。 將主機集區部署到生產環境之前，強烈建議您建立驗證主機集區。 更新會先套用到驗證主機集區，讓您先監視服務更新，再將其推展到生產環境。 若沒有驗證主機集區，您可能無法發現引入錯誤的變更，這可能會導致您生產環境中的使用者停機。

為了確保您的應用程式可使用最新更新，驗證主機集區應盡可能類似於您生產環境中的主機集區。 使用者應該經常連線到驗證主機集區，就如同連線到生產主機集區一樣。 如果您已在主機集區上進行自動化測試，則應該在驗證主機集區上包含自動化測試。

您可以使用[診斷功能](diagnostics-role-service.md)或 [Windows 虛擬桌面疑難排解文章](https://docs.microsoft.com/Azure/virtual-desktop/troubleshoot-set-up-overview)，在驗證主機集區中進行問題偵錯。

>[!NOTE]
> 建議您將驗證主機集區保持原狀，以測試所有未來的更新。

開始之前，[下載並匯入 Windows 虛擬桌面 PowerShell 模組](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) (如果您還沒這麼做的話)。

## <a name="create-your-host-pool"></a>建立您的主機集區

您可以依照下列文章中的指示來建立主機集區：
- [教學課程：透過 Azure Marketplace 建立主機集區](create-host-pools-azure-marketplace.md)
- [使用 Azure Resource Manager 範本建立主機集區](create-host-pools-arm-template.md)
- [使用 PowerShell 建立主機集區](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>定義您的主機集區作為驗證主機集區

執行下列 PowerShell Cmdlet 來定義新的主機集區作為驗證主機集區。 以您工作階段相關的值取代引號中的值：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

執行下列 PowerShell Cmdlet 來確認已設定驗證屬性。 以您工作階段相關的值取代引號中的值。

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Cmdlet 的結果應該類似以下輸出：

```
    TenantName          : contoso 
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False 
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>更新排程

在預覽版中，服務更新大約每個月發生一次。 如有重大問題，則會更頻繁地提供重大更新。

## <a name="next-steps"></a>後續步驟

既然您已建立驗證主機集區，您可了解如何部署並連線管理工具，以便管理 Microsoft 虛擬桌面資源。

> [!div class="nextstepaction"]
> [部署管理工具教學課程](./manage-resources-using-ui.md)
