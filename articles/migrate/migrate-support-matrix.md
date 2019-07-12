---
title: Azure 的移轉支援矩陣
description: 提供支援設定和 Azure Migrate 服務的限制的摘要。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: raynew
ms.openlocfilehash: b2ca1b9118ecc3d112a49bb4c79b413c46fe67cb
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811553"
---
# <a name="azure-migrate-support-matrix"></a>Azure 的移轉支援矩陣

您可以使用[Azure Migrate 服務](migrate-overview.md)評定，並將機器移轉至 Microsoft Azure 雲端。 本文摘要說明一般的支援設定和 Azure Migrate 的案例和部署的限制。


## <a name="azure-migrate-versions"></a>Azure Migrate 版本

有兩個版本的 Azure Migrate 服務：

- **目前版本**:使用此版本，您可以建立新的 Azure Migrate 專案，探索內部部署會評估，並協調評估及移轉。 [深入了解](whats-new.md#azure-migrate-new-version)。
- **舊版**:使用舊版的 Azure Migrate （支援的內部部署 VMware Vm 的評量） 的客戶，您現在應該使用目前的版本。 在舊版中，您無法建立新的 Azure Migrate 專案，或執行新的探索。

## <a name="supported-migration-scenarios"></a>支援的移轉案例

下表摘要說明支援的移轉案例。

**部署** | **詳細資料*** 
--- | --- 
**在內部評估** | 評估內部部署工作負載和 VMware Vm 和 HYPER-V Vm 上執行的資料。 評估 Azure Migrate Server 評量和 Microsoft Data Migration Assistant (DMA)，以及第三方工具，包括 Cloudamize、 Corent 技術和 Turbonomic 伺服器使用。
**在內部部署移轉至 Azure** | 移轉工作負載和實體伺服器、 VMware Vm、 HYPER-V Vm，或 AWS/GCP 的執行個體，azure 上執行的資料。 使用 Azure Migrate Server 評量和 Azure 資料庫移轉服務 (DMS)，進行移轉，以及使用第三方工具，包括 Carbonite 和 CorentTech。

特定的工具支援的摘要，如下所示。

**工具** | **評量/移轉** | **詳細資料**
--- | --- | ---
Azure Migrate Server 評定 | 評量 | 試試看 server 評估[HYPER-V](tutorial-prepare-hyper-v.md)並[VMware](tutorial-prepare-vmware.md)。
Cloudamize | 評量 | [深入了解](https://www.cloudamize.com/platform#tab-0)。
CorentTech | 評量 | [深入了解](https://www.corenttech.com/)。
Turbonomic | 評量 | [深入了解](https://turbonomic.com/solutions/technologies/azure-cloud/)。
Azure Migrate Server 移轉 | 移轉 | 試試看的伺服器移轉[HYPER-V](tutorial-migrate-hyper-v.md)並[VMware](tutorial-migrate-vmware.md)。
Carbonite | 移轉 | [深入了解](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure)。
CorentTech | 移轉 | [深入了解](https://www.corenttech.com/)。


## <a name="azure-migrate-projects"></a>Azure Migrate 專案

**支援** | **詳細資料**
--- | ---
Subscription | 您可以在訂用帳戶的單一 Azure Migrate 專案。
Azure 權限 | 您需要 「 參與者 」 或 「 擁有者權限的訂用帳戶中建立 Azure Migrate 專案。
VMware VM  | 評估最多 35000 單一專案中的 VMware Vm。
Hyper-V VM | 評估最多 10,000 個單一專案中的 HYPER-V Vm。

專案可以包含 VMware Vm 和 HYPER-V Vm 到評定的上限。


## <a name="vmware-assessment-and-migration"></a>VMware 評定及移轉

[檢閱](migrate-support-matrix-vmware.md)Azure Migrate Server 評量與伺服器移轉支援矩陣 VMware 虛擬機器。

## <a name="hyper-v-assessment-and-migration"></a>HYPER-V 評定及移轉

[檢閱](migrate-support-matrix-hyper-v.md)Azure Migrate Server 評量與伺服器移轉支援矩陣 HYPER-V 虛擬機器。


## <a name="next-steps"></a>後續步驟

- [評定 VMware Vm](tutorial-assess-vmware.md)進行移轉。
- [評估 HYPER-V Vm](tutorial-assess-hyper-v.md)進行移轉。

