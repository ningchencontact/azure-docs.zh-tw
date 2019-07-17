---
title: 搭配 Android 的使用者入門 - Microsoft 身分識別平台 | Azure
description: Android 應用程式如何從 Microsoft 身分識別平台取得存取權杖，以及呼叫 Microsoft Graph API 或需要存取權杖的 API。
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/09/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71c6b0d4cd664b12dbd0fbd4e9423240c8dbebb3
ms.sourcegitcommit: 0ebc62257be0ab52f524235f8d8ef3353fdaf89e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723809"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>從 Android 應用程式登入使用者並呼叫 Microsoft Graph

在本教學課程中，您將了解如何整合 Android 應用程式與 Microsoft 身分識別平台。 您的應用程式會讓使用者登入、取得存取權杖來呼叫 Microsoft Graph API，以及對 Microsoft Graph API 提出要求。  

完成本指南後，您的應用程式會接受使用個人Microsoft 帳戶 (包括 outlook.com、live.com 和其他帳戶)，以及採用 Azure Active Directory 的公司或組織所提供的公司或學校帳戶登入。

## <a name="how-this-tutorial-works"></a>本教學課程的運作方式

![示範本教學課程所產生的應用程式範例如何運作](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

此範例中的應用程式將會登入使用者，並且代表他們取得資料。  這項資料會透過需要授權的受保護 API (Microsoft Graph API) 來存取。

具體而言：

* 您的應用程式會透過瀏覽器或 Microsoft Authenticator 及 Intune 公司入口網站來登入使用者。
* 終端使用者會接受您應用程式已要求的權限。 
* 您的應用程式會針對 Microsoft Graph API 發出存取權杖。
* 此存取權杖會包含在 Web API 的 HTTP 要求中。
* 處理 Microsoft Graph 回應。

此範例會使用適用於 Android 的 Microsoft 驗證程式庫 (MSAL) 來實作驗證。 MSAL 會自動更新權杖、提供裝置上其他應用程式之間的 SSO，以及管理帳戶。

## <a name="prerequisites"></a>必要條件

* 本引導式設定使用 Android Studio。
* 需要 Android 16 或更新版本 (建議使用 19 以上)。

## <a name="library"></a>程式庫

本指南使用下列驗證程式庫：

|程式庫|說明|
|---|---|
|[com.microsoft.identity.client (英文)](https://javadoc.io/doc/com.microsoft.identity.client/msal)|Microsoft Authentication Library (MSAL)|

## <a name="create-a-project"></a>建立專案

此教學課程會建立新的專案。 如果您想改為下載完整的教學課程，[請下載程式碼](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip).

1. 開啟 Android Studio，然後選取 [開始新的 Android Studio 專案] 
2. 選取 [基本活動]  ，然後按 [下一步]  。
3. 為您的應用程式命名
4. 儲存套件名稱。 您稍後會在 Azure 入口網站中加以輸入。 
5. 將 [最低 API 層級]  設為 [API 19]  或更高，然後按一下 [完成]  。
6. 在專案檢視中，從下拉式清單中選擇 [專案]  以顯示來源和非來源專案檔，然後開啟 **app/build.gradle**，並將 `targetSdkVersion` 設為 `27`。

## <a name="register-your-application"></a>註冊您的應用程式

1. 移至 [Azure 入口網站](https://aka.ms/MobileAppReg)
2. 開啟 [應用程式註冊](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) 刀鋒視窗，然後按一下 [+新增註冊]  。
3. 輸入應用程式的 [名稱]  ，然後不設定 [重新導向 URI] 而直接按一下 [註冊]  。
4. 在顯示的窗格中，從 [管理]  區段選取 [驗證]   > [+ 新增平台]   > [Android]  。
5. 輸入您專案的套件名稱。 如果您已下載程式碼，此值會是 `com.azuresamples.msalandroidapp`。
6. 在 [設定 Android 應用程式]  頁面的 [簽章雜湊]  區段中，按一下 [產生開發簽章雜湊]  。 然後，複製要用於平台的 KeyTool 命令。 請注意，KeyTool.exe 會安裝為 Java Development Kit (JDK) 的一部分，且您必須也已安裝 OpenSSL 工具，才能執行 KeyTool 命令。
7. 輸入 KeyTool 所產生的**簽章雜湊**。
8. 按一下 `Configure` 並儲存出現在 [Android 設定]  頁面中的 [MSAL 設定]  ，以便稍後在設定應用程式時可加以輸入。  按一下 [完成]  。

## <a name="build-your-app"></a>建置您的應用程式

### <a name="configure-your-android-app"></a>設定您的 Android 應用程式

1. 在 Android Studio 的專案窗格中，瀏覽至 **app\src\main\res**。
2. 以滑鼠右鍵按一下 [res]  ，然後選擇 [新增]   > [目錄]  。 輸入 `raw` 作為新的目錄名稱，然後按一下 [確定]  。
3. 在 [app]   > [src]   > res   > [raw]  中，建立名為 `auth_config.json` 的新 JSON 檔案，並貼上您先前儲存的 [MSAL 設定]。 請參閱 [ MSAL 組態，以取得詳細資訊](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Configuring-your-app)。
   <!-- Workaround for Docs conversion bug -->
4. 在 [app]   > [src]   > [main]   > [AndroidManifest.xml]  中，於下方新增 `BrowserTabActivity` 活動。 此輸入可讓 Microsoft 在完成驗證後回呼您的應用程式：

    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
        </intent-filter>
    </activity>
    ```

    將 `android:host=` 值取代為您在 Azure 入口網站中註冊的套件名稱。
    將 `android:path=` 值取代為您在 Azure 入口網站中註冊的索引鍵雜湊。 簽章雜湊不應進行 URL 編碼。

5. 在 **AndroidManifest.xml** 內部的 `<application>` 標籤正上方，新增下列權限：

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

### <a name="create-the-apps-ui"></a>建立應用程式 UI

1. 在 Android Studio 專案視窗中，瀏覽至 [app]   > [src]   > [main]   > [res]   > [layout]  ，然後開啟 [activity_main.xml]  ，並開啟 [文字]  檢視。
2. 變更活動配置，例如從 `<androidx.coordinatorlayout.widget.CoordinatorLayout` 變更為 `<androidx.coordinatorlayout.widget.LinearLayout`。
3. 將 `android:orientation="vertical"` 屬性新增至 `LinearLayout` 節點。
4. 將下列程式碼貼到 `LinearLayout` 節點中，並取代目前的內容：

    ```xml
    <TextView
        android:text="Welcome, "
        android:textColor="#3f3f3f"
        android:textSize="50px"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:id="@+id/welcome"
        android:visibility="invisible"/>

    <Button
        android:id="@+id/callGraph"
        android:text="Call Microsoft Graph"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="200dp"
        android:textAllCaps="false" />

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:gravity="center|bottom"
        android:orientation="vertical" >

        <Button
            android:text="Sign Out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:textColor="#FFFFFF"
            android:background="#00a1f1"
            android:textAllCaps="false"
            android:id="@+id/clearCache"
            android:visibility="invisible" />
    </LinearLayout>
    ```

### <a name="add-msal-to-your-project"></a>將 MSAL 新增至您的專案

1. 在 Android Studio 專案視窗中，瀏覽至 [app]   > [src]   > [build.gradle]  。
2. 在 [相依性]  下方，貼上下列內容：

    ```gradle  
    implementation 'com.android.volley:volley:1.1.1'
    implementation 'com.microsoft.identity.client:msal:0.3.+'
    ```

### <a name="use-msal"></a>使用 MSAL

現在，請在 `MainActivity.java` 內進行變更，以在您的應用程式中新增和使用 MSAL。
在 Android Studio 專案視窗中，瀏覽至 [app]   > [src]   > [main]   > [java]   > [com.example.msal]  ，然後開啟 `MainActivity.java`

#### <a name="required-imports"></a>必要的匯入項目

在 `MainActivity.java` 頂端附近新增下列匯入項目：

```java
import android.app.Activity;
import android.content.Intent;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import com.android.volley.*;
import com.android.volley.toolbox.JsonObjectRequest;
import com.android.volley.toolbox.Volley;
import org.json.JSONObject;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

#### <a name="instantiate-msal"></a>具現化 MSAL

在 `MainActivity` 類別中，我們需要具現化 MSAL 及一些應用程式會執行的作業相關設定，包括們想要存取的範圍和 Web API。

複製 `MainActivity` 類別內的下列變數：

```java
final static String SCOPES [] = {"https://graph.microsoft.com/User.Read"};
final static String MSGRAPH_URL = "https://graph.microsoft.com/v1.0/me";

/* UI & Debugging Variables */
private static final String TAG = MainActivity.class.getSimpleName();
Button callGraphButton;
Button signOutButton;

/* Azure AD Variables */
private PublicClientApplication sampleApp;
private IAuthenticationResult authResult;
```

將 `onCreate()` 的內容取代為下列程式碼，以具現化 MSAL：

```java
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);

callGraphButton = (Button) findViewById(R.id.callGraph);
signOutButton = (Button) findViewById(R.id.clearCache);

callGraphButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onCallGraphClicked();
    }
});

signOutButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onSignOutClicked();
    }
});

/* Configure your sample app and save state for this activity */
sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        R.raw.auth_config);

/* Attempt to get a user and acquireTokenSilent */
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {
        if (!accounts.isEmpty()) {
            /* This sample doesn't support multi-account scenarios, use the first account */
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts */
        }
    }
});
```

當使用者開啟您的應用程式時，上述程式碼會嘗試透過 `getAccounts()` 以無訊息模式登入使用者，如果成功的話，則執行 `acquireTokenSilentAsync()`。  在下面幾節中，我們將實作沒有已登入帳戶時的回撥處理常式。

#### <a name="use-msal-to-get-tokens"></a>使用 MSAL 取得權杖

現在，我們可以實作應用程式的 UI 處理邏輯，並以互動方式透過 MSAL 取得權杖。

MSAL 會公開兩個主要方法來取得權杖：`acquireTokenSilentAsync()` 和 `acquireToken()`。  

如果帳戶存在，`acquireTokenSilentAsync()` 會登入使用者並取得權杖，並且不會與使用者有任何互動。 如果成功，MSAL 會將權杖傳遞至應用程式，如果失敗，將會產生 `MsalUiRequiredException`。  如果產生此例外狀況，或是您想讓使用者有互動式的登入體驗 (可能會需要認證、mfa 或其他條件式存取原則，但也可能不需要)，請使用 `acquireToken()`。  

`acquireToken()` 在嘗試登入使用者並取得權杖時，會顯示 UI。 不過，它可能會使用瀏覽器中的工作階段 Cookie 或 Microsoft 驗證器中的帳戶，以提供互動式 SSO 體驗。

請在 `MainActivity` 類別中建立下列三個 UI 方法：

```java
/* Set the UI for successful token acquisition data */
private void updateSuccessUI() {
    callGraphButton.setVisibility(View.INVISIBLE);
    signOutButton.setVisibility(View.VISIBLE);
    findViewById(R.id.welcome).setVisibility(View.VISIBLE);
    ((TextView) findViewById(R.id.welcome)).setText("Welcome, " +
            authResult.getAccount().getUsername());
    findViewById(R.id.graphData).setVisibility(View.VISIBLE);
}

/* Set the UI for signed out account */
private void updateSignedOutUI() {
    callGraphButton.setVisibility(View.VISIBLE);
    signOutButton.setVisibility(View.INVISIBLE);
    findViewById(R.id.welcome).setVisibility(View.INVISIBLE);
    findViewById(R.id.graphData).setVisibility(View.INVISIBLE);
    ((TextView) findViewById(R.id.graphData)).setText("No Data");

    Toast.makeText(getBaseContext(), "Signed Out!", Toast.LENGTH_SHORT)
            .show();
}

/* Use MSAL to acquireToken for the end-user
 * Callback will call Graph api w/ access token & update UI
 */
private void onCallGraphClicked() {
    sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
}
```

新增下列方法以取得目前的活動，並處理無訊息與互動式的回呼：

```java
public Activity getActivity() {
    return this;
}

/* Callback used in for silent acquireToken calls.
 * Looks if tokens are in the cache (refreshes if necessary and if we don't forceRefresh)
 * else errors that we need to do an interactive request.
 */
private AuthenticationCallback getAuthSilentCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");

            /* Store the authResult */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}

/* Callback used for interactive request.  If succeeds we use the access
 * token to call the Microsoft Graph. Does not check cache
 */
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");
            Log.d(TAG, "ID Token: " + authenticationResult.getIdToken());

            /* Store the auth result */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

#### <a name="use-msal-for-sign-out"></a>使用 MSAL 登出

接著，新增登出的支援。

> [!Important]
> 使用 MSAL 登出會從應用程式中移除關於使用者的所有已知資訊，但使用者仍可在其裝置上擁有作用中的工作階段。 如果使用者嘗試再次登入，他們可能會看到登入 UI，但可能不需要重新輸入其認證，因為裝置工作階段仍為作用中。

若要新增登出能力，請在 `MainActivity` 中新增下列方法。 此方法會逐一查看所有帳戶，並將其移除：

```java
/* Clears an account's tokens from the cache.
 * Logically similar to "sign out" but only signs out of this app.
 * User will get interactive SSO if trying to sign back-in.
 */
private void onSignOutClicked() {
    /* Attempt to get a user and acquireTokenSilent
     * If this fails we do an interactive request
     */
    sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
        @Override
        public void onAccountsLoaded(final List<IAccount> accounts) {

            if (accounts.isEmpty()) {
                /* No accounts to remove */

            } else {
                for (final IAccount account : accounts) {
                    sampleApp.removeAccount(
                            account,
                            new PublicClientApplication.AccountsRemovedCallback() {
                        @Override
                        public void onAccountsRemoved(Boolean isSuccess) {
                            if (isSuccess) {
                                /* successfully removed account */
                            } else {
                                /* failed to remove account */
                            }
                        }
                    });
                }
            }

            updateSignedOutUI();
        }
    });
}
```

#### <a name="call-the-microsoft-graph-api"></a>呼叫 Microsoft Graph API

在收到權杖後，我們即可對 [Microsoft Graph API](https://graph.microsoft.com) 提出要求。存取權杖會包含在驗證回呼 `onSuccess()` 方法的 `AuthenticationResult` 內。 若要建構已授權的要求，您的應用程式必須將存取權杖新增至 HTTP 標頭：

| 標頭索引鍵    | value                 |
| ------------- | --------------------- |
| Authorization | 持有人 \<access-token> |

在 `MainActivity` 類別中新增下列兩個方法，以呼叫圖形並更新 UI：

```java
/* Use Volley to make an HTTP request to the /me endpoint from MS Graph using an access token */
private void callGraphAPI() {
    Log.d(TAG, "Starting volley request to graph");

    /* Make sure we have a token to send to graph */
    if (authResult.getAccessToken() == null) {return;}

    RequestQueue queue = Volley.newRequestQueue(this);
    JSONObject parameters = new JSONObject();

    try {
        parameters.put("key", "value");
    } catch (Exception e) {
        Log.d(TAG, "Failed to put parameters: " + e.toString());
    }
    JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
            parameters,new Response.Listener<JSONObject>() {
        @Override
        public void onResponse(JSONObject response) {
            /* Successfully called graph, process data and send to UI */
            Log.d(TAG, "Response: " + response.toString());

            updateGraphUI(response);
        }
    }, new Response.ErrorListener() {
        @Override
        public void onErrorResponse(VolleyError error) {
            Log.d(TAG, "Error: " + error.toString());
        }
    }) {
        @Override
        public Map<String, String> getHeaders() {
            Map<String, String> headers = new HashMap<>();
            headers.put("Authorization", "Bearer " + authResult.getAccessToken());
            return headers;
        }
    };

    Log.d(TAG, "Adding HTTP GET to Queue, Request: " + request.toString());

    request.setRetryPolicy(new DefaultRetryPolicy(
            3000,
            DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
            DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
    queue.add(request);
}

/* Sets the graph response */
private void updateGraphUI(JSONObject graphResponse) {
    TextView graphText = findViewById(R.id.graphData);
    graphText.setText(graphResponse.toString());
}
```

#### <a name="multi-account-applications"></a>多重帳戶應用程式

此應用程式專為單一帳戶案例所建置。 MSAL 也支援多重帳戶案例，但需要從應用程式執行一些額外的工作。 您必須建立 UI，以協助使用者針對每個需要權杖的動作，選取想要使用的帳戶。 或者，您的應用程式可以實作啟發學習法，透過 `getAccounts()` 方法來選取要使用的帳戶。

## <a name="test-your-app"></a>測試應用程式

### <a name="run-locally"></a>在本機執行

將應用程式建置及部署至測試裝置或模擬器。 您應該已能夠登入 Azure AD 或個人 Microsoft 帳戶，並取得權杖。

在您登入之後，應用程式會顯示從 Microsoft Graph `/me` 端點傳回的資料。

### <a name="consent"></a>同意

任何使用者在初次登入您的應用程式時，Microsoft 身分識別都會提示他們同意所要求的權限。  大部分使用者都能夠親自同意，但某些 Azure AD 租用戶已停用使用者同意，而需要系統管理員代表所有使用者表示同意。 若要支援此案例，請在 Azure 入口網站中註冊您的應用程式範圍。

## <a name="get-help"></a>取得說明

對於本教學課程或 Microsoft 身分識別平台若有任何問題，請瀏覽[說明與支援](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)。
