---
title: Azure Migrate 複寫設備架構 |Microsoft Docs
description: 提供在 Azure Migrate 複寫應用裝置的概觀
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: 4f4dc307bee4190a0e94ace493053e0cfd01150e
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811436"
---
# <a name="replication-appliance"></a>複寫應用裝置

本文說明使用 Azure migrate 的複寫設備：伺服器評估時移轉 VMware Vm、 實體機器及私密/公開金鑰雲端 Vm 至 Azure，使用代理程式為基礎的移轉。 

此工具會在[Azure Migrate](migrate-overview.md)中樞。 中樞提供原生工具評定及移轉，以及從其他 Azure 服務，以及從第三方獨立軟體廠商 (Isv) 的工具。


## <a name="appliance-overview"></a>設備概觀

複寫應用裝置被部署為單一內部部署機器，做為 VMware VM 或實體伺服器。 它會執行：
- **複寫設備**:複寫設備協調通訊，以及管理資料複寫，在內部部署 VMware Vm 和實體伺服器複寫至 Azure。
- **處理序伺服器**：處理序伺服器，在 複寫應用裝置上的預設會安裝並執行下列：
    - **複寫閘道器**:它會作為複寫閘道器。 它會從啟用複寫的機器接收複寫資料。 它可最佳化使用快取、 壓縮和加密複寫資料，並將它傳送至 Azure。
    - **代理程式安裝程式**:執行行動服務推送安裝。 此服務必須安裝並執行每個內部部署移轉要複寫的機器。

## <a name="appliance-deployment"></a>設備的部署

**部署為** | **用於** | **詳細資料**
--- | --- |  ---
VMware VM | 將 VMware Vm 代理程式為基礎的移轉搭配使用 Azure 移轉的移轉工具移轉時，通常使用。 | 您從 Azure Migrate 中樞內，下載 OVA 範本，並匯入 vCenter Server 來建立設備 VM。
實體機器 | 使用移轉內部部署實體伺服器，如果您沒有 VMware 基礎結構時，或如果您無法建立使用.OVA 範本的 VMware VM。 | 您的 Azure Migrate 的中樞，從下載的軟體安裝，然後執行它以設備機器所設定。

## <a name="appliance-deployment-requirements"></a>應用裝置的部署需求

[檢閱](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements)的部署需求。



## <a name="appliance-license"></a>應用裝置授權
設備隨附於 Windows Server 2016 的評估授權，也就是有效的 180 天。 如果試用期即將到期，我們建議您下載並部署新的裝置，或您啟動的應用裝置 VM 的作業系統授權。

## <a name="replication-process"></a>複寫程序

1. 當您啟用 VM 複寫時，首先會使用指定的複寫原則開始複寫至 Azure 儲存體。 
2. 流量會透過網際網路複寫到 Azure 儲存體的公用端點。 不支援從內部部署網站透過站對站虛擬私人網路 (VPN) 將流量複寫至 Azure。
3. 初始複寫完成之後，便會開始差異複寫。 機器追蹤的變更會記錄。
4. 進行通訊的過程如下：
    - Vm 與複寫應用裝置，連接埠 HTTPS 443 上進行輸入通訊，針對複寫管理。
    - 複寫設備會協調透過輸出連接埠 HTTPS 443 與 Azure 的複寫。
    - Vm 將傳送至處理序伺服器 （複寫應用裝置上執行） 連接埠 HTTPS 9443 上的複寫資料輸入。 您可以修改此連接埠。
    - 處理伺服器會透過輸出連接埠 443 接收複寫資料、將其最佳化並加密，然後傳送至 Azure 儲存體。
5. 複寫資料會記錄在 Azure 中的快取儲存體帳戶中的首。 處理這些記錄檔和資料儲存在 Azure 受控的磁碟。

![架構](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>設備升級

應用裝置是手動升級，從 Azure Migrate 的中樞。 我們建議您一律執行最新版本。

1. 在 Azure 中移轉 > 伺服器 > Azure Migrate:伺服器評估、 基礎結構伺服器，按一下**組態伺服器**。
2. 在 **組態伺服器**，連結會出現在**代理程式版本**複寫設備的新版本可用時。 
3. 安裝程式下載到複寫的設備機器，並安裝的升級。 安裝程式偵測到版本目前應用裝置上執行。
 
## <a name="next-steps"></a>後續步驟

[了解如何](tutorial-assess-vmware.md#set-up-the-appliance-vm)來設定適用於 VMware 的設備。
[了解如何](tutorial-assess-hyper-v.md#set-up-the-appliance-vm)來設定適用於 HYPER-V 的設備。

