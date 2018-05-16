---
title: 如何使用 Azure 地圖服務的地圖控制項 | Microsoft Docs
description: 了解如何使用 Azure 地圖服務的地圖控制項用戶端 Javascript 程式庫。
services: azure-maps
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
manager: timlt
ms.openlocfilehash: bbd06aad9052d2a775c35dd08f80462f8ea505a9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-use-the-azure-maps-map-control"></a>如何使用 Azure 地圖服務的地圖控制項
地圖控制項用戶端 Javascript 程式庫可讓您將地圖和內嵌的 Azure 地圖服務功能轉譯到 Web 或行動裝置應用程式中。 

## <a name="create-a-new-map-in-a-web-page"></a>在網頁中建立新的地圖

您可以使用地圖控制項用戶端 Javascript 程式庫，在網頁中內嵌地圖。

1. 建立新的檔案並將它命名為 MapSearch.html。

2. 將 Azure 地圖服務樣式表和指令碼來源參考新增新檔案的 `<head>` 元素：

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
    ```
    
3. 若要在瀏覽器中轉譯新地圖，請在 `<style>` 元素中新增 **#map** 參考。

    ```html
    #map {
                width: 100%;
                height: 100%;
            }
    ``` 
    
4. 若要初始化地圖控制項，請在 html 主體中定義新的區段並建立指令碼。 在指令碼中使用您自己的 Azure 地圖服務帳戶金鑰。 如果您需要建立帳戶或尋找金鑰，請參閱[如何管理 Azure 地圖服務帳戶和金鑰](how-to-manage-account-keys.md)

    ```html
    <div id="map">
        <script>
            var MapsAccountKey = "<_your account key_>";
            var map = new atlas.Map("map", {
                "subscription-key": MapsAccountKey,
                center: [47.59093,-122.33263],
                zoom: 12
            });
        </script>
    </div>
    ```
    
5. 在網頁瀏覽器中開啟此檔案，並檢視轉譯的地圖。

## <a name="next-steps"></a>後續步驟

本文說明如何使用 Azure 地圖服務金鑰建立基本地圖。 如需更多可新增至地圖的程式碼範例，請參閱下列文章： 

* [建立地圖](map-create.md)
* [新增圖釘](map-add-pin.md)