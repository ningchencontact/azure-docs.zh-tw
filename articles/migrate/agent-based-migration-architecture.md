---
title: Azure 移轉的伺服器移轉中的代理程式為基礎的移轉架構
description: 提供代理程式為基礎進行 Azure 移轉的伺服器移轉的 VMware VM 移轉的概觀。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: 21c779587842c976ba93d7fa592a91ee714bc55c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811150"
---
# <a name="agent-based-migration-architecture"></a>代理程式為基礎的移轉架構

本文提供的架構和使用 Azure Migrate Server 移轉工具的代理程式為基礎複寫所用的程序的概觀。

[Azure Migrate](migrate-services-overview.md)提供中央的中樞，以追蹤探索、 評估和您的內部部署應用程式和工作負載和 AWS/GCP VM 執行個體，Azure 移轉。 中樞會提供 Azure Migrate 的工具，來評估和移轉，以及第三方獨立軟體廠商 (ISV) 供應項目。

## <a name="agent-based-replication"></a>代理程式為基礎的複寫

代理程式為基礎的複寫，在 Azure Migrate Server 複寫工具用來移轉內部部署 VMware Vm 和實體伺服器至 Azure。 它也可以用來移轉其他內部部署虛擬化伺服器，以及私人和公用雲端的 Vm，包括 AWS 執行個體和 GCP Vm。

如需 VMware 移轉，Azure Migrate Server 移轉工具會提供幾個選項：

- 使用代理程式為基礎的複寫，這篇文章中所述的移轉。
- 無代理程式的複寫，將 Vm 移轉而不需要在其上安裝任何項目。

深入了解[選取移轉方法，適用於 VMware](server-migrate-overview.md)。

## <a name="server-migration-and-azure-site-recovery"></a>Server 移轉和 Azure Site Recovery

Azure 移轉的伺服器移轉是移轉在內部部署和公用雲端工作負載至 Azure 的工具。 它被適合移轉。 Site Recovery 是嚴重損壞修復工具。 Azure 的伺服器移轉和 Site Recovery 共用一些常見的技術元件，用於資料複寫，但有不同的用途。

## <a name="architectural-components"></a>架構元件

![架構](./media/agent-based-replication-architecture/architecture.png)

下表摘要說明所用元件的代理程式為基礎的移轉。

**元件** | **詳細資料** | **安裝**
--- | --- | ---
**複寫應用裝置** | 複寫應用裝置 （組態伺服器） 是在內部部署機器做為內部部署環境中，與 Azure Migrate Server 移轉工具之間的橋樑。 設備探索內部部署 VM 清查，以便 Azure 伺服器移轉來協調複寫和移轉。 應用裝置有兩個元件：<br/><br/> **組態伺服器**︰連接到 Azure 移轉的伺服器移轉，並協調複寫。<br/> **處理序伺服器**：處理資料複寫。 它會接收 VM 資料，會壓縮及加密，並將傳送至 Azure 訂用帳戶。 伺服器移轉會將資料寫入至受控磁碟。 | 預設會安裝與設定伺服器在複寫應用裝置上的處理序伺服器。
**行動服務** | 行動服務是您想要複寫，並移轉每個電腦上安裝代理程式。 它會從電腦的複寫資料傳送至處理序伺服器時。 有幾個不同的行動服務代理程式。 | 行動服務的安裝檔案位於複寫應用裝置上。 您可以下載並安裝代理程式，您也需要根據作業系統和您想要複寫之機器的版本。

### <a name="mobility-service-installation"></a>行動服務安裝

您可以使用下列方法來部署「行動服務」︰

- **推入安裝**：當您啟用機器的保護時，處理序伺服器會安裝行動服務。 
- **手動安裝**：您可以透過 UI 或命令提示字元中的每部機器上手動安裝行動服務。

行動服務與複寫設備進行通訊和複寫的機器。 如果您有複寫的應用裝置、 處理序伺服器或正在複寫的機器上執行的防毒軟體時，應從掃描排除下列資料夾：


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86)\Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent （在上安裝行動服務使用的 Windows 機器）

## <a name="replication-process"></a>複寫程序

1. 當您啟用 VM 複寫時，便會開始初始複寫至 Azure。
2. 初始在複寫期間，行動服務會從電腦磁碟讀取資料，並將它傳送至處理序伺服器。
3. 這項資料用來植入您的 Azure 訂用帳戶中磁碟的複本。 
4. 初始複寫完成之後，就會開始將差異變更複寫到 Azure。 區塊層級，而接近連續複寫。
4. 行動服務會攔截寫入至 VM 磁碟的記憶體，藉由整合與作業系統的儲存子系統。 這個方法可以避免在累加式複寫的複寫機器上的磁碟 I/O 作業。 
5. 機器追蹤的變更會傳送到處理伺服器連接埠 HTTPS 9443 上輸入。 您可以修改此連接埠。 處理序伺服器會壓縮及加密，並將它傳送至 Azure。 

