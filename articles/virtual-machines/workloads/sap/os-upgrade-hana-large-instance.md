---
title: 在 Azure 上執行 SAP Hana (大型執行個體) 的作業系統升級 | Microsoft Docs
description: 在 Azure 上執行 SAP Hana on Azure (大型執行個體) 的作業系統升級
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1354b67ade78a0da1578ef9d98c3b1e8edb41cd4
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077743"
---
# <a name="operating-system-upgrade"></a>作業系統升級
本文件會詳述 Hana 大型執行個體作業系統升級的詳細資訊。

>[!NOTE]
>作業系統升級是客戶的責任，Microsoft 作業支援可以指引您在升級期間應特別注意的關鍵區域。 規劃升級之前，請先諮詢您的作業系統廠商。

佈建 HLI 單元時，Microsoft 作業小組即會安裝作業系統。 隨時間過去，會需要維護 HLI 單元上的作業系統 (例如：修補、調整、升級等)。

對作業系統進行大型變更前 (例如作業系統升級)，請**務必**將以下相容性矩陣納入考量。 開始進行升級等重大作業系統活動之前，也請**務必**建立請求諮詢用的支援票證，藉以連絡 Microsoft 作業小組。

如需具有不同 Linux 版本之 SAP HANA 版本的支援矩陣，請參閱 [SAP 附註 #2235581](https://launchpad.support.sap.com/#/notes/2235581)。

HLI 的下列相容性已經過測試。 如果您的 HLI 伺服器未列於相容性矩陣之中，請連絡 Microsoft 作業支援。

## <a name="for-type-i-class-sku-category"></a>類型 I 類別的 SKU 分類

| 組態 | SUSE12 SP1 | SUSE12 SP2 | RHEL 7.2 | RHEL 7.3|
| --- | --- | --- | --- | --- |
| 伺服器韌體 | 3.1(2b) | 3.1(2b) | 3.1(2b) | 3.1(2b) |
| ENIC 版本 | 2.3.0.44 | 2.3.0.44 | 2.3.0.30 | 2.3.0.44 |
| FNIC 版本 | 1.6.0.34 | 1.6.0.34 | 1.6.0.27 | 1.6.0.36 |
| EDAC | 已停用 | 已停用 | 已停用 | 已停用 |
| 核心版本 | 4.4.21-69-default | 3.12.49-11-default | 3.10.0-327.el7.x86_64 | 3.10.0-693.17.1 |


## <a name="for-type-ii-class-sku-category"></a>類型 II 類別的 SKU 分類

| 組態 | SUSE12 SP1 | SUSE12 SP2 | RHEL 7.2 | RHEL 7.3|
| --- | --- | --- | --- | --- |
| RMC 韌體版本 | 1.1.121  | 1.1.121  | 1.1.121  | 1.1.121 |
| BMC 韌體版本 | 1.0.43   | 1.0.43   | 1.0.43   | 1.0.43  |
| Software Foundation Server (SFS) 版本 | 2.16    | 2.16    | 2.14/2.16   | 2.16   |
| BIOS | 5.2.6    | 5.2.6    | 5.2.6    | 5.2.6   |
| i40e 版本    | 2.0.19     | 2.0.19     | 1.5.10-k    | 1.5.10-k   |
| 核心版本    | 3.12.49-11.1     | 4.4.21-69.1     | 3.10.0-327    | 3.10.0-693.17.1   |


## <a name="known-issues"></a>已知問題

以下是升級時一些已知的常見問題：
- II 型 SKU 類別的 SKU 進行作業系統升級之後，會移除 Software Foundation 軟體 (SFS)。 您必須在作業系統升級後重新安裝相容的 SFS。
- 乙太網路卡驅動程式 (ENIC 和 FNIC) 會復原至舊版本。 升級後必須重新安裝相容的驅動程式版本。

## <a name="next-steps"></a>後續步驟
- 請參閱類型 I 的 SKU 類別作業系統的[備份和還原](hana-overview-high-availability-disaster-recovery.md)。
- 請參閱類型 II 的 SKU 類別的[類型 II SKU 作業系統備份](os-backup-type-ii-skus.md)。