---
title: Azure 地圖服務中的縮放層級和圖格格線 | Microsoft Docs
description: 了解 Azure 地圖服務中的縮放層級和圖格格線
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 55441cda7a6fc65ac8103d19510823a7c84a9cbf
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599920"
---
# <a name="zoom-levels-and-tile-grid"></a>縮放層級和圖格格線
Azure 地圖服務使用麥卡托圓球投影座標系統 (EPSG：3857)。

將世界分為正方形圖格。 圖像呈現方式 (光柵) 有 19 個縮放層級，編號 0 到 18。 圖像呈現方式 (向量) 有 21 個縮放層級，編號 0 到 20。 在縮放層級 0，整個世界剛好放進單一圖格裡：

![世界圖格](./media/zoom-levels-and-tile-grid/world0.png)

縮放層級 1 使用四個圖格來呈現世界：2 x 2 個正方形

![世界圖格左上](./media/zoom-levels-and-tile-grid/world1a.png)     ![世界圖格右上](./media/zoom-levels-and-tile-grid/world1c.png) 

![世界圖格左下](./media/zoom-levels-and-tile-grid/world1b.png)     ![世界圖格右下](./media/zoom-levels-and-tile-grid/world1d.png) 


每個後續的縮放層級再將前一層級的圖格一分為四，建立 2<sup>縮放</sup> x 2<sup>縮放</sup> 的格線。 縮放層級 20 是 2<sup>20</sup> x 2<sup>20</sup> 的格線，等於 1,048,576 x 1,048,576 個圖格 (總計 109,951,162,778 個圖格)。

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

圖格是由縮放層級來召集，其 x 和 y 座標對應至圖格在該縮放層級格線上的位置。

決定要使用哪個縮放層級時，請記住每個位置是在其圖格上的固定位置。 這表示顯示給定領域範圍所需的圖格數目取決於特定縮放格線中在世界上的位置。 例如，如果兩個點相距 900 公尺，「可能」只需要在縮放層級 17 用三個圖格來顯示這兩點之間的路線。 不過，如果西邊的點是在其圖格中的右邊，而東邊的點是在其圖格中的左邊，則可能需要四個圖格：

![縮放比例示範](./media/zoom-levels-and-tile-grid/zoomdemo_scaled.png) 

一旦決定縮放層級，就可以計算 x 和 y 值。 每個縮放格線的左上圖格是 x = 0、y = 0；右下角圖格是在 x = 2<sup>縮放-1</sup>、y = 2<sup>縮放-1</sup>。

以下是縮放層級 1 的縮放格線：

![縮放層級 1 的縮放格線](./media/zoom-levels-and-tile-grid/api_x_y.png)
