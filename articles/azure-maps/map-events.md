---
title: 使用 Azure 地圖服務處理事件 |Microsoft Docs
description: 如何建立互動式 Web SDK 地圖與地圖事件
author: jingjing-z
ms.author: jinzh
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c5b4ed73d7bc4d89a67280a0bb183f374ae093d8
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899438"
---
# <a name="interact-with-the-map"></a>與地圖互動

本文說明如何使用 [[對應類別事件](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events)] 屬性來反白顯示地圖上的事件和地圖的不同圖層。 此外，也會示範如何使用地圖類別事件屬性來醒目提示您與 HTML 標記互動時的事件。

## <a name="interact-with-the-map"></a>與地圖互動

請使用下面的地圖來播放，並查看右邊反白顯示的對應滑鼠事件。 您可以按一下 [JS] 索引標籤來檢視和編輯 JavaScript 程式碼。 您也可以按一下 [在 CodePen 上編輯] 按鈕，並在 CodePen 上編輯程式碼。

<br/>

<iframe height='600' scrolling='no' title='與地圖互動 – 滑鼠事件' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>與地圖互動 – 滑鼠事件</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="interact-with-map-layers"></a>與地圖圖層互動

下列程式碼會反白顯示當您與符號層互動時所引發的事件名稱。 符號、反升、線條和多邊形圖層都支援相同的事件集。 熱度圖和磚圖層不支援任何這些事件。

<br/>

<iframe height='600' scrolling='no' title='與地圖互動 – 地圖事件' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>與地圖互動 – 圖層事件</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="interact-with-html-marker"></a>與 HTML 標記互動

下列程式碼會將 JAVAscript 地圖事件新增至 HTML 標籤。 它也會醒目提示當您與 HTML 標記互動時所引發的事件名稱。

<br/>

<iframe height='500' scrolling='no' title='與地圖互動 – HTML 標記事件' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>與地圖互動 – HTML 標記事件</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

下表列出所有支援的對應類別事件。

| Event - 事件             | 描述 |
|-------------------|-------------|
| boxzoomend        | 當「框縮放」互動結束時引發。|
| boxzoomstart      | 在「方塊縮放」互動開始時引發。|
| 然後             | 在地圖上的相同點按下並放開指標裝置時引發。|
| 關閉             | 在以手動方式或以程式設計方式關閉快顯視窗時引發。|
| coNtextmenu       | 按一下滑鼠右鍵時引發。|
| dataadded         | 當圖形加入至資料來源時引發。|
| dataremoved       | 當圖形從資料來源中移除時引發。|
| datasourceupdated | 在更新 DataSource 物件時引發。|
| dblclick          | 當指標裝置在地圖上的相同點按兩次時引發。|
| 拖放式              | 在地圖、快顯或 HTML 標籤的「拖曳至平移」互動期間重複引發。|
| dragend           | 當地圖、快顯或 HTML 標籤上的「拖曳至移動流覽」互動結束時引發。|
| dragstart         | 當地圖、快顯或 HTML 標籤上有「拖曳至移動流覽」互動時引發。|
| error             | 發生錯誤時引發。|
| keydown           | 當按下按鍵時引發。|
| 擊          | 在產生 typable 字元（ANSI 索引鍵）的索引鍵被按下時引發。|
| keyup             | 在釋放按鍵時引發。|
| layeradded        | 在圖層新增至地圖時引發。|
| 載入              | 在下載所有必要的資源後立即引發，並已完成對應的第一個視覺化呈現。|
| mousedown         | 在地圖內按下指標裝置時引發。|
| mousemove         | 當指標裝置在地圖內移動時引發。|
| mouseout          | 當點裝置離開地圖的畫布時引發。|
| mouseover         | 當指標裝置在地圖內移動時引發。|
| mouseup           | 在地圖中釋放指標裝置時引發。|
| 移動              | 在動畫轉換期間重複引發，做為使用者互動或方法的結果。|
| moveend           | 在對應完成從一個 view 至另一個視圖的轉換後引發，做為使用者互動或方法的結果。|
| movestart         | 在對應開始從一個 view 轉換到另一個視圖之前就引發，做為使用者互動或方法的結果。|
| 開啟              | 在以手動方式或以程式設計方式開啟快顯視窗時引發。|
| 介紹             | 每當地圖的音調（傾斜）變更為使用者互動或方法的結果時，就會引發。|
| pitchend          | 在地圖的螺距（傾斜）結束之後，立即引發，做為使用者互動或方法的結果。|
| pitchstart        | 每當地圖的音調（傾斜）開始變更為使用者互動或方法的結果時，就會引發。|
| 已就緒             | 當對應已準備好要以程式設計方式與互動時，載入最小的必要對應資源時引發。|
| render            | <p> 當地圖繪製到螢幕時引發，做為的結果：<ul><li>地圖的位置、縮放、音調或軸承的變更。</li><li>地圖樣式的變更。</li><li>資料來源來源的變更。</li><li>向量磚、GeoJSON 檔、圖像或 sprite 的載入。</li></ul></p>|
| 調整            | 在地圖已調整大小之後立即引發。|
| 向            | 在「拖曳至旋轉」互動期間重複引發。|
| rotateend         | 當「拖曳至旋轉」互動結束時引發。|
| rotatestart       | 在「拖曳至旋轉」互動開始時引發。|
| shapechanged      | 當繪圖物件屬性變更時引發。|
| sourceadded       | 當資料來源或 VectorTileSource 加入至對應時引發。|
| sourceremoved     | 從對應中移除資料來源或 VectorTileSource 時引發。|
| styledata         | 對應的樣式載入或變更時引發。|
| tokenacquired     | 取得 AAD 存取權杖時引發。|
| touchcancel       | 在對應中發生 touchcancel 事件時引發。|
| touchend          | 在對應中發生 touchend 事件時引發。|
| system.windows.uielement.touchmove>         | 在對應中發生 system.windows.uielement.touchmove> 事件時引發。|
| touchstart        | 在對應中發生 touchstart 事件時引發。|
| wheel             | 在對應中發生滑鼠滾輪事件時引發。|
| 貼近              | 在從一個縮放層級動畫轉換到另一個時重複引發，做為使用者互動或方法的結果。|
| zoomend           | 在對應完成從一個縮放層級轉換為另一個，做為使用者互動或方法的結果之後，就會引發。|
| zoomstart         | 在對應開始從一個縮放層級轉換為另一個，做為使用者互動或方法的結果，就會引發。|


## <a name="next-steps"></a>後續步驟

請參閱下列文章中的完整程式碼範例：

> [!div class="nextstepaction"]
> [使用 Azure 地圖服務 Services 模組](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [程式碼範例](https://docs.microsoft.com/samples/browse/?products=azure-maps)
