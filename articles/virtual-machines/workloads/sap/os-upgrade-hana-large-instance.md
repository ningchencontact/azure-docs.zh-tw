---
title: 在 Azure 上執行 SAP Hana (大型執行個體) 的作業系統升級 | Microsoft Docs
description: 在 Azure 上執行 SAP Hana on Azure (大型執行個體) 的作業系統升級
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aa88e45f2523dd65c4f714bfeab1c0eda401d720
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869159"
---
# <a name="operating-system-upgrade"></a>作業系統升級
本文件會詳述 Hana 大型執行個體作業系統升級的詳細資訊。

>[!NOTE]
>作業系統升級是客戶的責任，Microsoft 作業支援可以指引您在升級期間應特別注意的關鍵區域。 規劃升級之前，請先諮詢您的作業系統廠商。

在第一次布建的過程中, Microsoft 營運小組會安裝作業系統。 隨時間過去，會需要維護 HLI 單元上的作業系統 (例如：修補、調整、升級等)。

在對作業系統進行重大變更 (例如, 將 SP1 升級至 SP2) 之前, 您必須先開啟支援票證來諮詢 Microsoft 營運小組, 以取得相關資訊。

包含在您的票證中:

* 您的 HLI 訂用帳戶識別碼。
* 您的伺服器名稱。
* 您打算套用的修補等級。
* 您打算進行變更的日期。 

建議您在合適的升級日期之前，開啟此票證至少一周，因為作業小組會檢查您的伺服器刀鋒視窗上是否需要進行韌體升級。


如需具有不同 Linux 版本之 SAP HANA 版本的支援矩陣，請參閱 [SAP 附註 #2235581](https://launchpad.support.sap.com/#/notes/2235581)。


## <a name="known-issues"></a>已知問題

以下是升級時一些已知的常見問題：
- II 型 SKU 類別的 SKU 進行作業系統升級之後，會移除 Software Foundation 軟體 (SFS)。 在作業系統升級之後, 您必須重新安裝相容的 SFS。
- 乙太網路卡驅動程式 (ENIC 和 FNIC) 會復原至舊版本。 升級之後, 您必須重新安裝相容的驅動程式版本。

## <a name="next-steps"></a>後續步驟
- 請參閱類型 I 的 SKU 類別作業系統的[備份和還原](hana-overview-high-availability-disaster-recovery.md)。
- 請參閱類型 ii SKU 類別[之修訂版3戳記的類型 Ii sku 的 OS 備份](os-backup-type-ii-skus.md)。
