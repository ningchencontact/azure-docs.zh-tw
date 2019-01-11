---
title: Azure Stack 中的待用資料加密
description: 了解 Azure Stack 如何藉由待用加密來保護您的資料
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 12/06/2018
ms.author: patricka
ms.reviewer: fiseraci
keywords: ''
ms.openlocfilehash: f9e76b255647f62b273fef8336ed845e365261cf
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2018
ms.locfileid: "53728496"
---
# <a name="data-at-rest-encryption-in-azure-stack"></a>Azure Stack 中的待用資料加密

Azure Stack 會使用待用加密，來保護儲存子系統層級的使用者和基礎結構資料。 Azure Stack 的儲存子系統加密方式是使用 BitLocker 搭配 128 位元 AES 加密。 BitLocker 金鑰會保存在內部祕密存放區中。

待用資料加密是許多主要合規性標準 (例如 PCI-DSS、FedRAMP、HIPAA) 的通用需求。 Azure Stack 可讓您無須進行額外的工作或設定，即可符合這些需求。 如需有關 Azure Stack 如何協助您符合合規性需求的詳細資訊，請參閱 [Microsoft 服務信任入口網站](https://aka.ms/AzureStackCompliance)。

> [!NOTE]
> 待用資料加密可防止實際竊取一或多個硬碟的人存取您的資料。 待用資料加密無法在系統已啟動且正在執行的情況下，防護正透過網路攔截的資料 (傳輸中的資料)、目前使用中的資料 (記憶體中的資料)，或更廣泛來說，正在外洩的資料。

## <a name="retrieving-bitlocker-recovery-keys"></a>擷取 BitLocker 復原金鑰

Azure Stack BitLocker 的待用資料金鑰是內部受控金鑰。 針對一般作業或在進行系統啟動期間，您都無須提供這些金鑰。 不過，支援案例可能會要求提供 BitLocker 復原金鑰以讓系統上線。  

> [!WARNING]
> 請擷取您的 BitLocker 復原金鑰，並將其儲存在 Azure Stack 外部的安全位置中。 在某些支援案例中，如果未提供復原金鑰，可能會導致資料遺失，而必須從備份映像還原系統。

若要擷取 BitLocker 復原金鑰，必須存取[具特殊權限的端點](azure-stack-privileged-endpoint.md) (PEP)。 請從 PEP 工作階段，執行 Get-AzsRecoveryKeys Cmdlet。

```powershell
##This cmdlet retrieves the recovery keys for all the volumes that are encrypted with BitLocker.
Get-AzsRecoveryKeys
```

*Get-AzsRecoveryKeys* Cmdlet 的選擇性參數：

| 參數 | 說明 | 類型 | 必要 |
|---------|---------|---------|---------|
|*raw* | 會針對每個加密磁碟區之復原金鑰、電腦名稱及密碼識別碼之間的對應，傳回未經處理資料  | 參數 | 否 (專為支援案例設計)|


## <a name="troubleshoot-issues"></a>針對問題進行疑難排解

在極端的情況下，BitLocker 解除鎖定要求可能發生失敗，而導致特定磁碟區無法啟動。 如果您沒有 BitLocker 復原金鑰，視某些架構元件的可用性而定，這個失敗可能會導致停機及可能讓資料遺失。

> [!WARNING]
> 請擷取您的 BitLocker 復原金鑰，並將其儲存在 Azure Stack 外部的安全位置中。 在某些支援案例中，如果未提供復原金鑰，可能會導致資料遺失，而必須從備份映像還原系統。

如果您懷疑系統發生 BitLocker 問題 (例如 Azure Stack 無法啟動)，請與支援人員連絡。 支援人員將需要您的 BitLocker 復原金鑰。 大多數 BitLocker 相關問題都可藉由該特定 VM/主機/磁碟區的 FRU 作業來解決。 針對其他情況，可以執行使用 BitLocker 復原金鑰的手動解除鎖定程序。 如果無法取得 BitLocker 復原金鑰，則唯一的選項是從備份映像進行還原。 視上次執行備份的時間而定，您可能會遺失資料。

## <a name="next-steps"></a>後續步驟

- [深入了解 Azure Stack 安全性](azure-stack-security-foundations.md)
- 如需有關 BitLocker 如何保護 CSV 的詳細資訊，請參閱[使用 BitLocker 來保護叢集共用磁碟區和存放區域網路](https://docs.microsoft.com/windows/security/information-protection/bitlocker/protecting-cluster-shared-volumes-and-storage-area-networks-with-bitlocker) \(英文\)。