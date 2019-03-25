---
title: Windows 虛擬桌面預覽主應用程式集區的負載平衡方法-Azure
description: 主應用程式集區的負載平衡方法的 Windows 虛擬桌面預覽環境。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 8b18224339654c067d8ab9b543fa49a9c7d55ddd
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400197"
---
# <a name="host-pool-load-balancing-methods"></a>主應用程式集區的負載平衡方法

Windows 虛擬桌面的預覽支援兩種負載平衡方法。 每個方法會判斷哪一個工作階段主機會裝載連線至主應用程式集區中的資源時的使用者工作階段。

下列的負載平衡方法可用於 Windows 的虛擬桌面：

- 廣度優先的負載平衡可讓您平均分散在主應用程式集區中的工作階段主機中的使用者工作階段。
- 深度優先的負載平衡可讓您與使用者工作階段的工作階段主機 saturate 主應用程式集區中。 當第一個工作階段達到其工作階段限制臨界值時，負載平衡器會指示任何新的使用者連接到下一個工作階段主機主應用程式集區中，直到達到其限制，並依此類推。

每個主應用程式集區只能設定一種類型的負載平衡特定給它。 不過，它們在下列的行為，不論使用哪些裝載集區這兩個負載平衡方法共用：

- 如果使用者已經在主應用程式集區中有工作階段，正在重新連線到該工作階段，負載平衡器將會成功地重新導向至其現有的工作階段的工作階段主機。 即使該工作階段主機的 AllowNewConnections 屬性設定為 False，則會套用此行為。
- 如果使用者還沒有工作階段主應用程式集區中，負載平衡器將不會考慮其 AllowNewConnections 屬性設定為 False，在負載平衡的工作階段主機。

## <a name="breadth-first-load-balancing-method"></a>廣度優先的負載平衡方法

廣度優先負載平衡方法可讓您將針對此案例進行最佳化的使用者連接。 這個方法非常適合想要連接到其集區虛擬桌面環境的使用者提供最佳體驗的組織。

廣度優先方法會先查詢允許新連線的工作階段主機。 方法接著會選取具有最少工作階段主機的工作階段數目。 如果沒有繫結，方法會在查詢中選取第一個工作階段主機。

## <a name="depth-first-load-balancing-method"></a>深度優先負載平衡方法

深度優先負載平衡方法可讓您針對此案例進行最佳化，一次就一個工作階段主機。 這個方法非常適合注重成本的組織想要更細微的控制，它們已配置給主應用程式集區的虛擬機器的數目。

深度優先方法會先查詢允許進行新的連線，且尚未提到超過其最大工作階段限制的工作階段主機。 方法接著會選取的工作階段的最高數目的工作階段主機。 如果沒有繫結，方法會在查詢中選取第一個工作階段主機。