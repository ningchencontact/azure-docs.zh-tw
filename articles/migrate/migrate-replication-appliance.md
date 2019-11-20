---
title: Azure Migrate 複寫設備架構
description: 概述以代理程式為基礎的遷移的 Azure Migrate 複寫設備。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: ba14767bde5d6cdca3a82dbe4e8a115ec25cc911
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186546"
---
# <a name="replication-appliance"></a>複寫設備

本文說明使用以代理程式為基礎的遷移，將 VMware Vm、實體機器和私人/公用雲端 Vm 遷移至 Azure 時，Azure Migrate：伺服器評估所使用的複寫設備。 

此工具可在[Azure Migrate](migrate-overview.md)中樞中取得。 中樞提供了用於評估和遷移的原生工具，以及來自其他 Azure 服務和協力廠商獨立軟體廠商（Isv）的工具。


## <a name="appliance-overview"></a>設備總覽

複寫設備會部署為單一內部部署機器，以作為 VMware VM 或實體伺服器。 它會執行：
- 複寫**設備**：複寫設備會協調內部部署 VMware vm 和實體伺服器複寫至 Azure 的通訊及管理資料複寫。
- **進程伺服器**：依預設會安裝在複寫應用裝置上的進程伺服器，並執行下列動作：
    - 複寫**閘道**：它會作為複寫閘道。 它會從啟用複寫的機器接收復寫資料。 它會使用快取、壓縮和加密來優化複寫資料，並將其傳送至 Azure。
    - **代理程式安裝程式**：執行行動服務的推入安裝。 這項服務必須安裝在您想要複寫以進行遷移的每部內部部署機器上執行。

## <a name="appliance-deployment"></a>設備部署

**部署為** | **用於** | **詳細資料**
--- | --- |  ---
VMware VM | 通常在使用 Azure Migrate 遷移工具搭配代理程式型遷移來遷移 VMware Vm 時使用。 | 您可以從 Azure Migrate 中樞下載 OVA 範本，並匯入至 vCenter Server 以建立設備 VM。
實體機器 | 如果您沒有 VMware 基礎結構，或無法使用 OVA 範本建立 VMware VM，則在遷移內部部署實體伺服器時使用。 | 您可以從 Azure Migrate 中樞下載軟體安裝程式，並加以執行以設定設備機器。

## <a name="appliance-deployment-requirements"></a>設備部署需求

[檢查](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements)部署需求。



## <a name="appliance-license"></a>設備授權
設備隨附 Windows Server 2016 評估授權，其有效期為180天。 如果評估期接近到期日，建議您下載並部署新的應用裝置，或啟用設備 VM 的作業系統授權。

## <a name="replication-process"></a>複寫程序

1. 當您啟用 VM 複寫時，首先會使用指定的複寫原則開始複寫至 Azure 儲存體。 
2. 流量會透過網際網路複寫到 Azure 儲存體的公用端點。 不支援從內部部署網站透過站對站虛擬私人網路 (VPN) 將流量複寫至 Azure。
3. 初始複寫完成後，就會開始進行差異複寫。 系統會記錄機器的追蹤變更。
4. 進行通訊的過程如下：
    - Vm 會與埠 HTTPS 443 輸入上的複寫設備進行通訊，以進行複寫管理。
    - 複寫設備會透過埠 HTTPS 443 輸出來協調與 Azure 的複寫。
    - Vm 會將複寫資料傳送至埠 HTTPS 9443 傳入的進程伺服器（在複寫應用裝置上執行）。 您可以修改此連接埠。
    - 處理伺服器會透過輸出連接埠 443 接收複寫資料、將其最佳化並加密，然後傳送至 Azure 儲存體。
5. 複寫資料記錄會先在 Azure 中的快取儲存體帳戶中進行。 系統會處理這些記錄，並將資料儲存在 Azure 受控磁片中。

![架構](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>設備升級

設備會從 Azure Migrate 中樞手動升級。 我們建議您一律執行最新版本。

1. 在 Azure Migrate > 伺服器 > Azure Migrate：伺服器評估、基礎結構伺服器，請按一下 [設定**伺服器**]。
2. 在 [設定**伺服器**] 中，當有新版本的複寫設備可供使用時，會在**代理程式版本**中出現連結。 
3. 將安裝程式下載至複寫設備機器，並安裝升級。 安裝程式會偵測設備上目前正在執行的版本。
 
## <a name="next-steps"></a>後續步驟

[瞭解如何](tutorial-assess-vmware.md#set-up-the-appliance-vm)設定 VMware 的應用裝置。
[瞭解如何](tutorial-assess-hyper-v.md#set-up-the-appliance-vm)設定 hyper-v 的應用裝置。

