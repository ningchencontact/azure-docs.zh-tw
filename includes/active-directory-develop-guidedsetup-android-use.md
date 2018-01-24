
## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>使用 MSAL 取得 Microsoft Graph API 的權杖

1.  在 [應用程式] > [Java] > [{domain}.{appname}] 之下，開啟 `MainActivity`。 
2.  新增下列匯入：

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
    ```

3. 以下列程式碼取代 `MainActivity` 類別：

    ```java
    public class MainActivity extends AppCompatActivity {

        final static String CLIENT_ID = "[Enter the application Id here]";
        final static String SCOPES [] = {"https://graph.microsoft.com/User.Read"};
        final static String MSGRAPH_URL = "https://graph.microsoft.com/v1.0/me";

        /* UI & Debugging Variables */
        private static final String TAG = MainActivity.class.getSimpleName();
        Button callGraphButton;
        Button signOutButton;

        /* Azure AD Variables */
        private PublicClientApplication sampleApp;
        private AuthenticationResult authResult;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
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
            sampleApp = null;
            if (sampleApp == null) {
                sampleApp = new PublicClientApplication(
                        this.getApplicationContext(),
                        CLIENT_ID);
            }

    /* Attempt to get a user and acquireTokenSilent
    * If this fails we do an interactive request
    */
            List<User> users = null;

            try {
                users = sampleApp.getUsers();

                if (users != null && users.size() == 1) {
            /* We have 1 user */

                    sampleApp.acquireTokenSilentAsync(SCOPES, users.get(0), getAuthSilentCallback());
                } else {
            /* We have no user */

            /* Let's do an interactive request */
                    sampleApp.acquireToken(this, SCOPES, getAuthInteractiveCallback());
                }
            } catch (MsalClientException e) {
                Log.d(TAG, "MSAL Exception Generated while getting users: " + e.toString());

            } catch (IndexOutOfBoundsException e) {
                Log.d(TAG, "User at this position does not exist: " + e.toString());
            }

        }

    //
    // App callbacks for MSAL
    // ======================
    // getActivity() - returns activity so we can acquireToken within a callback
    // getAuthSilentCallback() - callback defined to handle acquireTokenSilent() case
    // getAuthInteractiveCallback() - callback defined to handle acquireToken() case
    //

        public Activity getActivity() {
            return this;
        }

        /* Callback method for acquireTokenSilent calls 
        * Looks if tokens are in the cache (refreshes if necessary and if we don't forceRefresh)
        * else errors that we need to do an interactive request.
        */
        private AuthenticationCallback getAuthSilentCallback() {
            return new AuthenticationCallback() {
                @Override
                public void onSuccess(AuthenticationResult authenticationResult) {
                /* Successfully got a token, call Graph now */
                    Log.d(TAG, "Successfully authenticated");

                /* Store the authResult */
                    authResult = authenticationResult;

                /* call graph */
                    callGraphAPI();

                /* update the UI to post call Graph state */
                    updateSuccessUI();
                }

                @Override
                public void onError(MsalException exception) {
                /* Failed to acquireToken */
                    Log.d(TAG, "Authentication failed: " + exception.toString());

                    if (exception instanceof MsalClientException) {
                    /* Exception inside MSAL, more info inside MsalError.java */
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
                public void onSuccess(AuthenticationResult authenticationResult) {
                /* Successfully got a token, call graph now */
                    Log.d(TAG, "Successfully authenticated");
                    Log.d(TAG, "ID Token: " + authenticationResult.getIdToken());

                /* Store the auth result */
                    authResult = authenticationResult;

                /* call Graph */
                    callGraphAPI();

                /* update the UI to post call Graph state */
                    updateSuccessUI();
                }

                @Override
                public void onError(MsalException exception) {
                /* Failed to acquireToken */
                    Log.d(TAG, "Authentication failed: " + exception.toString());

                    if (exception instanceof MsalClientException) {
                    /* Exception inside MSAL, more info inside MsalError.java */
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

        /* Set the UI for successful token acquisition data */
        private void updateSuccessUI() {
            callGraphButton.setVisibility(View.INVISIBLE);
            signOutButton.setVisibility(View.VISIBLE);
            findViewById(R.id.welcome).setVisibility(View.VISIBLE);
            ((TextView) findViewById(R.id.welcome)).setText("Welcome, " +
                    authResult.getUser().getName());
            findViewById(R.id.graphData).setVisibility(View.VISIBLE);
        }

        /* Use MSAL to acquireToken for the end-user
        * Callback will call Graph api w/ access token & update UI
        */
        private void onCallGraphClicked() {
            sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
        }

        /* Handles the redirect from the System Browser */
        @Override
        protected void onActivityResult(int requestCode, int resultCode, Intent data) {
            sampleApp.handleInteractiveRequestRedirect(requestCode, resultCode, data);
        }

    }
    ```

<!--start-collapse-->
### <a name="more-information"></a>詳細資訊
#### <a name="get-a-user-token-interactively"></a>以互動方式取得使用者權杖
呼叫 `AcquireTokenAsync` 方法時會顯示一個視窗，提示使用者登入。 當使用者第一次需要存取受保護的資源時，應用程式通常會要求使用者以互動方式登入。 當取得權杖的無訊息作業失敗 (例如，使用者的密碼過期) 時，使用者也可能需要登入。

#### <a name="get-a-user-token-silently"></a>以無訊息方式取得使用者權杖
`AcquireTokenSilentAsync` 方法會處理權杖取得和更新作業，不需要與使用者進行任何互動。 在 `AcquireTokenAsync` 第一次執行之後，`AcquireTokenSilentAsync` 就會成為用來取得權杖的常用方法，以在後續呼叫中存取受保護的資源，因為系統會以無訊息方式進行呼叫來要求或更新權杖。

最後，`AcquireTokenSilentAsync` 方法會失敗。 失敗的原因可能是使用者已經登出，或已經在其他裝置上變更其密碼。 當 MSAL 偵測到可透過要求執行互動式動作來解決問題時，就會發出一個 `MsalUiRequiredException` 例外狀況。 您的應用程式可以透過兩種方式處理此例外狀況：

* 它可以立即對 `AcquireTokenAsync` 進行呼叫。 此呼叫會促使系統提示使用者登入。 此模式通常用於沒有離線內容可供使用者使用的線上應用程式。 此引導式設定所產生的範例會遵循此模式，您可以在第一次執行範例時看到它運作。 
    * 因為沒有任何使用者用過該應用程式，所以 `PublicClientApp.Users.FirstOrDefault()` 會包含一個 null 值，而且會擲回 `MsalUiRequiredException` 例外狀況。 
    * 然後範例中的程式碼會透過呼叫 `AcquireTokenAsync` 來處理例外狀況，進而提示使用者登入。 

* 其可改為對使用者呈現視覺指示，讓使用者知道需要透過互動方式登入，以便選取正確的登入時機。 或者，應用程式可以稍後重試 `AcquireTokenSilentAsync`。 此方式通常用於使用者可以使用其他應用程式功能，不需要因此中斷作業的情況，例如，應用程式中有離線內容可供使用時。 在此情況下，使用者可以決定何時登入以存取受保護的資源，或重新整理過期的資訊。 此外，當網路暫時無法使用而後還原時，應用程式可以決定是否重試 `AcquireTokenSilentAsync`。 
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>使用您剛剛取得的權杖呼叫 Microsoft Graph API
將下列方法加入至 `MainActivity` 類別：

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
        public Map<String, String> getHeaders() throws AuthFailureError {
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

/* Sets the Graph response */
private void updateGraphUI(JSONObject graphResponse) {
    TextView graphText = (TextView) findViewById(R.id.graphData);
    graphText.setText(graphResponse.toString());
}
```
<!--start-collapse-->
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>針對受保護 API 進行 REST 呼叫的相關詳細資訊

在這個範例應用程式中，`callGraphAPI` 會呼叫`getAccessToken`，接著針對需要權杖的資源發出 HTTP `GET` 要求，然後傳回內容。 此方法會在「HTTP 授權標頭」中新增取得的權杖。 對於此範例，資源為 Microsoft Graph API *me* 端點，它會顯示使用者的設定檔資訊。
<!--end-collapse-->

## <a name="set-up-sign-out"></a>設定登出

將下列方法加入至 `MainActivity` 類別：

```java
/* Clears a user's tokens from the cache.
 * Logically similar to "sign out" but only signs out of this app.
 */
private void onSignOutClicked() {

    /* Attempt to get a user and remove their cookies from cache */
    List<User> users = null;

    try {
        users = sampleApp.getUsers();

        if (users == null) {
            /* We have no users */

        } else if (users.size() == 1) {
            /* We have 1 user */
            /* Remove from token cache */
            sampleApp.remove(users.get(0));
            updateSignedOutUI();

        }
        else {
            /* We have multiple users */
            for (int i = 0; i < users.size(); i++) {
                sampleApp.remove(users.get(i));
            }
        }

        Toast.makeText(getBaseContext(), "Signed Out!", Toast.LENGTH_SHORT)
                .show();

    } catch (MsalClientException e) {
        Log.d(TAG, "MSAL Exception Generated while getting users: " + e.toString());

    } catch (IndexOutOfBoundsException e) {
        Log.d(TAG, "User at this position does not exist: " + e.toString());
    }
}

/* Set the UI for signed-out user */
private void updateSignedOutUI() {
    callGraphButton.setVisibility(View.VISIBLE);
    signOutButton.setVisibility(View.INVISIBLE);
    findViewById(R.id.welcome).setVisibility(View.INVISIBLE);
    findViewById(R.id.graphData).setVisibility(View.INVISIBLE);
    ((TextView) findViewById(R.id.graphData)).setText("No Data");
}
```
<!--start-collapse-->
### <a name="more-information-about-user-sign-out"></a>使用者登出的詳細資訊

前述程式碼中的 `onSignOutClicked` 方法會從 MSAL 使用者快取中移除使用者，這可有效告知 MSAL 忘記目前的使用者，只有將此作業設為互動式作業，未來取得權杖的要求才能成功。

雖然此範例中的應用程式支援單一使用者，MSAL 也支援可同時登入多個帳戶的案例。 例如，使用者擁有多個帳戶的電子郵件應用程式。
<!--end-collapse-->
