---
title: 如何使用 Azure 地圖服務的地圖控制項 | Microsoft Docs
description: 了解如何使用 Azure 地圖服務的地圖控制項用戶端 Javascript 程式庫。
author: dsk-2015
ms.author: dkshir
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 877393de20788b0aa1c76084b121a82f12715cd3
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118066"
---
# <a name="use-the-azure-maps-map-control"></a>使用 Azure 地圖服務的地圖控制項

地圖控制項用戶端 Javascript 程式庫可讓您將地圖和內嵌的 Azure 地圖服務功能轉譯到 Web 或行動裝置應用程式中。

## <a name="create-a-new-map-in-a-web-page"></a>在網頁中建立新的地圖

您可以使用地圖控制項用戶端 Javascript 程式庫，在網頁中內嵌地圖。

1. 建立新的 HTML 檔案。

2. 載入 Azure 地圖服務 Web SDK。 這可以透過兩個不同的選項來達成：
    
    a. 使用 Azure 地圖服務 Web SDK 的全域裝載 CDN 版本，方法是將 URL 端點新增到檔案 `<head>` 元素中的樣式表和指令碼參考：

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=2" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=2"></script>
    ```

    b. 或者，使用 [azure-maps-control](https://www.npmjs.com/package/azure-maps-control) \(英文\) NPM 套件於本機載入 Azure 地圖服務 Web SDK，並搭配您的應用程式裝載它。 此套件也包含 TypeScript 定義。

    > npm 安裝 azure-maps-control

    然後將參考新增到 Azure 地圖服務樣式表，並將指令碼來源參考新增到檔案的 `<head>` 元素：

    ```html
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/css/atlas.min.css" type="text/css" />
    <script src="node_modules/azure-maps-control/dist/js/atlas.min.js"></script>
    ```

3. 若要轉譯地圖來使它填滿頁面的完整主體，請將下列 `<style>` 元素新增到 `<head>` 元素。

    ```html
    <style>
        html, body {
            margin: 0;
        }
    
        #myMap {
            height: 100vh;
            width: 100vw;
        }
    </style>
    ```

4. 在頁面的主體中，新增 `<div>` 元素，並為它提供 **myMap** 的 `id`。 

    ```html
    <body>
        <div id="myMap"></div>
    </body>
    ```

5. 若要初始化地圖控制項，請在 html 主體中定義新的區段，並建立指令碼。 使用您自己的 Azure 地圖服務帳戶金鑰或 Azure Active Directory (AAD) 認證來使用[驗證選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.atlas.authenticationoptions)驗證地圖。 如果您需要建立帳戶或尋找金鑰，請參閱[如何管理 Azure 地圖服務帳戶與金鑰](how-to-manage-account-keys.md)。 [語言] 選項會指定要用於地圖標籤和控制項的語言。 如需有關支援語言的資訊，請參閱[支援的語言](supported-languages.md)。 如果使用訂用帳戶金鑰進行驗證。

    ```html
    <script type='text/javascript'>
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });
    </script>
    ```

    如果使用 Azure Active Directory (AAD) 進行驗證：

    ```html
    <script type='text/javascript'>
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'aad',
                clientId: '<Your AAD Client Id>',
                aadAppId: '<Your AAD App Id',
                aadTenant: 'msft.ccsctp.net'
            }
        });
    </script>
    ```

6. 除此之外，將下列中繼標籤元素新增到頁面的標頭可能會對您很有幫助：

    ```html
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    
    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />
    ```

7. 將全部整理好之後，您的 HTML 檔案看起來應該如下所示：

    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>
    
        <meta charset="utf-8" />
        
        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    
        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />
    
        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=2" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=2"></script>
    
        <style>
            html, body {
                margin: 0;
            }
        
            #myMap {
                height: 100vh;
                width: 100vw;
            }
        </style>
    </head>
    <body>
        <div id="myMap"></div>
        
        <script type='text/javascript'>
            //Create an instance of the map control and set some options.
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
        </script>
    </body>
    </html>
    ```

8. 在網頁瀏覽器中開啟此檔案，並檢視轉譯的地圖。 該元素如下所示：

    <iframe height="700" style="width: 100%;" scrolling="no" title="如何使用地圖控制項" src="//codepen.io/azuremaps/embed/yZpEYL/?height=557&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請在 <a href='https://codepen.io'>CodePen</a> 上，參閱 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/yZpEYL/'>如何使用地圖控制項</a>。
    </iframe>

## <a name="next-steps"></a>後續步驟

了解如何建立地圖並與其互動：

> [!div class="nextstepaction"]
> [建立地圖](map-create.md)

了解如何設定地圖的樣式：

> [!div class="nextstepaction"]
> [選擇地圖樣式](choose-map-style.md)
