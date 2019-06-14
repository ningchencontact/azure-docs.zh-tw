---
title: Azure 的 VMware 解決方案由 CloudSimple-CloudSimple 維護和更新
description: 描述排定的維護和更新的 CloudSimple 服務程序
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4dde358f10e9ac5054297ff68a0971404c0dc135
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65160241"
---
# <a name="cloudsimple-maintenance-and-updates"></a>CloudSimple 維護和更新

私用雲端環境被設計來取得任何單一失敗點：

* ESXi 叢集依設定 vSphere 的高可用性。 叢集調整大小必須至少一個備用的節點，提供恢復功能。
* 需要至少三個節點，以提供保護，防止單一失敗的 vSAN 會提供備援的主要儲存體。 vSAN 可以提供更高的恢復功能的較大的叢集設定。
* vCenter、 PSC 和 NSX Manager Vm 都已設定 RAID 10 存放裝置原則，以防止儲存體失敗。 Vm 會受到 vSphere HA 節點/網路故障。
* ESXi 主機有風扇和 Nic。
* TOR 和背面的參數會設定 HA 成對提供恢復功能。

CloudSimple 持續監視下列 Vm 執行時間和可用性，並提供可用性 Sla:

* ESXi 主機
* vCenter
* PSC
* NSX 管理員

CloudSimple 也會監視下列持續失敗：

* 硬碟
* 實體 NIC 連接埠
* 伺服器
* 風扇
* 乘冪
* 交換器
* 交換器連接埠

如果磁碟或節點失敗，新的節點會自動加入至受影響的 VMware 叢集，以立即將它回復到健全狀況。

CloudSimple 備份、 維護及更新這些私用雲端中的 VMware 項目：

* ESXi
* vCenter 平台服務
* Controller
* vSAN
* NSX

## <a name="back-up-and-restore"></a>備份和還原

CloudSimple 備份包括：

* 每晚的增量備份 vCenter PSC 及 DVS 的規則。
* 使用 vCenter 來備份應用程式層元件的原生 Api。
* 之前的任何更新或升級的 VMware 管理軟體的自動備份。
* 在 vCenter 中，才能透過 tls 1.2 加密通道傳輸到 Azure 的資料來源，資料加密。 資料會儲存在 Azure blob 中，跨區域複寫。

您可以開啟以要求在還原[支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="maintenance"></a>維護

CloudSimple 會執行數種類型的已規劃的維護。

### <a name="backendinternal-maintenance"></a>後端/內部維護

此次維護作業通常需要重新設定實體的資產，或安裝軟體的修補程式。 它不會影響資產提供服務的一般性的取用。 具有備援的 Nic，移至每個實體的機架，一般的網路流量和私用雲端作業不會受到影響。 只有當您的組織需要維護間隔期間使用的完整備援的頻寬，您可能會注意到對效能產生影響。

### <a name="cloudsimple-portal-maintenance"></a>CloudSimple 入口網站的維護

更新 CloudSimple 控制平面或基礎結構時，需要一些有限的服務停機時間。 目前，維護間隔可以是每月一次為頻繁。 頻率必須拒絕一段時間。 CloudSimple 提供入口網站的維護的通知，並讓儘可能縮短間隔。 在入口網站的維護的間隔內，下列服務繼續運作而造成的影響：

* VMware 管理平面和應用程式
* vCenter 存取
* 所有的網路和儲存體
* Azure 的所有流量

### <a name="vmware-infrastructure-maintenance"></a>VMware 基礎結構維護

偶爾也會需要 VMware 基礎結構的組態進行變更。  目前，這些間隔可能會發生每隔 1 到 2 月，但是頻率應該拒絕一段時間。 這種類型的維護通常可以完成而不會中斷正常 CloudSimple 服務耗用量。 在 VMware 維護間隔內，下列服務繼續運作而造成的影響：

* VMware 管理平面和應用程式
* vCenter 存取
* 所有的網路和儲存體
* Azure 的所有流量

## <a name="updates-and-upgrades"></a>更新和升級

CloudSimple 負責生命週期管理的 VMware （ESXi、 vCenter、 PSC 和 NSX） 軟體的私人雲端。

軟體更新包括：

* **修補程式**。 安全性修補程式或 VMware 所發行的 bug 修正。
* **更新**。 次要版本的 VMware 堆疊元件的變更。
* **升級**。 主要版本的 VMware 堆疊元件的變更。

CloudSimple 可用從 VMware 時，測試重大安全性修補程式。 每個 SLA，CloudSimple 推出的安全性修補程式到私用雲端環境一週內。

CloudSimple 提供 VMware 軟體元件的每季的維護更新。 VMware 軟體的新主要版本可用時，CloudSimple 的運作方式與客戶合作，協調適當的維護期間升級。

## <a name="next-steps"></a>後續步驟

[工作負載 Vm 使用 Veeam 備份](https://docs.azure.cloudsimple.com/backup-workloads-veeam/)。