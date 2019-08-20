---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/18/2019
ms.author: cephalin
ms.openlocfilehash: 0691b1a531ffebbb2c368bdb37dd4d8025fb4a4e
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2019
ms.locfileid: "69623681"
---
當您複製其他部署位置的組態時，可以編輯複製的組態。 某些設定元素會遵循交換的內容 (非位置特定), 而其他設定元素會保留在交換之後的相同位置 (位置特定)。 以下清單顯示當您交換位置時會變更的設定。

**交換的設定**：

* 一般設定, 例如 framework 版本、32/64 位、web 通訊端
* 應用程式設定 (可以設定為停在某一個位置)
* 連接字串 (可以設定為停在某一個位置)
* 處理常式對應
* 監視與診斷設定
* 公開憑證
* WebJobs 內容
* 混合式連接 *
* 虛擬網路整合 *
* 服務端點 *
* Azure 內容傳遞網路 *

以星號 (*) 標示的功能計畫會在位置上出現。 

**無法交換的設定**：

* 正在發行端點
* 自訂網域名稱
* 私人憑證與 SSL 繫結
* 調整設定
* WebJobs 排程器
* IP 限制
* Always On
* 通訊協定設定 (HTTPS、TLS 版本、用戶端憑證)
* 診斷記錄設定
* 跨原始來源資源分享 (CORS)

<!-- VNET and hybrid connections not yet sticky to slot -->