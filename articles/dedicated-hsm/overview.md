---
title: 專用硬體安全模組 (HSM) 是什麼？ | Microsoft Docs
description: Azure 專用硬體安全模組 (HSM) 可在 Azure 內提供金鑰儲存功能，以符合 FIPS 140-2 等級 3 認證
services: dedicated-hsm
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 11/26/2018
ms.author: barclayn
ms.openlocfilehash: 92d77ec886a0f37c28f5e3031a7e14f63299c8aa
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/27/2018
ms.locfileid: "52427109"
---
# <a name="what-is-dedicated-hsm"></a>專用硬體安全模組 (HSM) 是什麼？

Azure 專用硬體安全模組 (HSM) 可在 Azure 內提供密碼編譯金鑰儲存體，以符合最嚴格的安全性需求。 若客戶需要通過 FIPS 140-2 等級 3 驗證的裝置，以及完整且專屬的 HSM 設備控制權，則專用硬體安全模組 (HSM) 是理想的解決方案。 硬體安全模組 (HSM) 裝置可跨數個 Azure 區域進行全域部署，並可輕鬆地佈建為一對裝置及設定高可用性。 硬體安全模組 (HSM) 也可跨區域進行佈建，以確保能提供區域層級的容錯移轉。 Microsoft 已使用 Gemalto 中的 [SafeNet Luna 網路 HSM 7 (A790 型號)](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/) 設備來提供專用硬體安全模組 (HSM) 服務。 此裝置會提供最高層級的效能及密碼編譯整合選項。 佈建時，硬體安全模組 (HSM) 會直接連線到客戶的虛擬網路，並可藉由設定點對站或站對站 VPN 連線來讓內部部署應用程式和管理工具存取 HSM。 客戶會從 Gemalto 的支援入口網站取得設定及管理 HSM 裝置所需的軟體和文件。

## <a name="why-use-azure-dedicated-hsm"></a>為何使用 Azure 專用硬體安全模組 (HSM)？

### <a name="fips-140-2-level-3-compliance"></a>符合 FIPS 140-2 層級 3 的規範

許多組織都有嚴格的業界規範，規定密碼編譯金鑰儲存體需符合 [FIPS 140-2 等級 3](https://csrc.nist.gov/publications/detail/fips/140/2/final) 的需求。 Microsoft 的多租用戶 Azure Key Vault 服務目前只提供 FIPS 140-2 等級 2 認證。 針對金融服務產業、政府機關和其他必須符合 FIPS 140-2 等級 3 需求的其他產業，Azure 專用硬體安全模組 (HSM) 可滿足他們的實際需求。

### <a name="single-tenant-devices"></a>單一租用戶裝置

我們有許多客戶會要求對密碼編譯儲存體裝置採用單一租用戶。 Azure 專用硬體安全模組 (HSM) 服務可允許從 Microsoft 的其中一個全域散發資料中心佈建實體裝置。 一旦對客戶進行佈建後，就只有該客戶能夠存取裝置。

### <a name="full-administrative-control"></a>完整的系統管理控制權

除了單一租用戶裝置外，許多客戶會需要完整的系統管理控制權，以及用於系統管理的唯一存取權。 佈建之後，只有該客戶有裝置的所有系統管理或應用程式層級存取權。 在客戶第一次存取裝置，並由系統要求變更密碼之後，Microsoft 就沒有任何系統管理控制權。 從此時間點開始，客戶就是具有完整系統管理控制權和應用程式管理能力的單一租用戶。 Microsoft 仍可透過序列埠連線擁有監視層級的遙測資料存取權 (不是系統管理員角色)，以監視硬體相關項目，例如溫度、能源供應健康情況和風扇健康情況等。 如有需要，客戶可自由地停用此功能，但之後將無法收到 Microsoft 主動發出的健康情況警示。

### <a name="high-performance"></a>高效能

這項服務之所以選用 Gemalto 裝置，是因為其支援廣泛的密碼編譯演算法、多種作業系統和廣泛的 API。 針對 RSA 2048，已部署的特定模型可提供每秒 10,000 個作業數的絕佳效能。 其支援 10 個用於唯一應用程式執行個體的分割區。 這是低延遲、高容量及高輸送量的裝置。

### <a name="unique-cloud-based-offering"></a>唯一的雲端式供應項目

Microsoft 在獨特的客戶群之間發現了特定需求，也是唯一提供專用硬體安全模組 (HSM) 服務給新客戶的雲端提供者，HSM 服務已通過 FIPS 140-2 等級 3 的驗證，並在此層級上提供雲端式應用程式和內部部署應用程式的整合。

## <a name="is-azure-dedicated-hsm-right-for-you"></a>Azure 專用硬體安全模組 (HSM) 適合您嗎？

Azure 專用硬體安全模組 (HSM) 是特製化服務，專為特定類型的大型組織處理獨特需求。 因此，我們預期大部分的 Azure 客戶並不符合使用這項服務的條件。 許多組織會發現 Azure Key Vault 服務更適合他們，而且更符合成本效益。 為協助判斷您的需求是否適用此服務，我們已整理出下列準則。

### <a name="best-fit"></a>最適用

「隨即轉移」案例最適用此服務，其需要 HSM 裝置的唯一直接存取權。 範例包括：

- 將應用程式從內部部署遷移至 Azure 虛擬機器
- 將應用程式從 Amazon AWS EC2 遷移至使用 AWS 雲端 HSM 傳統服務 (Amazon 不會提供此服務給新客戶) 的 Azure 虛擬機器
- 在 Azure 虛擬機器中執行 Apache/Ngnix SSL 卸載、Oracle TDE 和 ADCS 等拆封授權軟體

### <a name="not-a-fit"></a>不適用

可使用客戶管理金鑰進行加密的 Microsoft 雲端服務不可與 Azure 專用硬體安全模組 (HSM) 整合，這些服務包括 Azure 資訊保護、Azure 磁碟加密、Azure Data Lake Store、Azure 儲存體、Azure SQL、Office 365 客戶金鑰等。

### <a name="it-depends"></a>視情況而定

有許多案例會取決於潛在的複雜需求組合，以及有或沒有折衷辦法。 例如 FIPS 140-2 等級 3 需求，這通常是明文規定的項目，因此，專用硬體安全模組 (HSM) 是目前唯一的選項。  如果與這些明文規定的需求無關，則通常會根據需求組合的評估，來決定使用 Azure Key Vault 或專用硬體安全模組 (HSM)。 範例包括：

- 在新客戶的 Azure 虛擬機器中執行新程式碼
- Azure 虛擬機器中的 SQL Server TDE
- Azure 儲存體用戶端加密
- SQL Server 和 Azure SQL DB Always Encrypted

## <a name="next-steps"></a>後續步驟

考慮到此服務的高度特製化本質，建議您應完全了解本文件集中找到的一些重要概念，並完全了解定價、支援和服務等級協定，以及完成快速將硬體安全模組 (HSM) 佈建到現有虛擬網路環境的教學課程。 用來決定部署架構的 [Gemalto 整合指南](https://safenet.gemalto.com/partners/microsoft/)及操作指南也是絕佳資源。

* [高可用性](high-availability.md)
* [實體安全性](physical-security.md)
* [網路功能](networking.md)
* [支援能力](supportability.md)
* [監視](monitoring.md)
