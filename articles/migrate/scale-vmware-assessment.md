---
title: 評定大量 VMware Vm 移轉至 Azure，Azure Migrate |Microsoft Docs
description: 描述如何評定要移轉至 Azure 中使用 Azure Migrate 服務大量的 VMware Vm。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: 6102a1c59be3627b95dc1e0cb1d1d712d5832d2f
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811332"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>評定要移轉至 Azure 的大量的 VMware Vm


這篇文章說明如何評定大量 (1000年 35,000) 的內部部署 VMware Vm 移轉至 Azure，使用 Azure Migrate Server 評量工具

[Azure Migrate](migrate-services-overview.md)提供工具，可協助您探索、 評估及移轉至 Microsoft Azure 的應用程式、 基礎結構，以及工作負載的中樞。 中樞還包含 Azure Migrate 的工具和第三方獨立軟體廠商 (ISV) 供應項目。 

在本文中，您將了解：
> [!div class="checklist"]
> * 規劃大規模評估。
> * 設定 Azure 的權限，以及準備 VMware 評估。
> * 建立 Azure Migrate 專案，並建立評估。
> * 當您在規劃移轉，請檢閱評估。


> [!NOTE]
> 如果您想要試用的概念之前評估大規模評估數個 Vm，請依照下列我們[教學課程系列](tutorial-prepare-vmware.md)

## <a name="plan-for-assessment"></a>評估計劃

在規劃大量的 VMware Vm 的評估時，有幾個事項来考慮：

- **規劃 Azure Migrate 專案**:了解如何部署 Azure Migrate 專案。 例如，如果您的資料中心位於不同的地理位置，或您要將探索、 評定或移轉相關的中繼資料儲存在不同的地理位置，您可能需要多個專案。 
- **規劃應用裝置**:Azure Migrate 會使用內部 Azure Migrate 設備，部署為 VMware VM，來持續探索 Vm。 設備監視環境的變更，例如新增 Vm、 磁碟或網路介面卡。 它也會其相關的中繼資料和效能資料傳送至 Azure。 您需要找出您要部署多少設備。
- **計劃進行探索的帳戶**:Azure Migrate 應用裝置會使用到 vCenter Server 的存取帳戶，以便探索評定及移轉的 Vm。 如果您要探索超過 10,000 個 Vm，設定多個帳戶。


## <a name="planning-limits"></a>計劃限制
 
使用規劃本表中摘要說明的限制。

**規劃** | **限制**
--- | --- 
**Azure Migrate 專案** | 評估最多 35000 專案中的 Vm。
**Azure Migrate 應用裝置** | 應用裝置只能連接到單一的 vCenter Server。<br/><br/> 只能與單一 Azure Migrate 專案相關聯的應用裝置。<br/> 設備可以探索 vCenter Server 上的最多 10,000 個 Vm。
**Azure Migrate 評估** | 您可以評估在單一評估中的最多 35000 Vm。

記住這些限制，以下是某些範例部署：


**vCenter Server** | **在伺服器上的 Vm** | **建議** | **動作**
---|---|---
一個 | < 10,000 | 一個 Azure Migrate 專案。<br/> 一個應用裝置。<br/> 一個用於探索的 vCenter 帳戶。 | 設定設備，連線到 vCenter Server 的帳戶。
一個 | > 10,000 | 一個 Azure Migrate 專案。<br/> 多個應用裝置。<br/> 多個 vCenter 帳戶。 | 為每 10,000 的 Vm 設定設備。<br/><br/> 設定 vCenter 帳戶，並將庫存，以限制存取帳戶至小於 10000 的 Vm。<br/> 連線到 vCenter server 的帳戶的每個設備。<br/> 您可以使用不同的應用裝置探索到的電腦上分析相依性。
多個 | < 10,000 |  一個 Azure Migrate 專案。<br/> 多個應用裝置。<br/> 一個用於探索的 vCenter 帳戶。 | 設定裝置，連線到 vCenter Server 的帳戶。<br/> 您可以使用不同的應用裝置探索到的電腦上分析相依性。
多個 | > 10,000 | 一個 Azure Migrate 專案。<br/> 多個應用裝置。<br/> 多個 vCenter 帳戶。 | 如果 vCenter 伺服器探索 < 10000 的 Vm，設定每個 vcenter Server 的應用裝置。<br/><br/> 如果探索 vCenter Server > 10,000 Vm 設定設備每 10,000 的 Vm。<br/> 設定 vCenter 帳戶，並將庫存，以限制存取帳戶至小於 10000 的 Vm。<br/> 連線到 vCenter server 的帳戶的每個設備。<br/> 您可以使用不同的應用裝置探索到的電腦上分析相依性。


## <a name="plan-discovery-in-a-multi-tenant-environment"></a>在多租用戶環境中的計劃探索

如果您打算在多租用戶環境，您可以將範圍探索 vCenter Server 上。

- 您可以將應用裝置探索範圍設定 vCenter 伺服器的資料中心、 叢集或叢集，主機或主機或個別 Vm 資料夾的資料夾。
- 如果您的環境租用戶之間共用，而且您不想要個別探索每個租用戶，您可以範圍設備用來探索 vCenter 帳戶存取。 
    - 如果租用戶共用的主機，建立具有唯讀存取認證，屬於特定的租用戶的 vm。 
    - 使用 Azure Migrate 設備探索這些認證。
    - 如果 vCenter 帳戶具有 vCenter VM 資料夾層級授與存取權，azure Migrate 評估無法探索 Vm。 支援的主機和叢集的資料夾。 

## <a name="prepare-for-assessment"></a>準備評估

準備 Azure 和 VMware server 評定。 

1. 請確認[VMware 支援需求和限制](migrate-support-matrix-vmware.md)。
2. 設定您的 Azure 帳戶與 Azure Migrate 互動的權限。
3. 準備 VMware 評估。


請依照下列中的指示[本教學課程](tutorial-prepare-vmware.md)進行這些設定。


## <a name="create-a-project"></a>建立專案

根據您計劃的需求，執行下列作業：

1. 建立 Azure Migrate 專案。
2. 將 Azure Migrate Server 評量工具新增至專案。

[深入了解](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>建立及檢閱評量

1. 建立評定 VMware Vm。
1. 檢閱 評量，以準備進行移轉規劃。


請依照下列中的指示[本教學課程](tutorial-assess-vmware.md)進行這些設定。
    

## <a name="next-steps"></a>後續步驟

在本文章中，您將：
 
> [!div class="checklist"] 
> * 調整適用於 VMware Vm 的 Azure Migrate 評估計劃
> * 備妥 Azure 和評定 VMware
> * 建立 Azure Migrate 專案並執行評量
> * 檢閱 評量，以準備移轉。

現在，請[了解如何](concepts-assessment-calculation.md)評量計算，以及如何[修改評估](how-to-modify-assessment.md)。
