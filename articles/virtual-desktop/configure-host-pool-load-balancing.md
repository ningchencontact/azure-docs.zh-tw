---
title: 設定 Windows 虛擬桌面負載平衡方法 （預覽）-Azure
description: 如何設定 Windows 虛擬桌面環境的負載平衡方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 10a1066b85b16749fe95e373e696d486b0e7bafa
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318340"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>設定 Windows 虛擬桌面負載平衡方法

設定主應用程式集區的負載平衡方法，可讓您調整 Windows 虛擬桌面 （預覽） 環境，以便符合您的需求。

>[!NOTE]
> 這不適用於桌面的持續性主應用程式集區因為使用者永遠都有 1 對 1 對應至工作階段主機的主應用程式集區中。

## <a name="configure-breadth-first-load-balancing"></a>設定廣度優先的負載平衡

廣度優先的負載平衡是新的非持續性的主應用程式集區的預設組態。 廣度優先負載平衡分散在主應用程式集區中的所有可用的工作階段主機的新使用者工作階段。 當設定廣度優先的負載平衡，您可以設定最大的工作階段限制每個工作階段主機，主應用程式集區中。

首先，[下載並匯入的 Windows 虛擬桌面的 PowerShell 模組](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)來使用您的 PowerShell 工作階段中，如果您還沒有這麼做。

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
