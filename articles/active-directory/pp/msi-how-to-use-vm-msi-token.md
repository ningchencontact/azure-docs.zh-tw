---
title: 如何使用使用者指派的受控服務識別來取得虛擬機器上的存取權杖。
description: 使用來自 Azure 虛擬機器的使用者指派 MSI，取得 OAuth 存取權杖的逐步指示和範例。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 6c6422bc2b13c0c40e48dabf0470c821b13e7851
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="acquire-an-access-token-for-a-vm-user-assigned-managed-service-identity-msi"></a>取得虛擬機器使用者指派之受控服務識別 (MSI) 的存取權杖

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]
本文提供各種取得權杖的程式碼和指令碼，以及處理權杖到期和 HTTP 錯誤等重要主題的指引。

## <a name="prerequisites"></a>先決條件
[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]
如果您打算使用本文中的 Azure PowerShell 範例，請務必安裝最新版的 [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM)。

> [!IMPORTANT]
> - 本文中的所有範例程式碼/指令碼都假設用戶端在已啟用 MSI 的虛擬機器上執行。 在 Azure 入口網站中使用虛擬機器「連線」功能，從遠端連線到您的虛擬機器。 如需有關在虛擬機器上啟用 MSI 的詳細資訊，請參閱[使用 Azure CLI 為虛擬機器設定使用者指派的受控服務識別 (MSI)](msi-qs-configure-cli-windows-vm.md)，或其中一篇變化文章 (使用入口網站、PowerShell、範本或 Azure SDK)。 

## <a name="overview"></a>概觀

