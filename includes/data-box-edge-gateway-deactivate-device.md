---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: aebb82690a7a49aba071ed64349d37d516208cca
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "67125620"
---
若要重設您的裝置，您需要安全地清除的資料磁碟和您的裝置的開機磁碟上的所有資料。 

使用`Reset-HcsAppliance`cmdlet 來清除資料磁碟和開機磁碟或資料磁碟。 `ClearData`和`BootDisk`參數可讓您分別抹除資料磁碟，而且開機磁碟。

如果您使用本機 web UI 中重設裝置時，會安全地抹除資料磁碟，但開機磁碟會保持不變。 開機磁片包含裝置的設定。

1. [連線到 PowerShell 介面](#connect-to-the-powershell-interface)。
2. 在命令提示字元中，輸入：

    `Reset-HcsAppliance -ClearData -BootDisk`

    下列範例示範如何使用這個指令程式：
    ```
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk
    
    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
