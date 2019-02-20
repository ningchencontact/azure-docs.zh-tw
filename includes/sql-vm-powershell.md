---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: 3dc799ecc75589279c8d1c73062a8f2157761330
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2019
ms.locfileid: "56212960"
---
## <a name="start-your-powershell-session"></a>啟動 PowerShell 工作階段
 

執行 [**Connect-AzAccount**](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount) \(英文\) Cmdlet，而您將會看到要輸入認證的登入畫面。 使用您用來登入 Azure 入口網站的相同認證。

```powershell
Connect-AzAccount
```

如果您有多個訂用帳戶，請使用 [**Set-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) \(英文\) Cmdlet，來選取您的 PowerShell 工作階段應使用的訂用帳戶。 若要查看目前的 PowerShell 工作階段正在使用哪個訂用帳戶，請執行 [**Get-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/get-azcontext) \(英文\)。 若要查看您的所有訂用帳戶，請執行 [**Get-AzSubscription**](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription) \(英文\)。

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```

