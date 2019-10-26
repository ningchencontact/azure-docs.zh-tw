---
title: 即將在客戶擁有的網站之間，使用 Hyper-v 和從 SCVMM 管理的網站之間的 DR 淘汰到 Azure |Microsoft Docs
description: 有關使用 Hyper-v 在客戶擁有的網站與從 SCVMM 管理的網站之間的 DR 即將淘汰的詳細資料，以及其他選項
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 09/13/2019
ms.author: rajanaki
ms.openlocfilehash: 972223815810917684f35c4e99f04e1ab5b882c8
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952117"
---
# <a name="upcoming-deprecation-of-dr-between-customer-owned-sites-using-hyper-v-and-between-sites-managed-by-scvmm-to-azure"></a>即將在客戶擁有的網站之間，使用 Hyper-v 和從 SCVMM 管理的網站之間的 DR 淘汰到 Azure

本文說明即將推出的淘汰計畫、對應的含意，以及針對 Azure Site Recovery 所支援的下列案例提供給客戶的替代選項。 

- 由 SCVMM 在客戶擁有的網站之間管理的 Hyper-v Vm 之間的 DR 
- 由 SCVMM 管理的 Hyper-v Vm 至 Azure 的 DR

> [!IMPORTANT]
> 這些案例目前已標示為即將淘汰，而客戶預期會儘早採取補救步驟，以避免對其環境造成任何中斷。 
 

## <a name="what-changes-should-you-expect"></a>您預期會有哪些變更？

- 從2019年11月開始，這些案例不會允許 boardings 的新使用者。 **現有的複寫和管理**作業，包括容錯移轉、測試容錯移轉、監視等，**將不會受到影響**。

- 案例一旦淘汰，除非客戶遵循建議步驟，否則會有下列含意。

    - 由 SCVMM 在客戶擁有的網站之間管理的 Hyper-v Vm 之間的 DR：複寫會繼續運作，因為 Hyper-v 複本的基礎功能仍可繼續運作，但客戶將無法查看、管理或執行 ny DR 相關的作業透過 Azure 入口網站中的 Azure Sire 復原體驗。 
    - 從 SCVMM 管理的 Hyper-v Vm 至 Azure 的 DR：現有的複寫將會中斷，而且客戶無法透過 Azure Site Recovery 來查看、管理或執行與 DR 相關的作業


## <a name="recommended-actions-to-be-taken"></a>建議採取的動作

以下是客戶必須確保在案例淘汰之後，不會影響其 DR 策略的替代選項。 

- 選擇[開始使用 Azure 做為 hyper-v 主機上 vm 的 DR 目標](hyper-v-azure-tutorial.md)。

> [!IMPORTANT]
> 請注意，您的內部部署環境仍然可以有 SCVMMM，但您會將僅限 Hyper-v 主機的參考設定為 ASR。

- 選擇繼續進行站對站複寫，但使用基礎[Hyper-v 複本解決方案](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica)，但將無法使用 Azure 入口網站中的 Azure Site Recovery 來管理 DR 設定。 


## <a name="next-steps"></a>後續步驟
規劃淘汰，並選擇最適合您的基礎結構和業務的替代選項。 如果您有任何關於此情況的查詢，請聯繫 Microsoft 支援服務

