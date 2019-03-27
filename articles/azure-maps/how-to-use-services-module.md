---
title: 使用服務模組-Azure 地圖服務 |Microsoft Docs
description: 了解如何使用 Azure Maps 服務模組。
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: 335e114fc6a4afa83a6b82509148cf258a9e8347
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2019
ms.locfileid: "58501147"
---
# <a name="using-the-azure-maps-services-module"></a>使用 Azure Maps 服務模組

Azure 地圖服務 Web SDK 提供的協助程式庫，可讓您更容易使用的 web 或使用 JavaScript 或 TypeScript 的 Node.js 應用程式中的 Azure 地圖服務 REST 服務的服務模組。

## <a name="using-the-services-module-in-a-web-page"></a>在網頁中使用的服務模組

1. 建立新的 HTML 檔案。
2. 載入 Azure Maps 服務模組中。 這可以透過兩個不同的選項來達成：

    a. 使用全域 CDN 的裝載的版本 Azure Maps 服務模組新增的指令碼參考<head>檔案項目：
    
    ```html
    <script src="https://atlas.microsoft.com/sdk/js/atlas-service.js?api-version=2"></script>
    ```
    
    b. 或者，載入在本機使用的 Azure 對應 Web SDK 原始程式碼[azure 地圖服務 rest](https://www.npmjs.com/package/azure-maps-rest) NPM 封裝，並裝載您的應用程式。 此套件也包含 TypeScript 定義。
    
    > npm 安裝 azure 地圖服務 rest
    
    然後指令碼將參考加入至`<head>`檔案項目：
    
    ```html
    <script src="node_modules/azure-maps-rest/dist/js/atlas-service.min.js"></script>
    ```

3. 若要初始化的服務 URL 的用戶端端點，您必須先建立驗證管線。 使用您自己的 Azure 地圖服務帳戶金鑰或 Azure Active Directory (AAD) 的認證來驗證搜尋服務用戶端。 在此範例中，將會建立搜尋服務 URL 的用戶端。 如果使用的訂用帳戶金鑰進行驗證：

    ```javascript
    //Get an Azure Maps key at https://azure.com/maps
    var subscriptionKey = '<Your Azure Maps Key>';
    
    //Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);
    
    //Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });
    
    //Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```
    
    如果使用 Azure Active Directory (AAD) 進行驗證：

    ```javascript
    // Enter your Azure Actiuve Directory client ID.
    var clientId = "<Your Azure Active Directory Client Id>";
    
    // Use TokenCredential with OAuth token (AAD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);
    
    // Create a repeating timeout that will renew the AAD token.
    // This timeout must be cleared once the TokenCredential object is no longer needed.
    // If the timeout is not cleared the memory used by the TokenCredential will never be reclaimed.
    var renewToken = async () => {
        try {
            console.log("Renewing token");
            var token = await getAadToken();
            tokenCredential.token = token;
            tokenRenewalTimer = setTimeout(renewToken, getExpiration(token));
        } catch (error) {
            console.log("Caught error when renewing token");
            clearTimeout(tokenRenewalTimer);
            throw error;
        }
    }
    tokenRenewalTimer = setTimeout(renewToken, getExpiration(aadToken));
    
    // Use tokenCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
        retryOptions: { maxTries: 4 } // Retry options
    });
    
    //Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
        //Use the logged in auth context to get a token.
        return new Promise((resolve, reject) => {
            //The resource should always be https://atlas.microsoft.com/.
            const resource = "https://atlas.microsoft.com/";
            authContext.acquireToken(resource, (error, token) => {
                if (error) {
                    reject(error);
                } else {
                    resolve(token);
                }
            });
        })
    }

    function getExpiration(jwtToken) {
        //Decode the JWT token to get the expiration timestamp.
        const json = atob(jwtToken.split(".")[1]);
        const decode = JSON.parse(json);

        //Return the milliseconds until the token needs renewed.
        //Reduce the time until renew by 5 minutes to avoid using an expired token.
        //The exp property is the timestamp of the expiration in seconds.
        const renewSkew = 300000;
        return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    如需詳細資訊，請參閱 <<c0> [ 使用 Azure 地圖服務進行驗證](azure-maps-authentication.md)。

4. 下列程式碼會使用新建立的搜尋服務 URL 用戶端，以地理編碼的地址，使用"1 Microsoft Way，Redmond，WA"`searchAddress`函式，並顯示結果頁面的主體中的資料表。 

    ```javascript
    //Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa').then(response => {
      var html = [];
      
      //Display the total results.
      html.push('Total results: ', response.summary.numResults, '<br/><br/>');
     
      //Create a table of the results.
      html.push('<table><tr><td></td><td>Result</td><td>Latitude</td><td>Longitude</td></tr>');
      
      for(var i=0;i<response.results.length;i++){
        html.push('<tr><td>', (i+1), '.</td><td>', 
                  response.results[i].address.freeformAddress, 
                  '</td><td>', 
                  response.results[i].position.lat,
                  '</td><td>', 
                  response.results[i].position.lon,
                  '</td></tr>');
      }
      
      html.push('</table>');
      
      //Add the result HTML to the body of the page.
      document.body.innerHTML = html.join('');
    });
    ```

    以下是完整執行的程式碼範例：

<iframe height="500" style="width: 100%;" scrolling="no" title="使用服務模組" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱畫筆<a href='https://codepen.io/azuremaps/pen/zbXGMR/'>使用的服務模組</a>透過 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 上<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [MapsURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.mapsurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SearchURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [RouteURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TokenCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.tokencredential?view=azure-iot-typescript-latest)

請參閱下列文章中使用的服務模組的其他程式碼範例：

> [!div class="nextstepaction"]
> [在地圖上顯示搜尋結果](./map-search-location.md)

> [!div class="nextstepaction"]
> [從座標取得資訊](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [顯示從甲地到乙地的指示](./map-route.md)