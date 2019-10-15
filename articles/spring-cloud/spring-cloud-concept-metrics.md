---
title: 了解 Azure Spring Cloud 的計量
description: 了解如何在 Azure Spring Cloud 中檢閱計量
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: d9c6f14b7ecfc2929dc48c11e0df1fe80303c8b1
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038497"
---
# <a name="metrics-for-azure-spring-cloud"></a>Azure Spring Cloud 的計量

Azure 監視器計量瀏覽器是 Microsoft Azure 入口網站的一個元件，可用來繪製圖表、以視覺方式串連趨勢，以及調查計量中的突增與突降。 使用計量瀏覽器來調查資源的健康情況和使用量。 在 Azure Spring Cloud 中，我們提供兩個選項來檢視計量：[應用程式概觀]  頁面和服務層級 [計量] 頁面中的圖表。

## <a name="application-overview-page"></a>應用程式概觀頁面

每個應用程式的 [應用程式概觀]  頁面都會呈現計量圖表，可讓您執行應用程式狀態快速檢查。  移至您的 Azure Spring Cloud 服務頁面，並選取 [應用程式儀表板]  ，然後從清單中選取應用程式。  

我們針對下列計量 (每分鐘更新) 提供 5 個圖表：

* **Http 伺服器錯誤**：對您應用程式發出 HTTP 要求的錯誤計數。
* **資料輸入**：您應用程式接收的位元組。
* **資料輸出**：傳送到您應用程式的位元組。
* **要求**：您應用程式接收的要求。
* **平均回應時間**：來自您應用程式的平均回應時間。

您可以選取圖表的時間範圍，介於 1 小時到 7 天之間。

## <a name="service-level-metric-queries"></a>服務層級計量查詢

Azure Spring Cloud 可讓您監視各種應用程式計量。 若要深入了解此服務，請檢閱使用 Azure 監視器計量的[使用者入門指南](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) \(部分機器翻譯\)。

若要檢閱計量資料，您將會選取計量、**彙總**和時間範圍。  以下說明這些概念。

### <a name="aggregation"></a>彙總 

Azure 會每分鐘輪詢並更新計量。 Azure 提供三種方式來彙總所選時間週期的資料：

* **Total**：將所有計量加總作為目標輸出。
* **Average**：使用週期內的平均值作為目標輸出。
* **最大值/最小值**：使用週期內的最大值/最小值作為目標輸出。

### <a name="time-range"></a>時間範圍

選取預設時間範圍或自行定義。

### <a name="modifying-the-granularity-of-your-metric-query"></a>修改計量查詢的細微性

根據預設，Azure 會彙總所有 Azure Spring Cloud 服務應用程式的計量。 若要檢閱應用程式或執行個體層級的計量，請使用篩選功能。  
選取 [加入篩選]  ，將屬性設定為 [應用程式]  ，然後選取您要監視的目標應用程式。 您也可以選擇性地使用 [套用分割]  選項，在圖表中針對每個應用程式繪製個別的線。

>[!TIP]
> 您可以在計量頁面中建置自己的圖表，並釘選到您的 [儀表板]  。 從命名圖表開始。  接下來，選取右上角的 [釘選至儀表板]  。 您現在可以在入口網站 [儀表板]  檢查應用程式。