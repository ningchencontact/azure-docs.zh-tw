---
title: Azure 地圖服務中的 web 地圖控制項使用者入門 |Microsoft Docs
description: 瞭解如何使用 Azure 地圖服務的地圖控制項用戶端 JAVAscript 程式庫。
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: a152f32ef7d68686539b7d6edee57db8a5da9553
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976234"
---
# <a name="use-the-azure-maps-map-control"></a>使用 Azure 地圖服務地圖控制項

地圖控制項用戶端 Javascript 程式庫可讓您將地圖和內嵌的 Azure 地圖服務功能轉譯到 Web 或行動裝置應用程式中。

## <a name="create-a-new-map-in-a-web-page"></a>在網頁中建立新的地圖

您可以使用地圖控制項用戶端 Javascript 程式庫，在網頁中內嵌地圖。

1. 建立新的 HTML 檔案。

2. 載入 Azure 地圖服務 Web SDK。 這可以透過兩個不同的選項來達成：

    a. 使用 Azure 地圖服務 Web SDK 的全域裝載 CDN 版本，方法是將 URL 端點新增到檔案 `<head>` 元素中的樣式表和指令碼參考：

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

    b. 或者，使用 [azure-maps-control](https://www.npmjs.com/package/azure-maps-control) \(英文\) NPM 套件於本機載入 Azure 地圖服務 Web SDK，並搭配您的應用程式裝載它。 此套件也包含 TypeScript 定義。

    > npm 安裝 azure-maps-control

    然後將參考新增到 Azure 地圖服務樣式表，並將指令碼來源參考新增到檔案的 `<head>` 元素：

    ```HTML
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/css/atlas.min.css" type="text/css">
    <script src="node_modules/azure-maps-control/dist/js/atlas.min.js"></script>
    ```

3. 若要轉譯地圖來使它填滿頁面的完整主體，請將下列 `<style>` 元素新增到 `<head>` 元素。

    ```HTML
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

    ```HTML
    <body>
        <div id="myMap"></div>
    </body>
    ```

5. 若要初始化地圖控制項，請在 html 主體中定義新的區段，並建立指令碼。 `Map` `document.getElementById('myMap')` `<div>`建立類別的實例時, 傳入對應`HTMLElement`或的 (例如,) 作為第一個參數。 `id` 使用您自己的 Azure 地圖服務帳戶金鑰或 Azure Active Directory (AAD) 認證來使用[驗證選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions)驗證地圖。 如果您需要建立帳戶或尋找金鑰，請參閱[如何管理 Azure 地圖服務帳戶與金鑰](how-to-manage-account-keys.md)。 [語言] 選項會指定要用於地圖標籤和控制項的語言。 如需有關支援語言的資訊，請參閱[支援的語言](supported-languages.md)。 如果使用訂用帳戶金鑰進行驗證。

    ```HTML
    <script type="text/javascript">
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

    ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'aad',
                clientId: '<Your AAD Client Id>',
                aadAppId: '<Your AAD App Id>',
                aadTenant: 'msft.ccsctp.net'
            }
        });
    </script>
    ```

    如需詳細資訊, 請參閱[使用 Azure 地圖服務的驗證](azure-maps-authentication.md)檔。

6. 除此之外，將下列中繼標籤元素新增到頁面的標頭可能會對您很有幫助：

    ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

7. 將它全部放在您的 HTML 檔案中看起來應該像下面這段程式碼:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

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

        <script type="text/javascript">
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

8. 在網頁瀏覽器中開啟此檔案，並檢視轉譯的地圖。 看起來應該類似下列程式碼:

    <iframe height="700" style="width: 100%;" scrolling="no" title="如何使用地圖控制項" src="//codepen.io/azuremaps/embed/yZpEYL/?height=557&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">請參閱<a href='https://codepen.io'>CodePen</a>上的 <<c0>如何使用地圖控制項Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)。
    </iframe>

## <a name="localizing-the-map"></a>當地語系化對應

Azure 地圖服務提供兩種不同的方式來設定地圖的語言和地區視圖。 第一個選項是將這項資訊新增至全域`atlas`命名空間, 這會導致應用程式中的所有地圖控制項實例預設為這些設定。 以下會將語言設定為法文 ("fr-fr"), 並將地區視圖設為「自動」:

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

第二個選項是在載入對應時, 將此資訊傳入對應選項, 如下所示:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: 'msft.ccsctp.net'
    }
});
```

> [!Note]
> 使用 Web SDK, 可以使用不同的語言和區域設定, 在相同的頁面上載入多個對應實例。 此外, 您可以在使用對應的`setStyle`函式載入對應之後, 更新這些設定。 

以下是語言設為 "fr-fr" 且地區視圖設為 "auto" 的 Azure 地圖服務範例。

![以法文顯示標籤的地圖影像](./media/how-to-use-map-control/websdk-localization.png)

支援的語言和區域觀點的完整清單記載于[此處](supported-languages.md)。

## <a name="next-steps"></a>後續步驟

了解如何建立地圖並與其互動：

> [!div class="nextstepaction"]
> [建立地圖](map-create.md)

了解如何設定地圖的樣式：

> [!div class="nextstepaction"]
> [選擇地圖樣式](choose-map-style.md)

若要將更多資料新增至您的對應:

> [!div class="nextstepaction"]
> [建立地圖](map-create.md)

> [!div class="nextstepaction"]
> [程式碼範例](https://docs.microsoft.com/samples/browse/?products=azure-maps)