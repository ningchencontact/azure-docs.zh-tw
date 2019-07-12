---
title: 使用 Azure 監視器檢視計量
description: 這篇文章會示範如何監視 Azure CLI 的 Azure 入口網站的圖表與計量。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: d331dc4eb0c6668426e1ab1a01a1dd1dcebe0c85
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795808"
---
# <a name="monitor-media-services-metrics"></a>監視媒體服務度量 

[Azure 監視器](../../azure-monitor/overview.md)可讓您監視計量和診斷的記錄檔，可協助您了解如何執行您的應用程式。 詳細的說明，這個功能以及您要使用 Azure 媒體服務的計量和診斷記錄的原因，請參閱[監視媒體服務的計量和診斷記錄](media-services-metrics-diagnostic-logs.md)。

Azure 監視器提供數種方式可與計量，包括製作計量圖表在入口網站、 透過 REST API 存取它們，或是查詢這些互動使用 CLI。 這篇文章會示範如何監視 Azure CLI 的 Azure 入口網站的圖表與計量。

## <a name="prerequisites"></a>必要條件

- [建立媒體服務帳戶](create-account-cli-how-to.md)
- 檢閱[監視媒體服務的計量和診斷記錄](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>在 Azure 入口網站中檢視計量

1. 在 https://portal.azure.com 登入 Azure 入口網站。
1. 瀏覽至您的 Azure 媒體服務帳戶，然後選取**計量**。
1. 按一下 **資源**方塊，然後選取您要監視計量的資源。 

    **選取資源** 視窗隨即出現在右側清單中可用的資源給您。 在此情況下您會看到 

    * &lt;媒體服務帳戶名稱&gt;
    * &lt;媒體服務帳戶名稱&gt;/&lt;串流端點名稱&gt;
    * &lt;儲存體帳戶名稱&gt;

    選取資源，然後按**套用**。 如需支援的資源和度量的詳細資訊，請參閱[監視媒體服務度量](media-services-metrics-diagnostic-logs.md)。
 
    ![度量](media/media-services-metrics/metrics02.png)
    
    > [!NOTE]
    > 若要在您要監視計量的資源之間切換，按一下**資源**方塊一次，並重複此步驟。
1. （選擇性） 指定圖表的名稱 （編輯按頂端的鉛筆名稱）。
1. 新增您想要檢視的計量。

    ![度量](media/media-services-metrics/metrics03.png)
1. 您可以將圖表釘選在儀表板。

## <a name="view-metrics-with-azure-cli"></a>使用 Azure CLI 檢視計量

若要使用 CLI 的 「 輸出 」 計量，您可以執行下列`az monitor metrics`CLI 命令：

```cli
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

若要取得其他計量，替代成您感興趣的計量名稱的 「 輸出 」。

## <a name="see-also"></a>另請參閱

* [Azure 監視器計量](../../azure-monitor/platform/data-platform.md)
* [建立、 檢視及管理使用 Azure 監視器計量警示](../../azure-monitor/platform/alerts-metric.md)。

## <a name="next-steps"></a>後續步驟

[診斷記錄](media-services-diagnostic-logs-howto.md)
