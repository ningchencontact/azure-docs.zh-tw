---
title: 使用 Azure Site Recovery 執行 Azure VM 災害復原演練
description: 了解如何使用 Azure Site Recovery 服務執行 Azure VM 到次要 Azure 區域的災害復原演練。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 817a220e36ac250b1d5a5aa90d0bddbfb155cc26
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091324"
---
# <a name="run-a-disaster-recovery-drill-to-a-secondary-region-for-azure-vms"></a>執行 Azure VM 到次要 Azure 區域的災害復原演練 

[Azure Site Recovery](site-recovery-overview.md) 服務藉由確保您的商務應用程式可在計劃性與非計劃性中斷期間持續啟動並執行，來提供商務持續性和災害復原 (BCDR) 策略。 Site Recovery 會管理並協調內部部署機器和 Azure 虛擬機器 (VM) 的災害復原，包括複寫、容錯移轉和復原。

本教學課程說明如何使用測試容錯移轉，執行 Azure VM 的災害復原演練 (從一個 Azure 區域到另一個區域)。 此演練會驗證不遺失資料或不停機的複寫策略，並且不會影響您的生產環境。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 檢查必要條件
> * 執行單一 VM 測試容錯移轉

> [!NOTE]
> 本教學課程可協助您透過最少的步驟執行 DR 演練；如果您想要深入了解執行 DR 演練的各個相關層面，包括網路功能考量、自動化或疑難排解，請參閱 Azure VM 的「操作說明」文件。

## <a name="prerequisites"></a>必要條件

- 在您執行測試容錯移轉之前，建議您驗證 VM 屬性並確定一切如同預期。  在 [複寫的項目]  中存取 VM 屬性。 [程式集]  刀鋒視窗會顯示機器設定與狀態的相關資訊。
- **建議您針對測試容錯移轉，使用個別的 Azure VM 網路**，而非在您啟用複寫時所設定的預設網路。
- 根據您對每個 NIC 的來源網路組態，您可以選擇性地指定 **子網路、IP 位址、公用 IP、網路安全性群組或內部 Load Balancer**，以在執行 DR 演練之前，連接到 [計算與網路] 中 測試容錯移轉設定底下的每個 NIC。


## <a name="run-a-test-failover"></a>執行測試容錯移轉

1. 在 [設定]   > [複寫的項目]  中，按一下 [VM] [+測試容錯移轉]  圖示。

2. 在 [測試容錯移轉]  中，選取要用於容錯移轉的復原點：

    - **最新**：在 Site Recovery 服務中處理所有資料，並提供最低的 RTO (復原時間目標)。
    - **最近處理**：將 VM 容錯移轉到 Site Recovery 所處理的最新復原點。 隨即顯示時間戳記。 使用此選項時，無須花費時間處理資料，因此它會提供低 RTO (復原時間目標)。
   - **最新應用程式一致**：此選項會將所有 VM 容錯移轉到最新的應用程式一致復原點。 隨即顯示時間戳記。
   - **自訂**：容錯移轉至特定復原點。 只有當您容錯移轉單一 VM，而不是使用復原方案進行容錯移轉時，才可以使用自訂。

3. 選取 Azure VM 在容錯移轉之後，次要地區所要連線的目標 Azure 虛擬網路。

    > [!NOTE]
    > 如果已針對複寫的專案預先設定測試容錯移轉設定，將看不到用來選取 Azure 虛擬網路的下拉式清單。

4. 若要開始容錯移轉，請按一下 [確定]  。 若要追蹤進度，請按一下 VM 開啟其內容。 或者，您也可以按一下保存庫名稱中的 [測試容錯移轉]  作業 > [設定]   > [作業]   > [Site Recovery 作業]  。
5. 容錯移轉完成之後，複本 Azure VM 會出現在 Azure 入口網站> [虛擬機器]  中。 請確定 VM 正在執行中、大小適中，並已連線到適當的網路。
6. 若要刪除測試容錯移轉期間建立的 VM，請按一下複寫的項目或復原計劃上的 [清除測試容錯移轉]  。 在 [記事]  中，記錄並儲存關於測試容錯移轉的任何觀察。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [執行產生容錯移轉](azure-to-azure-tutorial-failover-failback.md)
