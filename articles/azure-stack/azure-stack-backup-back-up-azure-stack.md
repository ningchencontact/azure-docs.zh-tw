---
title: "備份 Azure Stack | Microsoft Docs"
description: "在 Azure-Stack 上以就地備份來執行隨選備份。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: df1f4c6fadd08b17a1a1eb8bbe41ab71ae4729fc
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/02/2018
---
# <a name="back-up-azure-stack"></a>備份 Azure Stack

*適用於：Azure Stack 整合系統和 Azure Stack 開發封裝*

在 Azure-Stack 上以就地備份來執行隨選備份。 若您需要啟用基礎結構備份服務，請參閱[從系統管理入口網站啟用 Azure Stack 的備份](azure-stack-backup-enable-backup-console.md)。

## <a name="start-azure-stack-backup"></a>啟動 Azure Stack 備份

使用提升權限提示字元開啟 Windows PowerShell，並執行下列命令：

   ```powershell
   Start-AzSBackup -Location $location
   ```

## <a name="confirm-backup-completed-in-the-administration-portal"></a>在系統管理入口網站中確認已完成的備份

1. 在 [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external) 開啟 Azure Stack 系統管理員入口網站。
2. 選取 [更多服務] > [基礎結構的備份]。 在 [基礎結構備份] 刀鋒視窗中選擇 [設定]。
3. 在 [可用備份] 清單中尋找備份的 [名稱] 和 [完成日期]。
4. 確認**狀態**是**已成功**。

您也可以從系統管理入口網站確認已完成的備份。 巡覽到 `\MASBackup\<datetime>\<backupid>\BackupInfo.xml`

## <a name="next-steps"></a>後續步驟

- 深入了解從資料遺失事件從復原的工作流程。 請參閱[從重大資料遺失的情況下復原](azure-stack-backup-recover-data.md)。