## <a name="ports"></a>連接埠

**裝置** | **連接**
--- | --- 
VM | 在 Vm 上執行的行動服務與通訊連接埠 HTTPS 443 上的內部部署複寫設備輸入，針對複寫管理。<br/><br/> Vm 將複寫資料傳送至處理序伺服器 （執行中複寫的應用裝置上的預設值），以連接埠 HTTPS 9443 上輸入。 您可以修改此連接埠。
複寫應用裝置 | 複寫設備會協調透過輸出連接埠 HTTPS 443 與 Azure 的複寫。
處理序伺服器 | 處理伺服器會透過輸出連接埠 443 接收複寫資料、將其最佳化並加密，然後傳送至 Azure 儲存體。


## <a name="performance-and-scaling"></a>效能及延展性

根據預設，您可以部署單一複寫設備，以執行組態伺服器和處理序伺服器。 如果您只複寫少數機器，此部署就已足夠。 不過，如果您是複寫，且移轉數百部機器，在單一處理序伺服器可能無法處理所有的複寫流量。 在此情況下，您可以部署額外、 向外延展處理序伺服器。

### <a name="site-recovery-deployment-planner-for-vmware"></a>適用於 VMware 的 site Recovery Deployment Planner

如果您複寫 VMware Vm，您可以使用[Site Recovery Deployment Planner](../site-recovery/site-recovery-deployment-planner.md)適用於 VMware，以協助判斷效能需求，包括每日資料變更率，以及您所需要的處理序伺服器。

### <a name="replication-appliance-capacity"></a>複寫設備容量

此表中的值可用來找出您是否需要額外的處理序伺服器部署中。

- 每日變更率 （變換率） 是否超過 2 TB，部署額外的處理序伺服器。
- 如果您複寫超過 200 部機器，部署額外的複寫設備。

**CPU** | **記憶體** | **資料快取的可用空間** | **變換率** | **複寫限制**
--- | --- | --- | --- | ---
8 個 vCPU (2 個插槽 * 4 核心 \@ 2.5GHz) | 16 GB | 300 GB | 500 GB 或更少 | < 100 部機器 
12 個 vCPU (2 個插槽 * 6 核心 \@ 2.5GHz) | 18 GB | 600 GB | 501 GB 至 1 TB | 100-150 部機器。
16 個 vCPU (2 個插槽 * 8 核心 \@ 2.5GHz) | 32 G1 |  1 TB | 1 TB 至 2 TB | 151 200 機器。

### <a name="scale-out-process-server-sizing"></a>向外延展處理序伺服器的大小

如果您要部署向外延展處理伺服器時，此表格可協助您找出伺服器的大小。

**處理序伺服器** | **資料快取的可用空間** | **變換率** | **複寫限制**
--- | --- | --- | --- 
4 個 vCPU (2 個插槽 * 2 核心 \@ 2.5 GHz)，8 GB 記憶體 | 300 GB | 250 GB 或更少 | 高達 85 的電腦 
8 個 vCPU (2 個插槽 * 4 核心 \@ 2.5 GHz)，12 GB 記憶體 | 600 GB | 251 GB 至 1 TB    | 86 150 機器。
12 個 Vcpu (2 個通訊端 * 6 核心\@2.5ghz)，24 GB 記憶體 | 1 TB | 1-2 TB | 151 225 機器。

## <a name="control-upload-throughput"></a>控制項上傳輸送量

您可以限制用來將資料上傳至 Azure 的每部 HYPER-V 主機上的頻寬總量。 請小心。 如果您將有可能會影響它的值太低，複寫和延遲移轉。


1. 登入 HYPER-V 主機或叢集節點。
2. 執行**C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc**，以開啟 Windows Azure 備份 MMC 嵌入式管理單元。
3. 在嵌入式管理單元中，選取 [變更內容]  。
4. 在 **節流**，選取**啟用網際網路頻寬使用節流來執行備份作業**。 設定工作和非工作時數的限制。 有效範圍是 512 Kbps 到 1,023 Mbps。
I

### <a name="influence-upload-efficiency"></a>影響上傳效率

如果您有備用的頻寬進行複寫，並想要增加上傳，您可以增加，如下所示的上傳工作中，已配置的執行緒數目：

1. 您可以開啟 登錄，Regedit。
2. 瀏覽至 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM 金鑰
3. 增加用於複寫的每個 VM 的資料上傳的執行緒數目的值。 預設值為 4 和最大值為 32。 

## <a name="next-steps"></a>後續步驟

試用代理程式型[VMware VM 移轉](tutorial-migrate-vmware-agent.md)使用 Azure 移轉的伺服器移轉。
