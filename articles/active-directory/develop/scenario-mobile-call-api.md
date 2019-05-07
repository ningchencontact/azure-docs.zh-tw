---
title: 呼叫 web Api-呼叫 web API 的行動裝置應用程式 |Microsoft 身分識別平台
description: 了解如何建置行動應用程式呼叫 Web Api （呼叫 Web API）
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fd65b9f97c373c55a3486e06e83fca7cf824cad
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075111"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>呼叫 web Api-的行動裝置應用程式呼叫 web API

一旦您的應用程式已登入使用者，並接收權杖，MSAL 會公開幾個部分的使用者、 他們的環境，以及簽發之權杖的相關資訊。 您的應用程式可以使用這些值來呼叫 web API，或顯示歡迎訊息給使用者。

首先，我們將探討 MSAL 結果，然後如何使用來自的存取權杖`AuthenticationResult`或`result`呼叫受保護的 web API。

## <a name="msal-result"></a>MSAL 結果

- `AccessToken`:用來呼叫 HTTP 持有人要求中的受保護的 web Api。
- `IdToken`:包含登入的使用者，例如其名稱、 主租用戶，以及唯一識別項的相關實用宣告儲存體。
- `ExpiresOn`： 權杖的到期時間。 MSAL 會處理應用程式的自動重新整理。
- `TenantId`:用來登入的使用者的租用戶識別碼。 來賓使用者 (Azure AD B2B)，這會是使用者登入不是其的家用租用戶的租用戶。  
- `Scopes`： 使用您的權杖所授與的範圍。 這可能是您所要求的子集。

此外，MSAL 也會提供的抽象概念`Account`。 帳戶代表目前的使用者登入帳戶。

- `HomeAccountIdentifier`:使用者的主租用戶識別碼。
- `UserName`:使用者的慣用的使用者名稱。 這可能是空的 Azure AD B2C 使用者。
- `AccountIdentifier`:登入的使用者識別碼。 這會是相同`HomeAccountIdentifier`在大部分情況下除非使用者是另一個租用戶中的來賓。

## <a name="calling-an-api"></a>呼叫 API

準備好的存取權杖之後，很容易即可呼叫 web API。 您的應用程式會將此語彙基元、 建構 HTTP 要求，並加以執行。

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called graph, process data and send to UI 
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put Access Token in HTTP request 
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="ios"></a>iOS

```swift
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)

        // Put Access token in HTTP Request
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")

        URLSession.shared.dataTask(with: request) { data, response, error in
            if let error = error {
                self.updateLogging(text: "Couldn't get graph result: \(error)")
                return
            }
            guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
                self.updateLogging(text: "Couldn't deserialize result JSON")
                return
            }

            // Successfully got data from Graph
            self.updateLogging(text: "Result from Graph: \(result))")
        }.resume()
```

### <a name="xamarin"></a>Xamarin

```CSharp
httpClient = new HttpClient();

// Put Access token in HTTP request 
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Graph
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```

## <a name="making-several-api-requests"></a>提出幾個 API 要求

如果您需要多次呼叫相同的 API，或多個 Api，還是有其他考量，建置您的應用程式時：

- ***增量同意***:Microsoft 身分識別平台可讓應用程式來取得使用者同意，如權限是必要項，而非所有預付。 每當您的應用程式已準備好呼叫的 API，它應該要求只想要使用的範圍。
- ***條件式存取***:在某些情況下，您可能會提出幾個 API 要求時收到額外的條件式存取需求。 若要處理這種情況下，務必要攔截的無訊息的要求錯誤，並準備好進行互動式要求。 如果第一個要求已套用任何條件式存取原則，和您的應用程式嘗試以無訊息方式存取需要條件式存取的新 API，會發生這項目。 若要進一步了解，請參閱[條件式存取的指引](conditional-access-dev-guide.md)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [移至生產環境](scenario-mobile-production.md)
