---
title: 設定 Windows 虛擬桌面預覽負載平衡方法-Azure
description: 如何設定 Windows 虛擬桌面環境的負載平衡方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 0c4702dada17e759d89c33be99b3155f4b15ad9e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60328879"
---
# <a name="configure-the-windows-virtual-desktop-preview-load-balancing-method"></a>設定 Windows 虛擬桌面預覽負載平衡方法

設定主應用程式集區的負載平衡方法，可讓您調整 Windows 虛擬桌面預覽環境，以便符合您的需求。

>[!NOTE]
> 這不適用於桌面的持續性主應用程式集區因為使用者永遠都有 1 對 1 對應至工作階段主機的主應用程式集區中。

## <a name="configure-breadth-first-load-balancing"></a>設定廣度優先的負載平衡

廣度優先的負載平衡是新的非持續性的主應用程式集區的預設組態。 廣度優先的負載平衡分散在主應用程式集區中的所有可用的工作階段主機的新使用者工作階段。 當設定廣度優先的負載平衡，您可以設定最大的工作階段限制每個工作階段主機，主應用程式集區中。

首先，[下載並匯入 Windows 虛擬桌面的 PowerShell 模組](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)，以在您的 PowerShell 工作階段中使用 (如果您還沒這麼做的話)。

若要設定的主應用程式集區，來執行廣度優先的負載平衡，而不必調整最大的工作階段限制，請執行下列 PowerShell cmdlet:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

若要設定主應用程式集區執行廣度優先的負載平衡，並使用新的最大工作階段限制，請執行下列 PowerShell cmdlet:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>設定深度優先的負載平衡

深度優先的負載平衡會分散到可用的工作階段主機與連接最大數目的新使用者工作階段，但未達到其最大工作階段限制臨界值。 當設定深度優先的負載平衡，您**必須**設定主應用程式集區中的最大工作階段限制每個工作階段主機。

若要設定主應用程式集區執行深度優先的負載平衡，請執行下列 PowerShell cmdlet:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
