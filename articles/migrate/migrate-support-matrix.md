---
title: Azure Migrate 支援矩陣
description: 提供支援設定的摘要, 以及 Azure Migrate 服務的限制。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: raynew
ms.openlocfilehash: 49bd193303255cdf7d18fd5da9dec8d84c50a829
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019195"
---
# <a name="azure-migrate-support-matrix"></a>Azure Migrate 支援矩陣

您可以使用[Azure Migrate 服務](migrate-overview.md)來評估電腦, 並將機器遷移至 Microsoft Azure 雲端。 本文摘要說明 Azure Migrate 案例和部署的一般支援設定和限制。


## <a name="azure-migrate-versions"></a>Azure Migrate 版本

Azure Migrate 服務有兩個版本：

- **目前的版本**：使用此版本, 您可以建立新的 Azure Migrate 專案、探索內部部署評估, 以及協調評量與遷移。 [深入了解](whats-new.md#azure-migrate-new-version)。
- **先前版本**：針對使用舊版 Azure Migrate (僅支援內部部署 VMware VM 的評量) 的客戶，您現在應該改為使用目前的版本。 在先前的版本中, 您無法建立新的 Azure Migrate 專案或執行新的探索。

## <a name="supported-migration-scenarios"></a>支援的遷移案例

下表摘要說明支援的遷移案例。

**部署** | **說明*** 
--- | --- 
**內部部署評量** | 評估在 VMware Vm 和 Hyper-v Vm 上執行的內部部署工作負載和資料。 評估使用 Azure Migrate Server 評估和 Microsoft Data Migration Assistant (DMA), 以及包含 Cloudamize、Corent Tech 和 Turbonomic 伺服器的協力廠商工具。
**內部部署至 Azure 的遷移** | 將實體伺服器、VMware Vm、Hyper-v Vm 和 AWS/GCP 實例上執行的工作負載和資料移轉至 Azure。 使用 Azure Migrate Server 評估和 Azure 資料庫移轉服務 (DMS) 進行遷移, 以及使用包含 Carbonite 和 CorentTech 的協力廠商工具。

特定工具支援摘要如下。

**工具** | **評量/遷移** | **詳細資料**
--- | --- | ---
Azure Migrate 伺服器評量 | 評定 | 試用[hyper-v](tutorial-prepare-hyper-v.md)和[VMware](tutorial-prepare-vmware.md)的伺服器評估。
Cloudamize | 評定 | [深入了解](https://www.cloudamize.com/platform#tab-0)。
CorentTech | 評定 | [深入了解](https://www.corenttech.com/)。
Turbonomic | 評定 | [深入了解](https://turbonomic.com/solutions/technologies/azure-cloud/)。
Azure Migrate 伺服器移轉 | 遷移 | 試用[hyper-v](tutorial-migrate-hyper-v.md)和[VMware](tutorial-migrate-vmware.md)的伺服器遷移。
Carbonite | 遷移 | [深入了解](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure)。
CorentTech | 遷移 | [深入了解](https://www.corenttech.com/)。


## <a name="azure-migrate-projects"></a>Azure Migrate 專案

**支援** | **詳細資料**
--- | ---
訂閱 | 您在訂用帳戶中可以有單一 Azure Migrate 專案。
Azure 權限 | 您需要訂用帳戶中的「參與者」或「擁有者」許可權, 才能建立 Azure Migrate 專案。
VMware VM  | 在單一專案中評估最多35000個 VMware Vm。
Hyper-V VM | 在單一專案中評估最多10000個 Hyper-v Vm。

專案可以包含 VMware Vm 和 Hyper-v Vm, 最多可達評量限制。


## <a name="vmware-assessment-and-migration"></a>VMware 評估與遷移

請[參閱](migrate-support-matrix-vmware.md)適用于 VMware vm 的 Azure Migrate server 評估和伺服器遷移支援對照表。

## <a name="hyper-v-assessment-and-migration"></a>Hyper-V 評量及移轉

請[參閱](migrate-support-matrix-hyper-v.md)適用于 hyper-v vm 的 Azure Migrate server 評估和伺服器遷移支援對照表。


## <a name="next-steps"></a>後續步驟

- [評估 VMware vm](tutorial-assess-vmware.md)以進行遷移。
- [評估 Hyper-v vm](tutorial-assess-hyper-v.md)以進行遷移。

