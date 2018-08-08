---
title: Azure SSIS 整合執行階段的商務持續性和災害復原 (BCDR) 建議 | Microsoft Docs
description: 本文將概述 Azure SSIS 整合執行階段的商務持續性和災害復原建議。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/26/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 37347df2d543116085f52fed76c692b60fac2ad6
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/27/2018
ms.locfileid: "39295226"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-recommendations-for-azure-ssis-integration-runtime"></a>Azure SSIS 整合執行階段的商務持續性和災害復原 (BCDR) 建議

若要進行災害復原，您可以在目前執行 Azure-SSIS 整合執行階段的區域中停止 Azure-SSIS 整合執行階段，然後切換到另一個區域後，再將其重新啟動。 我們建議您使用 [Azure 配對區域](../best-practices-availability-paired-regions.md)來進行此作業。

## <a name="prerequisites"></a>必要條件

- 請確定您已針對 Azure SQL Database 伺服器啟用災害復原，以免服器在同一時間發生中斷。 如需詳細資訊，請參閱[使用 Azure SQL Database 的商務持續性概觀](../sql-database/sql-database-business-continuity.md)。

- 如果您在目前的區域中使用虛擬網路，那麼您需要在新區域中使用另一個虛擬網路來與 Azure SSIS 整合執行階段連線。 如需詳細資訊，請參閱[將 Azure-SSIS 整合執行階段加入虛擬網路](join-azure-ssis-integration-runtime-virtual-network.md)。

- 如果您使用自訂安裝，可能需要為儲存自訂安裝指令碼和相關檔案的 blob 容器準備另一個 SAS URI，如此一來，發生中斷時仍可繼續存取此 blob 容器。 如需詳細資訊，請參閱[在 Azure-SSIS 整合執行階段上設定自訂安裝](how-to-configure-azure-ssis-ir-custom-setup.md)。

## <a name="steps"></a>步驟

請遵循下列步驟來停止 Azure SSIS 整合執行階段，並將整合執行階段切換到新區域，然後重新啟動該整合執行階段。

1. 停止原始區域中的整合執行階段。

2. 在 PowerShell 中呼叫下列命令來更新整合執行階段

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    如需此 PowerShell 命令的詳細資訊，請參閱[在 Azure Data Factory 中建立 Azure-SSIS 整合執行階段](create-azure-ssis-integration-runtime.md)

3. 重新啟動整合執行階段。

## <a name="next-steps"></a>後續步驟

請考量 Azure-SSIS 整合執行階段的下列其他組態選項：

- [設定 Azure-SSIS Integration Runtime 以獲得高效能](configure-azure-ssis-integration-runtime-performance.md)

- [自訂 Azure-SSIS 整合執行階段的安裝](how-to-configure-azure-ssis-ir-custom-setup.md)

- [佈建 Azure-SSIS 整合執行階段的企業版](how-to-configure-azure-ssis-ir-enterprise-edition.md)
