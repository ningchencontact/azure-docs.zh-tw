---
title: Azure 安全性控制-資料復原
description: 安全性控制資料復原
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 1cd3e39fd504b5095a83192a4a6314c71d3ca980
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564292"
---
# <a name="security-control-data-recovery"></a>安全性控制：資料復原

確保所有系統資料、設定和秘密都會定期自動備份。

## <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保週期性自動備份

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 9.1 | 10.1 | 客戶 |

啟用 Azure 備份並設定備份來源（Azure Vm、SQL Server 或檔案共用），以及所需的頻率和保留期限。

如何啟用 Azure 備份： https://docs.microsoft.com/azure/backup/

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份，並備份任何客戶管理的金鑰

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 9.2 | 10.2 | 客戶 |

啟用 Azure 備份和目標 VM，以及所需的頻率和保留週期。 在 Azure Key Vault 內備份客戶管理的金鑰。

如何啟用 Azure 備份： https://docs.microsoft.com/azure/backup/

如何在 Azure 中備份金鑰保存庫金鑰： https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 9.3 | 10.3 | 客戶 |

確保能夠在 Azure 備份內定期執行內容的資料還原。 如有必要，請測試還原至隔離的 VLAN。 已備份客戶管理金鑰的測試還原。

如何從 Azure 虛擬機器備份復原檔案：

https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm

如何在 Azure 中還原金鑰保存庫金鑰：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理金鑰的保護

| Azure 識別碼 | CIS 識別碼 | 責任 |
|--|--|--|
| 9.4 | 10.4 | 客戶 |

針對內部部署備份，會使用您在備份至 Azure 時所提供的複雜密碼來提供靜態加密。 針對 Azure VM，會使用「儲存體服務加密」(SSE) 對資料進行靜態加密。 您可以在 Key Vault 中啟用虛刪除，以防止金鑰遭到意外或惡意刪除。

如何在 Key Vault 中啟用虛刪除：

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

## <a name="next-steps"></a>後續步驟

請參閱下一個安全性控制：[事件回應](security-control-incident-response.md)
