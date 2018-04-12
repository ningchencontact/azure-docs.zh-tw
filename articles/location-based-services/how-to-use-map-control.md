---
title: 如何使用 Azure LBS 的地圖控制項 | Microsoft Docs
description: 了解如何使用 Azure LBS 的地圖控制項用戶端 Javascript 程式庫。
services: location-based-services
author: kgremban
ms.author: kgremban
ms.date: 11/22/2017
ms.topic: article
ms.service: location-based-services
manager: timlt
ms.openlocfilehash: ee767c9461f79437ab49d2a919bb82e7de8feba7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-use-the-azure-location-based-services-map-control"></a>如何使用 Azure LBS 的地圖控制項
地圖控制項用戶端 Javascript 程式庫可讓您將地圖和內嵌的 Azure LBS 功能轉譯到 Web 或行動裝置應用程式中。 

## <a name="prerequisites"></a>先決條件
Azure 位置服務帳戶和金鑰。 如需建立帳戶和擷取金鑰的相關資訊，請參閱[如何管理您的 Azure 位置服務帳戶和金鑰](how-to-manage-account-keys.md)。 

## <a name="create-a-new-map-in-a-web-page-using-the-map-control-api"></a>使用地圖控制項 API 在網頁中建立新的地圖
您可以使用地圖控制項用戶端 Javascript 程式庫，在網頁中內嵌地圖。

1. 建立新的檔案並將它命名為 MapSearch.html。

2. 將 Azure LBS 樣式表和指令碼來源參考新增新檔案的 `<head>` 元素：

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
    
4. 若要初始化地圖控制項，請在 html 主體中定義新的區段並建立指令碼。 在指令碼中使用您自己的 Azure 位置服務帳戶金鑰。 

    ```html
    <div id="map">
        <script>
            var LBSAccountKey = "<_your account key_>";
            var map = new atlas.Map("map", {
                "subscription-key": LBSAccountKey,
                center: [47.59093,-122.33263],
                zoom: 12
            });
        </script>
    </div>
    ```
    
5. 在網頁瀏覽器中開啟此檔案，並檢視轉譯的地圖。