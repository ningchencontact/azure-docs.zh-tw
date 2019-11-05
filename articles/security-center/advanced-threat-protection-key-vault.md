---
title: 如何設定 Azure Key Vault 的先進威脅防護 |Microsoft Docs
description: 本文說明如何在 Azure 資訊安全中心中設定 Azure Key Vault 的先進威脅防護
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: b28f03f0997b6bb2c494c35cee9695a478906c47
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521874"
---
# <a name="how-to-set-up-advanced-threat-protection-for-azure-key-vault-preview"></a>如何設定 Azure Key Vault 的先進威脅防護（預覽）

Azure Key Vault 的先進威脅防護提供一層額外的安全性情報。 此工具會偵測可能有害的嘗試存取或惡意探索 Key Vault 帳戶。 使用資訊安全中心的原生先進威脅防護，您可以不是安全性專家，也不需要學習額外的安全性監視系統，就能解決威脅。

當資訊安全中心偵測到異常活動時，它會顯示警示。 它也會以可疑活動的詳細資料，以及如何調查並補救已識別威脅的建議，以電子郵件寄給訂用帳戶管理員。 

> [!NOTE]
> Azure Key Vault 的先進威脅防護目前僅適用于北美洲區域。

## <a name="to-set-up-advanced-threat-protection-from-azure-security-center"></a>若要從 Azure 資訊安全中心設定先進的威脅防護

根據預設，當您訂閱資訊安全中心的標準層時，會為您的所有 Key Vault 帳戶啟用「先進的威脅防護」（請參閱[定價](security-center-pricing.md)）。 

若要啟用或停用特定訂用帳戶的保護：

1. 從資訊安全中心的提要欄位中，按一下 **定價 & 設定**。
1. 選取您想要啟用或停用威脅防護之儲存體帳戶的訂用帳戶。
1. 按一下 [定價層]。
1. 從 [**依資源類型選取定價層**] 群組，尋找 [金鑰保存庫] 資料列，然後按一下 [**啟用**] 或 [**停用**]。
    [![在 Azure 資訊安全中心中啟用或停用 Key Vault 的「先進的威脅防護」](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)
1. 按一下 [儲存]。


## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何啟用和停用 Azure Key Vault 的 [先進的威脅防護]。 

如需其他相關內容，請參閱下列文章：

- [資訊安全中心中 Azure 服務層級的威脅偵測](security-center-alerts-service-layer.md)-這篇文章說明與 advanced 威脅防護相關的警示 Azure Key Vault