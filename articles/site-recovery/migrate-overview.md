---
title: 關於使用 Azure Site Recovery 對內部部署機器與 Azure VM 進行移轉 | Microsoft Docs
description: 本文說明如何使用 Azure Site Recovery 服務將內部部署和 Azure IaaS VM 移轉至 Azure。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: raynew
ms.openlocfilehash: 856d03b1ecc1c7a3bd527eb265061f9a305d8f50
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60196043"
---
# <a name="about-migration"></a>關於移轉

請閱讀本文，了解 [Azure Site Recovery](site-recovery-overview.md) 服務如何協助您移轉機器的快速概觀。 

以下是您可以使用 Site Recovery 移轉的項目：

- **從內部部署遷移至 Azure**：將內部部署 Hyper-V VM、VMware VM 和實體伺服器遷移至 Azure。 移轉之後，內部部署機器上執行的工作負載將在 Azure VM 上執行。 
- **在 Azure 內遷移**：在 Azure 區域之間遷移 Azure VM。 
- **遷移 AWS**：將 AWS Windows 執行個體遷移到 Azure IaaS VM。 


## <a name="what-do-we-mean-by-migration"></a>移轉的意思為何？

除了使用站台復原進行內部部署與 Azure VM 的災害復原，您還可以使用 Site Recovery 服務來加以移轉。 有何不同？

- 對於災害復原，您需要定期將機器複寫至 Azure。 當發生運行中斷時，您就會將該機器從主要站台容錯移轉至 Azure 次要站台，並從該處進行存取。 當主要站台恢復可用狀態時，您就可以從 Azure 進行容錯回復。
- 進行移轉時，您會將內部部署機器複寫至 Azure，或將 Azure VM 複寫至次要地區。 然後，將 VM 從主要站台容錯移轉至次要站台，並完成移轉流程。 其中不包含容錯回復。  


## <a name="migration-scenarios"></a>移轉案例

**案例** | **詳細資料**
--- | ---
**從內部部署移轉至 Azure** | 您可以將內部部署 VMware VM、Hyper-V VM 和實體伺服器移轉至 Azure。 若要這樣做，就要完成與完整災害復原幾乎相同的步驟。 只是不要將機器從 Azure 容錯移轉回到內部部署站台。
**在不同的 Azure 地區之間移轉** | 您可以將 Azure VM 從一個 Azure 區域移轉至另一個 Azure 區域。 完成移轉之後，現在您就可以在移轉前往的次要區域中，設定 Azure VM 的災害復原。
**將 AWS 移轉至 Azure** | 您可以將 AWS 執行個體移轉至 Azure VM。 在進行移轉時，Site Recovery 會將 AWS 執行個體視為實體伺服器。 

## <a name="next-steps"></a>後續步驟

- [將內部部署機器移轉至 Azure](migrate-tutorial-on-premises-azure.md)
- [在 Azure 區域間移轉 VM](azure-to-azure-tutorial-migrate.md)
- [將 AWS 移轉至 Azure](migrate-tutorial-aws-azure.md)
