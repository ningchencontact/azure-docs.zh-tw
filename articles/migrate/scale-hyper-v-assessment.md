---
title: 評定要移轉至 Azure，Azure Migrate 的大量的 HYPER-V Vm |Microsoft Docs
description: 描述如何評定要移轉至 Azure 中使用 Azure Migrate 服務大量的 HYPER-V Vm。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: 95704f2694892b349d0967fca2160dabd990b472
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811540"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>評定要移轉至 Azure 的大量的 HYPER-V Vm

本文說明如何評估的數目很大 (> 1000) 移轉至 Azure，使用 Azure Migrate Server 評量工具的內部部署 HYPER-V Vm。

[Azure Migrate](migrate-services-overview.md)提供工具，可協助您探索、 評估及移轉至 Microsoft Azure 的應用程式、 基礎結構，以及工作負載的中樞。 中樞還包含 Azure Migrate 的工具和第三方獨立軟體廠商 (ISV) 供應項目。 


在本文中，您將了解：
> [!div class="checklist"]
> * 規劃大規模評估。
> * 設定 Azure 的權限，並準備 HYPER-V 評估。
> * 建立 Azure Migrate 專案，並建立評估。
> * 當您在規劃移轉，請檢閱評估。


> [!NOTE]
> 如果您想要試用的概念之前評估大規模評估數個 Vm，請依照下列我們[教學課程系列](tutorial-prepare-hyper-v.md)

## <a name="plan-for-assessment"></a>評估計劃

在規劃大量的 HYPER-V Vm 的評估時，有幾個事項来考慮：

- **規劃 Azure Migrate 專案**:了解如何部署 Azure Migrate 專案。 例如，如果您的資料中心位於不同的地理位置，或您要將探索、 評定或移轉相關的中繼資料儲存在不同的地理位置，您可能需要多個專案。
- **規劃應用裝置**:Azure Migrate 會持續探索評定及移轉的 Vm 使用的內部部署 Azure Migrate 應用裝置，部署為 HYPER-V VM。 設備監視環境的變更，例如新增 Vm、 磁碟或網路介面卡。 它也會其相關的中繼資料和效能資料傳送至 Azure。 您需要找出要部署多少設備。


## <a name="planning-limits"></a>計劃限制
 
使用規劃本表中摘要說明的限制。

**規劃** | **限制**
--- | --- 
**Azure Migrate 專案** | 評估的專案中的最多 10,000 個 Vm。
**Azure Migrate 應用裝置** | 設備可以探索最多 5000 的 Vm。<br/> 應用裝置可以連線到達 300 部 HYPER-V 主機。<br/> 只能與單一 Azure Migrate 專案相關聯的應用裝置。<br/><br/> 
**Azure Migrate 評估** | 您可以評估在單一評估中的最多 10,000 個 Vm。



## <a name="other-planning-considerations"></a>其他的規劃考量

- 若要從設備開始探索，您必須選取每個 Hyper-v 主機。 
- 如果您執行的多租用戶環境，您目前無法探索屬於特定的租用戶的 Vm。 

## <a name="prepare-for-assessment"></a>準備評估

準備 Azure 和 HYPER-V server 評定。 

1. 請確認[HYPER-V 支援需求和限制](migrate-support-matrix-hyper-v.md)。
2. 設定您的 Azure 帳戶與 Azure Migrate 互動的權限
3. 準備 HYPER-V 主機和 Vm

請依照下列中的指示[本教學課程](tutorial-prepare-hyper-v.md)進行這些設定。

## <a name="create-a-project"></a>建立專案

根據您計劃的需求，執行下列作業：

1. 建立 Azure Migrate 專案。
2. 將 Azure Migrate Server 評量工具新增至專案。

[深入了解](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>建立及檢閱評量

1. 建立 HYPER-V Vm 的評量。
1. 檢閱 評量，以準備進行移轉規劃。

[了解更多](tutorial-assess-hyper-v.md)有關建立及檢閱評量。
    

## <a name="next-steps"></a>後續步驟

在本文章中，您將：
 
> [!div class="checklist"] 
> * 計劃要調整 HYPER-V vm 的 Azure Migrate 評估
> * 備妥 Azure 和 HYPER-V 進行評量
> * 建立 Azure Migrate 專案並執行評量
> * 檢閱 評量，以準備移轉。

現在，請[了解如何](concepts-assessment-calculation.md)評量計算，以及如何[修改評量](how-to-modify-assessment.md)
