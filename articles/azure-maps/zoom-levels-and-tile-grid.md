---
title: Azure 地圖服務中的縮放層級和圖格格線 | Microsoft Docs
description: 了解 Azure 地圖服務中的縮放層級和圖格格線
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: e7dcdb960fbd9196aca8b667269a4c6e5a1fb8f9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60795056"
---
# <a name="zoom-levels-and-tile-grid"></a>縮放層級和圖格格線
Azure 地圖服務會使用麥卡投影座標系統 (EPSG:3857).

將世界分為正方形圖格。 Azure 地圖服務提供 23 的縮放層級，編號 0 到 22 點陣和向量圖格。 在縮放層級 0，整個世界剛好放進單一圖格裡：

![世界圖格](./media/zoom-levels-and-tile-grid/world0.png)

縮放層級 1 使用四個圖格來呈現世界：2 x 2 個正方形

![世界圖格左上](media/zoom-levels-and-tile-grid/world1a.png)     ![世界圖格右上](media/zoom-levels-and-tile-grid/world1c.png) 

![世界圖格左下](media/zoom-levels-and-tile-grid/world1b.png)     ![世界圖格右下](media/zoom-levels-and-tile-grid/world1d.png) 

每個額外的縮放層級四組數字除以磚的前一個位置，建立 2 個方格<sup>縮放</sup>x 2<sup>縮放</sup>。 縮放層級 22 是 格線 2<sup>22</sup> x 2<sup>22</sup>，或 4,194,304 x 4,194,304 個圖格 (總計 17,592,186,044,416 個圖格)。

Azure 地圖服務的互動式地圖控制項，適用於 web 和 Android 支援縮放層級 25 縮放層級，編號 0 到 24。 雖然道路資料將僅位於圖格時可用的縮放層級。

下表提供完整的縮放層級清單值：

|縮放層級|公尺/像素|公尺/圖格邊|
|--- |--- |--- |
|0|156543|40075008|
|1|78271.5|20037504|
|2|39135.8|10018764.8|
|3|19567.9|5009382.4|
|4|9783.9|2504678.4|
|5|4892|1252352|
|6|2446|626176|
|7|1223|313088|
|8|611.5|156544|
|9|305.7|78259.2|
|10|152.9|39142.4|
|11|76.4|19558.4|
|12|38.2|9779.2|
|13|19.1|4889.6|
|14|9.6|2457.6|
|15|4.8|1228.8|
|16|2.4|614.4|
|17|1.2|307.2|
|18|0.6|152.8|
|19|0.3|76.4|
|20|0.15|38.2|
|21|0.075|19.1|
|22|0.0375|9.55|
|23|0.01875|4.775|
|24|0.009375|2.3875|

圖格是由縮放層級來召集，其 x 和 y 座標對應至圖格在該縮放層級格線上的位置。

在決定要使用的縮放層級時，請記住每個位置是在其圖格上的固定位置。 這表示顯示給定領域範圍所需的圖格數目取決於特定縮放格線中在世界上的位置。 例如，如果兩個點相距 900 公尺，「可能」  只需要在縮放層級 17 用三個圖格來顯示這兩點之間的路線。 不過，如果西邊的點是在其圖格中的右邊，而東邊的點是在其圖格中的左邊，則可能需要四個圖格：

![縮放比例示範](media/zoom-levels-and-tile-grid/zoomdemo_scaled.png) 

一旦決定縮放層級，就可以計算 x 和 y 值。 每個縮放格線的左上方 圖格是 x = 0，y = 0;右下角圖格是在 x = 2<sup>縮放-1</sup>，y = 2<sup>縮放-1</sup>。

以下是縮放層級 1 的縮放格線：

![縮放層級 1 的縮放格線](media/zoom-levels-and-tile-grid/api_x_y.png)
