---
title: 利用 Azure Advisor 改善應用程式的可用性 | Microsoft Docs
description: 使用 Azure 建議程式來改善 Azure 部署的高可用性。
services: advisor
documentationcenter: NA
author: kasparks
ms.author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 42627649145b568b2b25411d182e5a36cdb025b0
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2019
ms.locfileid: "58881183"
---
# <a name="improve-availability-of-your-application-with-azure-advisor"></a>利用 Azure Advisor 改善應用程式的可用性

Azure Advisor 可協助您確保和改善業務關鍵應用程式的持續性。 您可以從 [建議程式] 儀表板的 [高可用性] 索引標籤，利用建議程式取得高可用性建議。

## <a name="ensure-virtual-machine-fault-tolerance"></a>確保虛擬機器的容錯

若要為應用程式提供備援，建議您在可用性設定組中，將兩部以上的虛擬機器組成群組。 建議程式會識別不屬於可用性設定組的虛擬機器，並建議將它們移至某個可用性設定組中。 這項組態可以確保在規劃或未規劃的維護事件發生期間，至少有一部虛擬機器可以使用，且符合 Azure 虛擬機器 SLA。 您可以選擇為虛擬機器建立可用性設定組，或是將虛擬機器新增至現有的可用性設定組。

> [!NOTE]
> 如果您選擇建立可用性設定組，您必須至少再將一個虛擬機器新增至該可用性設定組。 我們建議讓兩部或多部虛擬機器組成一個可用性設定組，以確保至少有一部機器可用於中斷期間。

## <a name="ensure-availability-set-fault-tolerance"></a>確保可用性設定組的容錯

若要為應用程式提供備援，建議您在可用性設定組中，將兩部以上的虛擬機器組成群組。 Advisor 會識別包含單一虛擬機器的可用性設定組，並建議將一部或多部虛擬機器新增至該可用性設定組。 這項組態可以確保在規劃或未規劃的維護事件發生期間，至少有一部虛擬機器可以使用，且符合 Azure 虛擬機器 SLA。 您可以選擇建立虛擬機器，或是將現有的虛擬機器新增至可用性設定組。  

## <a name="use-managed-disks-to-improve-data-reliability"></a>使用受控磁碟可改善資料可靠性

可用性設定組的虛擬機器若有磁碟共用儲存體帳戶或儲存體縮放單位，就無法從中斷期間的單一儲存體縮放單位失敗中復原。 Advisor 將識別這些可用性設定組，並建議移轉至 Azure 受控磁碟。 這將確保可用性設定組中不同虛擬機器的磁碟已經過充分隔離，以避免發生單一點失敗。 

## <a name="ensure-application-gateway-fault-tolerance"></a>確保應用程式閘道的容錯

這項建議可確保由應用程式閘道提供技術支援的任務關鍵性應用程式擁有商務持續性。 Advisor 會識別未設定容錯功能的應用程式閘道執行個體，並建議可行的修復動作。 Advisor 可識別中型或大型的單一執行個體應用程式閘道，並建議再新增至少一個執行個體。 它也可識別單一或多重執行個體的小型應用程式閘道，並建議您移轉至中型或大型的 SKU。 Advisor 會建議這些動作，以確保應用程式閘道執行個體已設定為能夠符合這些資源目前的 SLA 需求。

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>防止意外刪除虛擬機器的資料

設定虛擬機器備份可確保業務關鍵資料的可用性，並防止資料意外刪除或損毀。 Advisor 會識別未啟用備份的虛擬機器，並建議啟用備份。 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>請確定您在需要時可以存取「Azure Cloud Experts」

當您執行業務關鍵工作負載時，在有需要時能諮詢支援小組至關緊要。 Advisor 可識別支援計劃中不包含支援小組的潛在業務關鍵訂用帳戶，並建議升級至包含支援小組的選項。

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>建立在 Azure 問題影響您時所要通知的 Azure 服務健康狀態警示

建議您設定當 Azure 問題影響您時所要通知的 Azure 服務健康狀態警示。 [Azure 服務健康狀態](https://azure.microsoft.com/features/service-health/)是一項免費服務，可在您受到 Azure 服務問題影響時，提供個人化的指導及支援。 Advisor 會識別沒有設定警示的訂用帳戶，並建議您建立一個警示。

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>設定流量管理員端點以供復原

如果任何指定的端點失敗，則使用多個端點的流量管理員設定檔可體驗較高的可用性。 將端點放在不同的區域能進一步改善服務可靠性。 Advisor 會識別流量管理員設定檔，其中只有一個端點，並建議在另一個區域中至少多新增一個端點。

如果流量管理員設定檔中設定為近接路由的所有端點都在相同的區域中，則其他區域中的使用者可能會發生連線延遲。 如果一個區域中的所有端點都失敗，則將端點新增或移動到另一個區域會改善整體效能，並提供更佳的可用性。 Advisor 會識別設定為近接路由 (其中所有的端點都位於相同區域) 的流量管理員設定檔。 建議將端點新增或移動到另一個 Azure 區域。

如果流量管理員設定檔設定為地理路由，則流量會根據定義的區域路由傳送至端點。 如果區域失敗，則沒有任何預先定義的容錯移轉。 若您擁有區域群組設定為「所有 (全球)」的端點，將可避免流量卸除，並改善服務可用性。 Advisor 會識別設定為地理路由 (其中沒有任何端點會設為具有「所有 (全球)」的區域群組) 的流量管理員設定檔。 建議變更組態，讓端點具有「所有 (全球)」區域群組。

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>在 Azure 儲存體帳戶上使用虛刪除來儲存及復原意外覆寫或刪除後的資料

在儲存體帳戶上啟用[虛刪除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)，可讓刪除的 Blob 轉換為虛刪除狀態，而不是永久刪除。 當資料遭到覆寫時，系統會產生虛刪除的快照集，以儲存覆寫資料的狀態。 使用虛刪除可讓您在意外刪除或覆寫時進行復原。 Advisor 會識別沒有啟用虛刪除的 Azure 儲存體帳戶，並建議您將其啟用。

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>將您的 VPN 閘道設定為主動-主動以得到連線復原能力

在主動-主動組態中，VPN 閘道的兩個執行個體將會建立到您的內部部署 VPN 裝置的 S2S VPN 通道。 當一個閘道器執行個體發生計劃性維護事件或非計劃性事件時，系統會自動將流量切換到另一個主動 IPsec 通道。 Azure Advisor 將識別未設定為主動-主動的 VPN 閘道，並建議您設定它們以獲得高可用性。

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>如何存取建議程式中的高可用性建議

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後開啟 [Advisor](https://aka.ms/azureadvisordashboard)。

2.  在 Advisor 儀表板上，按一下 [高可用性] 索引標籤。

## <a name="next-steps"></a>後續步驟

如需 Advisor 建議的詳細資訊，請參閱：
* [Azure 建議程式簡介](advisor-overview.md)
* [開始使用 Advisor](advisor-get-started.md)
* [Azure Advisor 成本建議](advisor-cost-recommendations.md)
* [建議程式效能建議](advisor-performance-recommendations.md)
* [建議程式安全性建議](advisor-security-recommendations.md)

