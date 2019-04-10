---
title: 使用基礎結構備份服務進行 Azure Stack 的備份和資料復原 | Microsoft Docs
description: 您可以使用基礎結構備份服務，來備份和還原設定和服務。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: da1ae76925ffeba7a1df57b4121f8cfe20b2887f
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58882101"
---
# <a name="backup-and-data-recovery-for-azure-stack-with-the-infrastructure-backup-service"></a>使用基礎結構備份服務進行 Azure Stack 的備份和資料復原

*適用於：Azure Stack 整合式系統和 Azure Stack 開發套件*

您可以使用基礎結構備份服務，來備份和還原設定和服務。 每個 Azure Stack 安裝都包含了服務的執行個體。 您可以使用為了重新部署 Azure Stack 雲端由服務所建立的備份，來還原身分識別、安全性和 Azure Resource Manager 資料。 

當您準備好實際執行雲端時，就可以啟用備份。 若您計劃要長時間執行測試和驗證，請勿啟用備份。

啟用備份服務前，請確定您[已符合需求](#verify-requirements-for-the-infrastructure-backup-service)。

> [!Note]  
> 基礎結構備份服務不包括使用者資料和應用程式。 如需有關如何保護 IaaS VM 型應用程式的詳細資訊，請參閱[保護部署在 Azure Stack 上的 VM](user/azure-stack-manage-vm-protect.md)。 如需有關如何保護 Azure Stack 上之應用程式的全面了解，請參閱[商務持續性和災害復原的 Azure Stack 考量白皮書](https://aka.ms/azurestackbcdrconsiderationswp) \(英文\)。

## <a name="the-infrastructure-backup-service"></a>基礎結構備份服務

這些服務包含下列功能。

| 功能                                            | 說明                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 備份基礎結構服務                     | 協調 Azure Stack 中整個基礎結構服務子集的備份。 若發生災害，資料還原會是重新部署的其中一環。 |
| 壓縮和加密匯出的備份資料 | 系統會在資料匯出到系統管理員所提供的外部儲存位置前，壓縮和加密備份資料。                |
| 監視備份作業                              | 系統會在備份作業失敗時通知您，並提供補救步驟。                                                                                                |
| 備份管理體驗                       | 備份 RP 可支援啟用備份。                                                                                                                         |
| 雲端復原                                     | 若發生重大資料遺失，備份可用來將核心 Azure Stack 資訊當作部署的一部分進行還原。                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>確認基礎結構備份服務的需求

- **儲存位置**  
  您需要可從 Azure Stack 存取的檔案共用 (其中可包含七個備份)。 每個備份是大約 10 GB。 您的共用應該能夠儲存 140 GB 的備份。 如需選取 Azure Stack 基礎結構備份服務的儲存位置的詳細資訊，請參閱[備份控制器的需求](azure-stack-backup-reference.md#backup-controller-requirements)。
- **認證**  
  您會需要網域使用者帳戶和認證 (例如，您可以使用 Azure Stack 管理員認證)。
- **加密憑證**  
  使用憑證中的公開金鑰加密備份檔案。 請務必將此憑證儲存在安全的位置。 


## <a name="next-steps"></a>後續步驟

了解如何[從系統管理入口網站啟用 Azure Stack 的備份](azure-stack-backup-enable-backup-console.md)。

了解如何[使用 PowerShell 來啟用 Azure Stack 的備份](azure-stack-backup-enable-backup-powershell.md)。

了解如何[備份 Azure Stack](azure-stack-backup-back-up-azure-stack.md )。

了解如何[從重大資料遺失的情況下復原](azure-stack-backup-recover-data.md)。