用戶端應用程式可以要求用於存取指定資源的 MSI [僅限應用程式存取權杖](~/articles/active-directory/develop/active-directory-dev-glossary.md#access-token)。 此權杖是[以 MSI 服務主體為基礎](msi-overview.md#how-does-it-work)。 因此，用戶端不需要自行註冊就能取得本身服務主體下的存取權杖。 權杖在[需要用戶端認證的服務對服務呼叫](~/articles/active-directory/active-directory-protocols-oauth-service-to-service.md)中適合作為持有人權杖。

|  |  |
| -------------- | -------------------- |
| [使用 HTTP 取得權杖](#get-a-token-using-http) | MSI 權杖端點的通訊協定詳細資料 |
| [使用 CURL 取得權杖](#get-a-token-using-curl) | 從 Bash/CURL 用戶端使用 MSI REST 端點的範例 |
| [處理權杖到期](#handling-token-expiration) | 處理過期存取權杖的指引 |
| [錯誤處理](#error-handling) | 從 MSI 權杖端點傳回 HTTP 錯誤的處理指引 |
| [節流指引](#throttling-guidance) | 處理 MSI 權杖端點之節流的指引 |
| [Azure 服務的資源識別碼](#resource-ids-for-azure-services) | 取得所支援 Azure 服務資源識別碼的地方 |


## <a name="get-a-token-using-http"></a>使用 HTTP 取得權杖 

取得存取權杖的基本介面是以 REST 為基礎，如此可讓用戶端應用程式在可執行 HTTP REST 呼叫的虛擬機器上執行時，可以對其進行存取。 這類似於 Azure AD 的程式設計模型，但用戶端會使用虛擬機器上的端點 (對比於 Azure AD 端點)。

使用執行個體中繼資料服務 (IMDS) 端點的範例要求：

```
GET http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id=712eac09-e943-418c-9be6-9fd5c91078bl HTTP/1.1 Metadata: true
```

使用 MSI VM 延伸模組端點 (即將被取代) 的範例要求：

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id=712eac09-e943-418c-9be6-9fd5c91078bl HTTP/1.1 Metadata: true
```

| 元素 | 說明 |
| ------- | ----------- |
| `GET` | HTTP 指令動詞，指出您想要擷取端點中的資料。 在此案例中是 OAuth 存取權杖。 | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | 執行個體中繼資料服務的 MSI 端點。 |
| `http://localhost:50342/oauth2/token` | VM 延伸模組的 MSI 端點，其中 50342 是預設連接埠，且可設定。 |
| `api-version`  | 一個查詢字串參數，指出 IMDS 端點的 API 版本。  |
| `resource` | 查詢字串參數，指出目標資源的應用程式識別碼 URI。 也會出現在所核發權杖的 `aud` (對象) 宣告中。 此範例會要求用來存取 Azure Resource Manager 的權杖，其中包含應用程式識別碼 URI https://management.azure.com/。 |
| `client_id` |  一個*選用*查詢字串參數，指出代表使用者指派 MSI 之服務主體的用戶端識別碼 (也稱為應用程式識別碼)。 如果您使用系統指派的 MSI，就不需要此參數。 在建立使用者指派的 MSI 期間，這個值會在 `clientId` 屬性中傳回。 此範例會要求用戶端識別碼 "712eac09-e943-418 c-9be6-9fd5c91078bl" 的權杖。 |
| `Metadata` | HTTP 要求標頭欄位，MSI 需此元素以減輕伺服器端偽造要求 (SSRF) 攻擊。 此值必須設定為 "true" (全部小寫)。

範例回應：

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
  "client_id":"712eac09-e943-418c-9be6-9fd5c91078bl"
}
```

| 元素 | 說明 |
| ------- | ----------- |
| `access_token` | 所要求的存取權杖。 呼叫受保護的 REST API 時，權杖會內嵌在 `Authorization` 要求標頭欄位中成為「持有人」權杖，以允許 API 驗證呼叫端。 | 
| `expires_in` | 存取權杖從發行到過期之前持續有效的秒數。 在權杖的 `iat` 宣告中可找到發行時間。 |
| `expires_on` | 存取權杖到期的時間範圍。 日期以 "1970-01-01T0:0:0Z UTC" 起算的秒數表示 (對應至權杖的 `exp` 宣告)。 |
| `not_before` | 存取權杖生效且可被接受的時間範圍。 日期以 "1970-01-01T0:0:0Z UTC" 起算的秒數表示 (對應至權杖的 `nbf` 宣告)。 |
| `resource` | 要求存取權杖所針對的資源，符合要求的 `resource` 查詢字串參數。 |
| `token_type` | 權杖的類型，即「持有人」存取權杖，表示此權杖的持有人可以存取資源。 |
| `client_id` | 代表使用者指派 MSI 之服務主體 (已對其要求權杖) 的用戶端識別碼 (也稱為應用程式識別碼)。 |

## <a name="get-a-token-using-curl"></a>使用 CURL 取得權杖

務必以使用者指派 MSI 之服務主體的用戶端識別碼 (也稱為應用程式識別碼)，取代 `client_id` 參數的 <MSI CLIENT ID> 值。 在建立使用者指派的 MSI 期間，這個值會在 `clientId` 屬性中傳回。
  
使用執行個體中繼資料服務 (IMDS) 端點的範例要求：

   ```bash
   response=$(curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<MSI CLIENT ID>")
   access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
   echo The MSI access token is $access_token
   ```
   
使用 MSI VM 延伸模組端點 (即將被取代) 的範例要求：

   ```bash
   response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/&client_id=<MSI CLIENT ID>" -H Metadata:true -s)
   access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
   echo The MSI access token is $access_token
   ```

   範例回應：

   ```bash
   user@vmLinux:~$ response=$(curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=9d484c98-b99d-420e-939c-z585174b63bl")
   user@vmLinux:~$ access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
   user@vmLinux:~$ echo The MSI access token is $access_token
   The MSI access token is eyJ0eXAiOiJKV1QiLCJhbGciO...
   ```

## <a name="handling-token-expiration"></a>處理權杖到期

MSI 子系統會快取權杖。 因此，您可以視需要呼叫它，而只有在下列情況，線上呼叫 Azure AD 才會有結果：
- 由於快取中沒有權杖而發生快取遺漏
- 權杖已過期

如果您在程式碼中快取權杖，則應該妥善處理資源指出權杖過期的情節。

## <a name="error-handling"></a>錯誤處理 

MSI 端點會透過 HTTP 回應訊息標頭的狀態碼欄位 (如 4xx 或 5xx 錯誤) 來發出錯誤通知：

| 狀態碼 | 錯誤原因 | 處理方式 |
| ----------- | ------------ | ------------- |
| 要求中的 4xx 錯誤。 | 一個或多個要求參數不正確。 | 請勿重試。  檢查錯誤詳細資料以取得更多資訊。  4xx 錯誤是設計階段錯誤。|
| 來自服務的 5xx 暫時性錯誤。 | MSI 子系統或 Azure Active Directory 傳回暫時性錯誤。 | 等待至少一秒後即可安全地進行重試。  如果您太快重試或重試太多次，Azure AD 可能傳回速率限制錯誤 (429)。|

如果發生錯誤，對應的 HTTP 回應主體會包含 JSON 格式的錯誤詳細資料：

| 元素 | 說明 |
| ------- | ----------- |
| 錯誤   | 錯誤識別碼。 |
| error_description | 錯誤的詳細資訊描述。 **錯誤描述可以隨時變更。請勿將程式碼撰寫為會針對錯誤描述中的值建立分支。**|

### <a name="http-response-reference"></a>HTTP 回應參考

本節會說明可能的錯誤回應。 「200 確定」狀態是成功的回應，而且存取權杖會包含在回應主體 JSON 中 (在 access_token 元素中)。

| 狀態碼 | Error | 錯誤說明 | 解決方法 |
| ----------- | ----- | ----------------- | -------- |
| 400 不正確的要求 | invalid_resource | AADSTS50001：在名為 \<TENANT-ID\> 的租用戶中找不到名為 \<URI\> 的應用程式。 如果租用戶的系統管理員尚未安裝此應用程式或租用戶中的任何使用者尚未同意使用此應用程式，也可能會發生此錯誤。 您可能會將驗證要求傳送至錯誤的租用戶。\ | (僅限 Linux) |
| 400 不正確的要求 | bad_request_102 | 未指定必要的中繼資料標頭 | 要求中遺漏 `Metadata` 要求標頭欄位，或欄位的格式不正確。 值必須指定為 `true` (全部小寫)。 如需相關範例，請參閱[使用 HTTP 取得權杖](#get-a-token-using-http)一節中的「範例要求」。|
| 401 未經授權 | unknown_source | 未知的來源 *\<URI\>* | 請確認 HTTP GET 要求 URI 的格式正確。 `scheme:host/resource-path` 部分必須指定為 `http://169.254.169.254/metadata/identity/oath2/token` 或 `http://localhost:50342/oauth2/token`。 如需相關範例，請參閱[使用 HTTP 取得權杖](#get-a-token-using-http)一節中的「範例要求」。|
|           | invalid_request | 要求遺漏必要參數、包含無效參數值、多次包含某個參數或格式不正確。 |  |
|           | unauthorized_client | 用戶端無權使用此方法要求存取權杖。 | 因為要求並未使用本機回送呼叫延伸模組，或是所在的虛擬機器沒有正確設定 MSI。 如果您需要設定虛擬機器的協助，請參閱[使用 Azure 入口網站設定虛擬機器受控服務識別 (MSI)](msi-qs-configure-portal-windows-vm.md)。 |
|           | access_denied | 資源擁有者或授權伺服器已拒絕要求。 |  |
|           | unsupported_response_type | 授權伺服器不支援使用此方法取得存取權杖。 |  |
|           | invalid_scope | 要求的範圍無效、未知或格式不正確。 |  |
| 500 內部伺服器錯誤 | 未知 | 無法從 Active 目錄擷取權杖。 如需詳細資訊，請參閱\<檔案路徑\>中的記錄 | 請確認虛擬機器上已正確啟用 MSI。 如果您需要設定虛擬機器的協助，請參閱[使用 Azure 入口網站設定虛擬機器受控服務識別 (MSI)](msi-qs-configure-portal-windows-vm.md)。<br><br>也請確認 HTTP GET 要求 URI 的格式正確，尤其是查詢字串中指定的資源 URI。 請參閱[使用 HTTP 取得權杖](#get-a-token-using-http)一節中的「範例要求」，以取得相關範例，或請參閱[支援 Azure AD 驗證的 Azure 服務](msi-overview.md#azure-services-that-support-azure-ad-authentication)，以取得服務及其各自資源識別碼的清單。

## <a name="throttling-guidance"></a>節流指引 

節流限制會套用至對 MSI IMDS 端點進行的呼叫數目。 超過節流閾值時，MSI IMDS 端點會在節流生效時，限制任何進一步的要求。 在這段期間，MSI IMDS 端點會傳回 HTTP 狀態碼 429 (「太多要求」)，且要求會失敗。 

對於重試，我們建議下列策略： 

| **重試策略** | **設定** | **值** | **運作方式** |
| --- | --- | --- | --- |
|ExponentialBackoff |重試計數<br />最小輪詢<br />最大輪詢<br />差異輪詢<br />第一個快速重試 |5<br />0 秒<br />60 秒<br />2 秒<br />false |嘗試 1 - 延遲 0 秒<br />嘗試 2 - 延遲 ~2 秒<br />嘗試 3 - 延遲 ~6 秒<br />嘗試 4 - 延遲 ~14 秒<br />嘗試 5 - 延遲 ~30 秒 |

## <a name="resource-ids-for-azure-services"></a>Azure 服務的資源識別碼

關於支援 Azure AD 且經過 MSI 測試的資源及其各自資源識別碼的清單，請參閱[支援 Azure AD 驗證的 Azure 服務](msi-overview.md#azure-services-that-support-azure-ad-authentication)。


## <a name="next-steps"></a>後續步驟

- 若要在 Azure 虛擬機器上啟用 MSI，請參閱[使用 Azure CLI 為虛擬機器設定使用者指派的受控服務識別 (MSI)](msi-qs-configure-cli-windows-vm.md)。

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。








