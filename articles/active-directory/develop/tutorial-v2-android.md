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
ms.date: 04/26/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6112facfc0c10d7a0a0495cd778fa6c3cb6130a7
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962144"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>從 Android 應用程式登入使用者並呼叫 Microsoft Graph

在本教學課程中，您將了解如何將 Android 應用程式整合到 Microsoft 身分識別平台中。 具體來說，您的應用程式會讓使用者登入、取得存取權杖來呼叫 Microsoft Graph API，以及對 Microsoft Graph API 提出要求。  

完成本指南後，您的應用程式會接受使用個人Microsoft 帳戶 (包括 outlook.com、live.com 和其他帳戶)，以及採用 Azure Active Directory 的公司或組織所提供的公司或學校帳戶登入。

## <a name="how-this-tutorial-works"></a>本教學課程的運作方式

![示範本教學課程所產生的應用程式範例如何運作](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

此範例中的應用程式將會登入使用者，並且代表他們取得資料。  這項資料會透過需要授權的受保護 API (也就是此案例中的 Microsoft Graph API) 來存取。

具體而言：

* 您的應用程式會透過瀏覽器或 Microsoft Authenticator 及 Intune 公司入口網站來登入使用者。
* 終端使用者會接受您應用程式已要求的權限。 
* 您的應用程式會針對 Microsoft Graph API 發出存取權杖。
* 此存取權杖會包含在 Web API 的 HTTP 要求中。
* 處理 Microsoft Graph 回應。

此範例會使用適用於 Android 的 Microsoft 驗證程式庫 (MSAL) 來實作驗證。MSAL 會自動更新權杖、提供裝置上其他應用程式之間的 SSO 以及管理帳戶。

## <a name="prerequisites"></a>必要條件

* 本引導式設定使用 Android Studio。
* 需要 Android 16 或更新版本 (建議使用 19 以上)。

## <a name="library"></a>程式庫

本指南使用下列驗證程式庫：

|程式庫|說明|
|---|---|
|[com.microsoft.identity.client (英文)](https://javadoc.io/doc/com.microsoft.identity.client/msal)|Microsoft Authentication Library (MSAL)|

## <a name="set-up-your-project"></a>設定專案

此教學課程會建立新的專案。 如果您想改為下載完整的教學課程，[請下載程式碼](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip).

### <a name="create-a-new-project"></a>建立新專案

1. 開啟 Android Studio，然後選取 [開始新的 Android Studio 專案]。
    - 如果 Android Studio 已開啟，請選取 [檔案] > [新增] > [新增專案]。
2. 將 [空白活動] 保留原狀，選取 [下一步]。
3. 命名您的應用程式，將 `Minimum API level`設定為 **API 19 或更新版本**，然後點擊 [完成]。
5. 在您的 `app/build.gradle` 中，將 `targetedSdkVersion`設定為 27。 

## <a name="register-your-application"></a>註冊您的應用程式

如接下來兩節所述，您可以在任一種方式註冊您的應用程式。

### <a name="register-your-app"></a>註冊您的應用程式

1. 前往 [Azure 入口網站](https://aka.ms/MobileAppReg) > 選取 `New registration`。 
2. 請為應用程式輸入**名稱** > `Register`。 **請勿在此階段設定重新導向 URI**。 
3. 在 `Manage` 區段中，移至 `Authentication` > `Add a platform` > `Android`
    - 輸入您專案的套件名稱。 如果您已下載程式碼，此值會是 `com.azuresamples.msalandroidapp`。 
    - 輸入您的偵錯/開發簽章雜湊碼。 使用入口網站中的 KeyTool 命令來產生簽章雜湊碼。 
4. 叫用 `Configure` 並儲存 ***MSAL 組態***，以供稍後使用。 

## <a name="build-your-app"></a>建置您的應用程式

### <a name="configure-your-android-app"></a>設定您的 Android 應用程式

1. 以滑鼠右鍵按一下 [res] > [新增] > [資料夾] > [未經處理的資源資料夾]
2. 在 [app] > [res] > [raw] 中，建立稱為 `auth_config.json` 的新 JSON 檔案，並貼上您的 ***MSAL 組態***. 請參閱 [ MSAL 組態，以取得詳細資訊](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Configuring-your-app)。
   <!-- Workaround for Docs conversion bug -->
3. 在 [app] > [manifests] > [AndroidManifest.xml] 中，新增下方的 `BrowserTabActivity` 活動。 此輸入可讓 Microsoft 在完成驗證後回呼您的應用程式：

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

    請注意，在 **AndroidManifest.xml** 中，使用的簽章雜湊碼不能是 URL 編碼。 

4. 在 **AndroidManifest.xml** 內部和 `<application>` 標記正上方，新增下列權限：

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

5. 在 `BrowserTabActivity` 中，將***套件名稱***和***簽章雜湊碼***取代為在 Azure 入口網站中註冊的值。

### <a name="create-the-apps-ui"></a>建立應用程式 UI

1. 移至 [res] > [配置]，然後開啟 **activity_main.xml**。
2. 將活動配置從 `android.support.constraint.ConstraintLayout` 或其他配置變更為 `LinearLayout`。
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

1. 在 Android Studio 中，選取 [Gradle 指令碼] >  [build.gradle (模組: 應用程式)]。
2. 在 [相依性] 之下，貼上下列程式碼：

    ```gradle  
    implementation 'com.android.volley:volley:1.1.1'
    implementation 'com.microsoft.identity.client:msal:0.3.+'
    ```

### <a name="use-msal"></a>使用 MSAL 

接下來幾節將會在 `MainAcitivty.java` 內部進行變更。 我們將逐步進行在應用程式中新增及使用 MSAL 所需的每個步驟。

#### <a name="required-imports"></a>必要的匯入項目

在您的專案中新增下列匯入項目： 

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

#### <a name="instantiating-msal"></a>具現化 MSAL 

在 `MainActivity` 類別中，我們需要具現化 MSAL 及一些應用程式會執行的作業相關設定，包括們想要存取的範圍和 Web API。 

複製 `MainActivity` 中的下列變數：

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

現在，若要具現化 MSAL，請複製 `onCreate(...)` 方法內的下列程式碼：

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

當使用者開啟您的應用程式時，上述程式碼區塊會嘗試透過 `getAccounts(...)` 和 `acquireTokenSilentAsync(...)` (如果成功的話)，以無訊息模式來登入使用者。  在下面幾節中，我們將實作沒有已登入帳戶時的回撥處理常式。 

#### <a name="use-msal-to-get-tokens"></a>使用 MSAL 取得權杖

現在，我們可以實作應用程式的 UI 處理邏輯，並以互動方式透過 MSAL 取得權杖。 

MSAL 會公開兩個主要方法來取得權杖：`acquireTokenSilentAsync` 和 `acquireToken`。  

如果帳戶存在，`acquireTokenSilentAsync` 會登入使用者並取得權杖，並且不會與使用者有任何互動。 如果成功，MSAL 會將權杖傳遞至應用程式，如果失敗，將會產生 `MsalUiRequiredException`。  如果產生此例外狀況，或是您想讓使用者有互動式的登入體驗 (可能會需要認證、mfa 或其他條件式存取原則，但也可能不需要)，那麼您可以使用 `acquireToken`。  

當 `acquireToken` 嘗試登入使用者並取得權杖時，一律會顯示 UI；不過，此方法可能會使用瀏覽器中的工作階段 Cookie 或 Microsoft 驗證器中的帳戶，以提供互動式 SSO 體驗。 

若要開始，請在 `MainActivity` 類別中建立下列三個 UI 方法：

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

接下來，新增方法來取得目前的活動，並處理無訊息與互動式的回撥：

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

接下來，我們將加入登出應用程式的支援。 

請務必注意，使用 MSAL 登出會從此應用程式移除關於使用者的所有已知資訊，但使用者仍可在其裝置上擁有作用中的工作階段。 如果使用者嘗試再次登入，他們可能會看到互動，但可能不需要重新輸入其認證，因為裝置工作階段正在作用中。 

若要加入登出，請將下列方法複製到您的應用程式，該方法會在所有帳戶上循環，並移除這些帳戶：

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

成功取得權杖後，我們可以對 Microsoft Graph API 提出要求。 在驗證回撥的 `onSuccess(...)` 方法內，您可以在 `AuthenticationResult` 中找到存取權杖。 若要建構已授權的要求，您的應用程式必須將存取權杖新增至 HTTP 標頭：

| 標頭索引鍵    | value                 |
| ------------- | --------------------- |
| Authorization | Bearer <access-token> |

若要在程式碼中執行該作業，請將下列兩個方法加入您的應用程式，以呼叫圖形和更新 UI： 

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

深入了解 [Microsoft Graph API](https://graph.microsoft.com)！

#### <a name="multi-account-applications"></a>多重帳戶應用程式

此應用程式專為單一帳戶案例所建置。 MSAL 也支援多重帳戶案例，但需要從應用程式執行一些額外的工作。 您必須建立 UI，以協助使用者針對每個需要權杖的動作，選取想要使用的帳戶。 或者，您的應用程式可以實作啟發學習法，透過 `getAccounts(...)` 方法來選取要使用的帳戶。 

## <a name="test-your-app"></a>測試應用程式

### <a name="run-locally"></a>在本機執行

如果您已遵循上述的程式碼，請嘗試將應用程式建置及部署至測試裝置或模擬器。 您應該已能夠登入 Azure AD 或個人 Microsoft 帳戶，並取得權杖了！ 使用者登入之後，此應用程式會顯示從 Microsoft Graph `/me` 端點傳回的資料。 

如果您有任何問題，歡迎隨時對此文件或 MSAL 程式庫提出問題，以讓我們知曉。 

### <a name="consent-to-your-app"></a>同意您的應用程式

任何使用者在初次登入您的應用程式時，Microsoft 身分識別都會提示他們同意所要求的權限。  大部分使用者都能夠親自同意，但某些 Azure AD 租用戶已停用使用者同意 - 而需要系統管理員代表所有使用者來同意。  若要支援此案例，請務必在 Azure 入口網站中註冊您的應用程式範圍。

## <a name="help-and-support"></a>說明與支援

對於本教學課程或 Microsoft 身分識別平台有任何疑問嗎？ 請參閱[說明與支援](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)
