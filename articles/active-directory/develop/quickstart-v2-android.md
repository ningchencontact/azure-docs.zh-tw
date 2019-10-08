---
title: Microsoft 身分識別平台 Android 快速入門 | Azure
description: 了解 Android 應用程式如何呼叫需要來自 Microsoft 身分識別平台端點存取權杖的 API。
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2019
ms.author: twhitney
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: e11e47952f70ce0cd212ca93eff1c38f2b3993a8
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2019
ms.locfileid: "71678060"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>快速入門：從 Android 應用程式登入使用者並呼叫 Microsoft Graph API

此快速入門將使用程式碼範例示範 Android 應用程式如何登入個人、公司或學校帳戶，然後取得存取權杖並呼叫 Microsoft Graph API。

![範例應用程式的螢幕擷取畫面](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **先決條件**
> * Android Studio 
> * 必須有 Android 16+

## <a name="step-1-get-the-sample-app"></a>步驟 1：取得範例應用程式

[複製程式碼](https://github.com/Azure-Samples/ms-identity-android-java.git)。

## <a name="step-2-register-your-application"></a>步驟 2：註冊您的應用程式

若要註冊應用程式物件，並手動將該應用程式物件的註冊資訊新增至範例專案，請遵循下列步驟：

1. 移至 [Azure 入口網站](https://aka.ms/MobileAppReg)。
1. 開啟 [應用程式註冊](https://portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) 刀鋒視窗，然後按一下 [+新增註冊]  。
1. 輸入應用程式註冊的 [名稱]  ，然後不設定 [重新導向 URI] 而直接按一下 [註冊]  。
1. 在 [管理]  區段中，選取 [驗證]   > [+ 新增平台]   > [Android]  。 (您可能必須選取刀鋒視窗頂端附近的 [試用新的體驗]  ，才能看到此畫面)
1. 輸入專案的 [套件名稱]  ，即 `com.azuresamples.msalandroidapp`。
1. 在 [設定 Android 應用程式]  頁面的 [簽章雜湊]  區段中，按一下 [產生開發簽章雜湊]  ，然後複製 KeyTool 命令，以用於您要用來開發 Android 應用程式的平台。

   > [!Note]
   > KeyTool.exe 會安裝為 Java 開發套件 (JDK) 的一部分。 您也必須安裝 OpenSSL 工具來執行 KeyTool 命令。  您的路徑中將需要 keytool，以及 OpenSSL\bin 目錄。

1. 執行您在終端機視窗中從入口網站複製的 keytool 命令。
1. 在入口網站中，將產生的簽章雜湊輸入 [簽章雜湊]  下方。
1. 按一下 `Configure`，並建立 [MSAL 組態]  的複本。 在下一個步驟中，您會將此複本複製並貼到組態檔中。 按一下 [完成]  。

## <a name="step-3-add-your-app-registration"></a>步驟 3：新增您的應用程式註冊

1. 在 Android Studio 中開啟範例專案。
1. 在 [app]   > [res]   > [raw]  內部，開啟 **auth_config_multiple_account.json**。  貼上 MSAL 組態的內容。 這會從入口網站新增用戶端識別碼、租用戶識別碼和 redirect_uri。 它會顯示如下，但已填入用戶端識別碼、租用戶識別碼和 redirect_uri 的值：

    ```json
    {
      "client_id" : "<your_client_id_here>",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "<your_redirect_uri_here>",
      "account_mode" : "MULTIPLE",
      "broker_redirect_uri_registered": true,
      "authorities" : [
        {
          "type": "AAD",
          "audience": {
            "type": "AzureADandPersonalMicrosoftAccount",
            "tenant_id": "common"
          }
        }
      ]
    }
    ```

1. 開啟 [app]   > [res]   > [raw]  ，並開啟 **auth_config_single_account.json**，然後貼上 MSAL 組態的內容。 它看起來會像是前述的 **auth_config_multiple_account.json** 檔案。
1. 在 [app]   > [manifests]   > [AndroidManifest.xml]  中，尋找 `BrowserTabActivity` 活動。 此輸入可讓 Microsoft 在完成驗證後回呼您的應用程式：

    ```xml
    ...
    <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
                <intent-filter>
                    <action android:name="android.intent.action.VIEW" />
    
                    <category android:name="android.intent.category.DEFAULT" />
                    <category android:name="android.intent.category.BROWSABLE" />
    
                    <!--
                        Add in your scheme/host from registered redirect URI
                        note that the leading "/" is required for android:path
                        For Example:
                        <data
                            android:host="com.azuresamples.msalandroidapp"
                            android:path="/1wIqXSqBj7w+h11ZifsnqwgyKrY="
                            android:scheme="msauth" />
                    -->
    
                    <data
                        android:host="YOUR_PACKAGE_NAME - must be registered at https://aka.ms/MobileAppReg"
                        android:path="/YOUR_DECODED_SIGNATURE_HASH - must be registered at https://aka.ms/MobileAppReg"
                        android:scheme="msauth" />
                </intent-filter>
            </activity>
    ```
    
1. 將 `android:host=` 值的套件名稱取代為您在 Azure 入口網站中註冊的名稱。  在此案例中，此名稱將是：`com.azuresamples.msalandroidapp`。

    > [!IMPORTANT]
    > **android:path** 值**必須**有前置的 "/" 字元，否則該值下方將會出現紅色字行，且範例應用程式將無法執行。
     
1. 將 `android:path=` 值取代為您先前藉由執行 keytool 而取得並在 Azure 入口網站中輸入的索引鍵雜湊。 簽章雜湊不應進行 URL 編碼。

## <a name="step-4-run-the-sample-app"></a>步驟 4：執行範例應用程式

從 Android Studio 的**可用的裝置**下拉式清單中選取您的模擬器或裝置，然後執行應用程式。

範例應用程式會在 [單一帳戶模式]  畫面上啟動。 依預設會提供預設範圍 **user.read**，這是在 Microsoft Graph API 呼叫期間讀取您自己的設定檔資料時所使用的範圍。 依預設會提供 Microsoft Graph API 呼叫的 URL。 您可以視需要變更這兩項設定。

![顯示單一和多個帳戶使用量的 MSAL 範例應用程式](./media/quickstart-v2-android/quickstart-sample-app.png)

使用應用程式功能表，在單一和多重帳戶模式之間進行變更。

在單一帳戶模式中，使用工作或主帳戶登入：

1. 選取 [以互動方式取得圖形資料]  ，以提示使用者輸入其認證。 您會在畫面底部看到 Microsoft Graph API 呼叫的輸出。
2. 登入之後，選取 [以無訊息方式取得圖形資料]  以呼叫 Microsoft Graph API，而不再次提示使用者提供認證。 您會在畫面底部看到 Microsoft Graph API 呼叫的輸出。

在多重帳戶模式中，您可以重複相同的步驟。  此外，您也可以移除已登入的帳戶，這也會移除該帳戶的快取權杖。

## <a name="how-the-sample-works"></a>此範例的運作方式

程式碼會組織成片段，說明如何撰寫單一和多重帳戶 MSAL 應用程式。 程式碼檔案的組織方式如下：

| 檔案  | 示範  |
|---------|---------|
| MainActivity | 管理 UI |
| MSGraphRequestWrapper  | 使用 MSAL 所提供的權杖來呼叫 Microsoft Graph API |
| MultipleAccountModeFragment  | 初始化多重帳戶應用程式、載入使用者帳戶，並取得權杖以呼叫 Microsoft Graph API |
| SingleAccountModeFragment | 初始化單一帳戶應用程式、載入使用者帳戶，並取得權杖以呼叫 Microsoft Graph API |
| res/auth_config_multiple_account.json  | 多重帳戶組態檔 |
| res/auth_config_single_account.json  | 單一帳戶組態檔 |
| Gradle Scripts/build.grade (Module:app) | 在此會新增 MSAL 程式庫相依性 |

現在我們將更詳細地探討這些檔案，並呼叫在每個檔案中 MSAL 特有的程式碼。

### <a name="add-msal-to-the-app"></a>將 MSAL 新增至應用程式

MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) 這個程式庫是用來登入使用者並要求權杖，該權杖會用來存取受 Microsoft 身分識別平台保護的 API。 當您在 [Gradle Scripts]   > [build.gradle (Module: app)]  中的 [相依性]  底下新增以下內容時，Gradle 3.0+ 就會安裝該程式庫：

```gradle  
implementation 'com.microsoft.identity.client:msal:1.0.0'
```

您可以在 build.gradle (Module: app) 的範例專案中看到此內容：

```java
dependencies {
    ...
    implementation 'com.microsoft.identity.client:msal:1.0.0-RC7'
    ...
}
```

這會指示 Gradle 從 Maven 中央存放庫下載並建置 MSAL。

### <a name="msal-imports"></a>MSAL 匯入

與 MSAL 程式庫相關的匯入為 `com.microsoft.identity.client.*`。  例如，您會看到 `import com.microsoft.identity.client.PublicClientApplication;`，這是 `PublicClientApplication` 類別的命名空間，代表您的公用用戶端應用程式。

### <a name="singleaccountmodefragmentjava"></a>SingleAccountModeFragment.java

此檔案示範如何建立單一帳戶 MSAL 應用程式，並呼叫 Microsoft Graph API。

單一帳戶應用程式僅供單一使用者使用。  例如，您可能只會有一個用來登入對應應用程式的帳戶。

#### <a name="single-account-msal-initialization"></a>單一帳戶 MSAL 初始化

在 `onCreateView()` 中，會使用儲存在 `auth_config_single_account.json` 檔案中的組態資訊來建立單一帳戶 `PublicClientApplication`。  這就是您初始化 MSAL 程式庫以供單一帳戶 MSAL 應用程式使用的方式：

```java
...
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createSingleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_single_account,
        new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(ISingleAccountPublicClientApplication application) {
                /**
                    * This test app assumes that the app is only going to support one account.
                    * This requires "account_mode" : "SINGLE" in the config json file.
                    **/
                mSingleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                displayError(exception);
            }
        });
```

#### <a name="sign-in-a-user"></a>登入使用者

用來登入使用者的程式碼位於 `initializeUI()` 的 `signInButton` 點擊處理常式中。

在嘗試取得權杖前，請先呼叫 `signIn()`。 `signIn()` 的行為會如同呼叫了 `acquireToken()`，而對登入的使用者產生互動式提示。

使用者登入是非同步作業。 在使用者登入後，會傳遞呼叫 Microsoft Graph API 的回呼，並更新 UI：

```java
mSingleAccountApp.signIn(getActivity(), null, getScopes(), getAuthInteractiveCallback());
```

#### <a name="sign-out-a-user"></a>登出使用者

用來登出使用者的程式碼位於 `initializeUI()` 的 `signOutButton` 點擊處理常式中。  將使用者登出是非同步作業。 將使用者登出後，也會清除該帳戶的權杖快取。 當使用者帳戶登出後，會建立一個更新 UI 的回呼：

```java
mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
    @Override
    public void onSignOut() {
        updateUI(null);
        performOperationOnSignOut();
    }

    @Override
    public void onError(@NonNull MsalException exception) {
        displayError(exception);
    }
});
```

#### <a name="get-a-token-interactively-or-silently"></a>以互動或無訊息方式取得權杖

若要向使用者呈現最少的提示數，您通常會以無訊息方式取得權杖。 然後，如果發生錯誤，請嘗試以互動方式取得權杖。 應用程式第一次呼叫 `signIn()` 時，會有效地作為 `acquireToken()` 的呼叫，這會提示使用者提供認證。

在某些情況下，可能會提示使用者選取其帳戶、輸入認證，或同意應用程式要求的權限，這些情況包括：

* 使用者第一次登入應用程式時
* 如果使用者重設自己的密碼，就必須輸入自己的認證
* 如果已撤銷同意
* 如果您的應用程式明確要求同意
* 您的應用程式第一次要求存取資源時
* 需要 MFA 或其他條件式存取原則時

以互動方式 (也就是使用者必須使用 UI 的方式) 取得權杖的程式碼，位於 `initializeUI()` 的 `callGraphApiInteractiveButton` 點擊處理常式中：

```java
/**
 * If acquireTokenSilent() returns an error that requires an interaction (MsalUiRequiredException),
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your Single Sign-On refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 */
mSingleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

如果使用者已經登入，`acquireTokenSilentAsync()` 可讓應用程式以無訊息方式在 `callGraphApiSilentButton` 點擊處理常式中要求權杖，如 `initializeUI()` 中所說明：

```java
/**
  * Once you've signed the user in,
  * you can perform acquireTokenSilent to obtain resources without interrupting the user.
  */
  mSingleAccountApp.acquireTokenSilentAsync(getScopes(), AUTHORITY, getAuthSilentCallback());
```

#### <a name="load-an-account"></a>載入帳戶

載入帳戶的程式碼位於 `loadAccount()` 中。  載入使用者的帳戶是非同步作業，因此會將在帳戶載入、變更或發生錯誤時予以處理的回呼傳至 MSAL。  下列程式碼也會處理 `onAccountChanged()`，此情況會在移除帳戶、使用者變更為另一個帳戶時發生。

```java
private void loadAccount() {
    ...

    mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback() {
        @Override
        public void onAccountLoaded(@Nullable IAccount activeAccount) {
            // You can use the account data to update your UI or your app database.
            updateUI(activeAccount);
        }

        @Override
        public void onAccountChanged(@Nullable IAccount priorAccount, @Nullable IAccount currentAccount) {
            if (currentAccount == null) {
                // Perform a cleanup task as the signed-in account changed.
                performOperationOnSignOut();
            }
        }

        @Override
        public void onError(@NonNull MsalException exception) {
            displayError(exception);
        }
    });
```

#### <a name="call-microsoft-graph"></a>呼叫 Microsoft Graph

當使用者登入時，對 Microsoft Graph 的呼叫會透過 `callGraphAPI()` 所要求的 HTTP 來進行。 此函式是一個包裝函式，可藉由執行如下的工作來簡化範例：從 `authenticationResult` 取得存取權杖，並將呼叫封裝至 MSGraphRequestWrapper，以及顯示呼叫的結果。

```java
private void callGraphAPI(final IAuthenticationResult authenticationResult) {
    MSGraphRequestWrapper.callGraphAPIUsingVolley(
            getContext(),
            graphResourceTextView.getText().toString(),
            authenticationResult.getAccessToken(),
            new Response.Listener<JSONObject>() {
                @Override
                public void onResponse(JSONObject response) {
                    /* Successfully called graph, process data and send to UI */
                    ...
                }
            },
            new Response.ErrorListener() {
                @Override
                public void onErrorResponse(VolleyError error) {
                    ...
                }
            });
}
```

### <a name="auth_config_single_accountjson"></a>auth_config_single_account.json

這是使用單一帳戶的 MSAL 應用程式的組態檔。

如需這些欄位的說明，請參閱[了解 Android MSAL 組態檔](msal-configuration.md)。

請注意 `"account_mode" : "SINGLE"` 的存在，這會將此應用程式設定為使用單一帳戶。

```json
{
  "client_id" : "<your_client_id_here>",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "<your_redirect_uri_here>",
  "account_mode" : "SINGLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

### <a name="multipleaccountmodefragmentjava"></a>MultipleAccountModeFragment.java

此檔案示範如何建立多重帳戶 MSAL 應用程式，並呼叫 Microsoft Graph API。 

舉例來說，可讓您使用多個使用者帳戶 (例如工作帳戶和個人帳戶) 的電子郵件應用程式，即為多重帳戶應用程式。

#### <a name="multiple-account-msal-initialization"></a>多重帳戶 MSAL 初始化

在 `onCreateView()` 中，會使用儲存在 `auth_config_multiple_account.json file` 中的組態資訊來建立多重帳戶應用程式物件 (`IMultipleAccountPublicClientApplication`)：

```java
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createMultipleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_multiple_account,
        new IPublicClientApplication.IMultipleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(IMultipleAccountPublicClientApplication application) {
                mMultipleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                ...
            }
        });
```

建立的 `MultipleAccountPublicClientApplication` 物件會儲存在類別成員變數中，以便用來與 MSAL 程式庫互動，而取得權杖以及載入和移除使用者帳戶。

#### <a name="load-an-account"></a>載入帳戶

多重帳戶應用程式通常會呼叫 `GetAccounts()`，以選取要用於 MSAL 作業的帳戶。 載入帳戶的程式碼位於 `loadAccount()` 中。  載入使用者帳戶是非同步作業。 因此，會以回呼處理帳戶載入、變更或發生錯誤時的情況。

```java
/**
    * Load the currently signed-in account, if there's any.
    * In the shared device mode, if the user is signed out from the device, the app can also perform the clean-up work in onAccountChanged().
    */
private void loadAccount() {
    ...
    mMultipleAccountApp.getAccounts(new IPublicClientApplication.LoadAccountsCallback() {
        @Override
        public void onTaskCompleted(final List<IAccount> result) {
            // You can use the account data to update your UI or your app database.
            accountList = result;
            updateUI(accountList);
        }

        @Override
        public void onError(MsalException exception) {
            displayError(exception);
        }
    });
}
```

#### <a name="get-a-token-interactively-or-silently"></a>以互動或無訊息方式取得權杖

在某些情況下，可能會提示使用者選取其帳戶、輸入認證，或同意應用程式要求的權限，這些情況包括：

* 使用者首次登入應用程式
* 如果使用者重設自己的密碼，就必須輸入自己的認證 
* 如果已撤銷同意 
* 如果您的應用程式明確要求同意 
* 您的應用程式第一次要求存取資源時
* 需要 MFA 或其他條件式存取原則時

多重帳戶應用程式通常應以互動方式取得權杖，也就是使用者必須使用 UI，並呼叫 `acquireToken()`。  以互動方式取得權杖的程式碼，位於 `initializeUI()` 的 `callGraphApiInteractiveButton` 點擊處理常式中：

```java
/**
 * Acquire token interactively. It will also create an account object for the silent call as a result (to be obtained by getAccount()).
 *
 * If acquireTokenSilent() returns an error that requires an interaction,
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your SSO refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 */
mMultipleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

應用程式應該不需要使用者在每次要求權杖時都必須登入。 如果使用者已經登入，`acquireTokenSilentAsync()` 可讓應用程式在 `callGraphApiSilentButton` 點擊處理常式中要求權杖，而不會對使用者發出提示，如 `initializeUI()` 中所說明：

```java
/**
 * Performs acquireToken without interrupting the user.
 *
 * This requires an account object of the account you're obtaining a token for.
 * (can be obtained via getAccount()).
 */
mMultipleAccountApp.acquireTokenSilentAsync(getScopes(),
accountList.get(accountListSpinner.getSelectedItemPosition()),
AUTHORITY,
getAuthSilentCallback());
```

#### <a name="remove-an-account"></a>移除帳戶

移除帳戶以及帳戶的任何快取權杖的程式碼，位於 `initializeUI()` 中的移除帳戶按鈕的處理常式中。 您必須要有從 MSAL 函式 (如 `getAccounts()` 和 `acquireToken()`) 取得的帳戶物件，才能移除帳戶。 由於移除帳戶是非同步作業，因此會提供 `onRemoved` 回呼以更新 UI。

```java
/**
  * Removes the selected account and cached tokens from this app (or device, if the device is in shared mode).
  */
mMultipleAccountApp.removeAccount(accountList.get(accountListSpinner.getSelectedItemPosition()),
        new IMultipleAccountPublicClientApplication.RemoveAccountCallback() {
            @Override
            public void onRemoved() {
                ...
                /* Reload account asynchronously to get the up-to-date list. */
                loadAccount();
            }

            @Override
            public void onError(@NonNull MsalException exception) {
                displayError(exception);
            }
        });
```

### <a name="auth_config_multiple_accountjson"></a>auth_config_multiple_account.json

這是使用多個帳戶的 MSAL 應用程式的組態檔。

如需這些欄位的說明，請參閱[了解 Android MSAL 組態檔](msal-configuration.md)。

不同於 [auth_config_single_account.json](#auth_config_single_accountjson) 組態檔，此組態檔具有 `"account_mode" : "MULTIPLE"` (而非 `"account_mode" : "SINGLE"`)，因為這是多重帳戶應用程式。

```json
{
  "client_id" : "<your_client_id_here>",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "<your_redirect_uri_here>",
  "account_mode" : "MULTIPLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

## <a name="next-steps"></a>後續步驟

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>了解建立本快速入門中所使用之應用程式的步驟

請試試看 Android 教學課程，以取得建置應用程式和新功能的完整逐步指南，且包括此快速入門的完整說明。

> [!div class="nextstepaction"]
> [呼叫 Graph API Android 教學課程](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>適用於 Android 的 MSAL 程式庫的 Wiki

閱讀適用於 Android 的 MSAL 程式庫的詳細資訊：

> [!div class="nextstepaction"]
> [適用於 Android 的 MSAL 程式庫的 Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

協助我們改善 Microsoft 身分識別平台。 完成問卷調查簡短的兩個問題，告訴我們您的想法。

> [!div class="nextstepaction"]
> [Microsoft 身分識別平台問卷調查](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
