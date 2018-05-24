---
title: 備份 Azure Stack | Microsoft Docs
description: 在 Azure Stack 上以就地備份來執行隨選備份。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/08/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: c2a6727692a7a74b3e5fe32de8800722a9ed91b5
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075182"
---
# <a name="back-up-azure-stack"></a>備份 Azure Stack

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*

在 Azure Stack 上以就地備份來執行隨選備份。 若您需要啟用基礎結構備份服務，請參閱[從系統管理入口網站啟用 Azure Stack 的備份](azure-stack-backup-enable-backup-console.md)。

> [!Note]  
>  如需設定 PowerShell 環境的指示，請參閱[安裝 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。

## <a name="start-azure-stack-backup"></a>啟動 Azure Stack 備份

在運算子管理環境中，使用提升權限提示字元開啟 Windows PowerShell，並執行下列命令：

```powershell
    Start-AzSBackup -Location $location.Name
```

## <a name="confirm-backup-completed-in-the-administration-portal"></a>在系統管理入口網站中確認已完成的備份

1. 開啟位於 [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external) 的 Azure Stack 系統管理入口網站。
2. 選取 [更多服務] > [基礎結構的備份]。 在 [基礎結構備份] 刀鋒視窗中選擇 [設定]。
3. 在 [可用備份] 清單中尋找備份的 [名稱] 和 [完成日期]。
4. 確認**狀態**是**已成功**。

<!-- You can also confirm the backup completed from the administration portal. Navigate to `\MASBackup\<datetime>\<backupid>\BackupInfo.xml`

In ‘Confirm backup completed’ section, the path at the end doesn’t make sense (ie relative to what, datetime format, etc?)
\MASBackup\<datetime>\<backupid>\BackupInfo.xml -->


## <a name="next-steps"></a>後續步驟

- 深入了解從資料遺失事件從復原的工作流程。 請參閱[從重大資料遺失的情況下復原](azure-stack-backup-recover-data.md)。
