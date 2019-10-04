---
title: 還原已刪除 App Service 應用程式-Azure App Service
description: 瞭解如何使用 PowerShell 還原已刪除的 App Service 應用程式。
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.service: app-service
ms.openlocfilehash: 7b3a21f3cfee806dc94353e0bc6c11e88641ea34
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827529"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>使用 PowerShell 還原已刪除 App Service 應用程式

如果您不小心在 Azure App Service 中刪除您的應用程式，您可以使用[Az PowerShell 模組](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0)中的命令來還原它。

## <a name="list-deleted-apps"></a>列出刪除的應用程式

若要取得已刪除之應用程式的集合， `Get-AzDeletedWebApp`您可以使用。

如需您可以使用之特定已刪除應用程式的詳細資訊：

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app>
```

詳細資訊包括：

- **DeletedSiteId**：應用程式的唯一識別碼，用於已刪除多個同名應用程式的案例
- **SubscriptionID**：包含已刪除資源的訂用帳戶
- **位置**：原始應用程式的位置
- **ResourceGroupName**：原始資源群組的名稱
- **名稱**：原始應用程式的名稱。
- 位置 **：位置**的名稱。
- **刪除時間**：應用程式何時刪除  

## <a name="restore-deleted-app"></a>還原已刪除的應用程式

一旦識別出您想要還原的應用程式之後，您就可以使用`Restore-AzDeletedWebApp`來還原它。

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

命令的輸入如下：

- **資源群組**：將還原應用程式的目標資源群組
- **名稱**：應用程式的名稱必須是全域唯一的。
- **TargetAppServicePlanName**：連結至應用程式的 App Service 方案

根據預設`Restore-AzDeletedWebApp` ，會同時還原您的應用程式設定和內容。 如果您只想要還原內容，您可以使用`-RestoreContentOnly`旗標搭配此 commandlet。

您可以在這裡找到完整的 commandlet 參考：[Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp)。
