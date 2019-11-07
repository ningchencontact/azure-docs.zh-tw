---
title: 瞭解 Azure 春季雲端的計量
description: 瞭解如何在 Azure 春季雲端中審查計量
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 952dfc1c707df3b7fa61443ea6ea18630352f0dc
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607817"
---
# <a name="metrics-for-azure-spring-cloud"></a>Azure 春季雲端的計量

Azure 監視器計量瀏覽器是 Microsoft Azure 入口網站的元件，可讓您繪製圖表、以視覺化方式相互關聯趨勢，以及調查計量中的尖峰和下降。 使用計量瀏覽器來調查資源的健康情況和使用量。 在 Azure 春季雲端中，我們提供兩個選項來查看計量： [**應用程式總覽**] 頁面和 [服務等級度量] 頁面中的圖表。

## <a name="application-overview-page"></a>應用程式總覽頁面

每個應用程式的 **[應用程式總覽**] 頁面都會顯示計量圖表，讓您能夠執行應用程式的快速狀態檢查。  移至您的 Azure 春季雲端服務頁面，並選取 [**應用程式儀表板**]，然後從清單中選取應用程式。  

我們會針對下列各項提供5個圖表，每分鐘更新一次計量：

* **Http 伺服器錯誤**：對您的應用程式發出的 HTTP 要求的錯誤計數。
* **中的資料**：您的應用程式所接收的位元組。
* **資料輸出**：傳送至您應用程式的位元組。
* **要求**：您的應用程式收到的要求。
* **平均回應時間**：來自您應用程式的平均回應時間。

您可以選取介於1小時到7天之間的圖表時間範圍。

## <a name="service-level-metric-queries"></a>服務層級度量查詢

Azure 春季雲端可讓您監視各種應用程式計量。 請參閱[開始](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)使用 Azure 監視器計量的指南，以深入瞭解此服務。

若要查看計量資料，您將選取您的度量、**匯總**和時間範圍。  以下將說明這些概念。

### <a name="aggregation"></a>彙總 

Azure 會每分鐘輪詢並更新計量。 Azure 提供三種方式來匯總所選時間週期內的資料：

* **總計**：將所有計量加總為目標輸出。
* **平均**：使用句點中的平均值作為目標輸出。
* **最大/最小**值：使用句點作為目標輸出的最大/最小值。

### <a name="time-range"></a>時間範圍

選取預設時間範圍或自行定義。

### <a name="modifying-the-granularity-of-your-metric-query"></a>修改度量查詢的資料細微性

根據預設，Azure 會匯總所有 Azure 春季雲端服務應用程式的計量。 若要查看應用程式或實例層級的計量，請使用 filter 函數。  
選取 [**新增篩選**]，將屬性設定為 [**應用**程式]，然後選取您要監視的目標應用程式。 （選擇性）使用 [套用**分割**] 選項，針對圖表中的每個應用程式繪製個別的線條。

>[!TIP]
> 您可以在 [計量] 頁面中建立自己的圖表，並將其釘選到**儀表板**。 一開始先命名您的圖表。  接下來，選取**右上角的 [釘選到儀表板**]。 您現在可以在入口網站**儀表板**上檢查您的應用程式。