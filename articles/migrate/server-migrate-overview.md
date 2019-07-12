---
title: 選取 Azure 移轉的伺服器移轉的 VMware 移轉選項 |Microsoft Docs
description: 提供 Azure 中使用 Azure 移轉的伺服器移轉的 VMware vm 移轉的選項概觀
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: f8bfbe26dc4c6ddbcf622f91938ba060de00b2ec
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811566"
---
# <a name="select-a-vmware-migration-option"></a>選取 VMware 移轉選項

您可以將 VMware Vm 移轉至 Azure 中使用 Azure Migrate Server 移轉工具。 這項工具提供幾個 VMware VM 移轉的選項：

- 移轉使用無代理程式的複寫。 將 Vm 移轉而不需要在其上安裝任何項目。
- 使用複寫代理程式的移轉。 複寫的 VM 上安裝代理程式。


雖然無代理程式的複寫從部署觀點來看更容易，但它目前有一些限制。

## <a name="agentless-migration-limitations"></a>無代理程式的移轉限制

限制如下所示：

- **同時複寫**:50 個 Vm 最多可以同時複寫，從 vCenter Server。<br/> 如果您有超過 50 個 Vm，可供移轉，建立多個批次的 Vm。<br/> 複寫多一次，將會影響效能。
- **VM 磁碟**:您想要移轉的 VM 必須有 60 或更少的磁碟。
- **VM 作業系統**:一般情況下，Azure Migrate 可以移轉的任何 Windows Server 或 Linux 作業系統，但它可能需要在 Vm 上的變更，以便它們可以在 Azure 中執行。 Azure Migrate 會進行自動為這些作業系統的變更：
    - Red Hat Enterprise Linux 6.5 + 7.0 +
    - CentOS 6.5+, 7.0+
    - SUSE Linux Enterprise Server 12 SP1+
    - Ubuntu 14.04LTS，16.04LTS、 18.04LTS
    - Debian 7、 8
    - 對於其他作業系統中，您需要進行移轉之前以手動方式調整。 [移轉教學課程](tutorial-migrate-vmware.md)說明如何執行這項操作。
- **Linux 開機**:如果 /boot 專用的磁碟分割上，它應該位於 OS 磁碟，並不會分散到多個磁碟。<br/> /Boot 是否屬於根 （/） 磁碟分割，然後 '/' 的分割區應該位於 OS 磁碟，並不會跨越其他磁碟。
- **UEFI 開機**:移轉不支援使用 UEFI 開機的 Vm。
- **加密磁碟/磁碟區 (BitLocker，cryptfs)** :不支援移轉具有加密的磁碟/磁碟區的 Vm。
- **RDM/穿通磁碟**:如果 Vm 有 RDM 或傳遞磁碟，這些磁碟將不會複寫至 Azure
- **NFS**:不會複寫的 Vm 上掛接為磁碟區的 NFS 磁碟區。
- **目標儲存體**:您可以只 VMware Vm 移轉至 Azure Vm 使用受控磁碟 （HDD 標準、 進階 SSD）。



## <a name="deployment-steps-comparison"></a>部署步驟的比較

檢閱之後的限制，請了解部署每個解決方案所需的步驟可協助您決定要選擇哪個選項。

**Task** | **詳細資料** |**無代理程式** | **代理程式為基礎**
--- | --- | --- | ---
**VMware 伺服器和 Vm 準備移轉** | 在 VMware 伺服器和 Vm 上設定一些設定。 | 必要項 | 必要項
**新增 Server 移轉工具** | 在 Azure Migrate 專案中新增 Azure Migrate Server 移轉工具。 | 必要項 | 必要項
**部署 Azure Migrate 應用裝置** | 設定適用於 VM 探索及評定 VMware VM 上的輕量型設備。 | 必要項 | 不需要。
**在 Vm 上安裝行動服務** | 每個您想要複寫的 VM 上安裝行動服務 | 不需要 | 必要項
**部署 Azure 移轉的伺服器移轉複寫設備** | 設定 VMware VM 上的應用裝置，來探索 Vm，並執行 Vm 和 Azure 移轉的伺服器移轉的行動服務之間的橋接 | 不需要 | 必要項
**將 Vm 複寫**。 啟用 VM 複寫。 | 設定複寫設定，然後選取要複寫的 Vm | 必要項 | 必要項
**執行測試移轉** | 執行測試移轉，確定一切如預期般運作。 | 必要項 | 必要項
**執行完整的移轉** | 將 Vm 移轉。 | 必要項 | 必要項




## <a name="next-steps"></a>後續步驟

[將 VMware Vm 移轉](tutorial-migrate-vmware.md)使用無代理程式的移轉。



