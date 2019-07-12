---
title: Azure 應用程式閘道資源健康狀態概觀
description: 這篇文章是 Azure 應用程式閘道的資源健康狀態功能的概觀
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: db29551a8150b70e797d45fe659482470c8aca2a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659498"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Azure 應用程式閘道資源健康狀態概觀

[Azure 資源健康狀態](../service-health/resource-health-overview.md)可協助您診斷並取得支援，當 Azure 問題影響您的資源。 它會通知您資源的目前及過去的健康狀態。 並提供可協助您減輕問題的技術支援。

應用程式閘道的資源健康狀態依賴閘道，就發出訊號，來評估狀況良好。 如果閘道狀況不良，資源健康狀態會分析其他資訊來判斷問題的來源。 此外，它也會識別 Microsoft 採取的動作，或您可以如何修正此問題。

如需健康狀態評估方式的其他詳細資訊，請檢閱 [Azure 資源健康狀態](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways)中的資源類型及健康狀態檢查完整清單。


應用程式閘道的健全狀況狀態會顯示為下列狀態之一：

## <a name="available"></a>可用

**可用**狀態表示服務偵測不到任何會影響資源的健康情況的事件。 您會看到**最近已解決**從未規劃的停機時間復原閘道有其中，過去 24 小時內的通知。

![可用的健康情況狀態](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>無法使用

**無法使用**狀態表示服務已偵測到的進行中平台或非平台事件會影響閘道的健康情況。

### <a name="platform-events"></a>平台事件

平台事件是由 Azure 基礎結構的多個元件觸發。 其中包括已排程的動作 (例如計劃性維護) 和非預期的事件 (例如非計劃性主機重新開機)。

資源健康狀態提供有關事件和復原程序的其他詳細資料。 即使您沒有作用中的 Microsoft 支援合約，它也可讓您連絡支援。

![無法使用的狀態](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>不明

**未知**健全狀態表示資源健康狀態未收到超過 10 分鐘閘道的相關資訊。 此狀態並非明確指示閘道的狀態。 但卻是疑難排解程序中的重要資料點。

如果閘道如預期般執行，狀態會變更為**可用**後幾分鐘的時間。

如果您遇到問題，**未知**健康狀態可能暗示平台事件會影響閘道。

![未知的狀態](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>已降級

**降級**健全狀態表示您的閘道已偵測到降低效能，雖然仍可供使用。

![Degrated 的狀態](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>後續步驟

若要深入了解疑難排解應用程式閘道 Web 應用程式防火牆 (WAF)，請參閱[疑難排解 Web 應用程式防火牆 (WAF) Azure 應用程式閘道](web-application-firewall-troubleshoot.md)。