---
title: FarmBeats 的參考
description: ''
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: efd294910531509d736dbda274406bd7c801c124
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931207"
---
# <a name="references"></a>參考

以下是概述 Azure FarmBeats Api 的附注和指示集合。

## <a name="rest-api"></a>REST API

Azure FarmBeats Api 提供具有以 JSON 為基礎之回應的標準化 RESTful 介面給農業 business，這可協助您運用 Azure FarmBeats 功能：

- 用來取得感應器、攝影機、無人機、天氣、衛星和策劃基礎資料的 API。
- 標準化/coNtextualization 跨通用資料提供者的資料。
- 架構化所有內嵌資料的存取和查詢功能。
- 根據 agronomic 功能，自動產生可查詢的中繼資料。  
- 自動產生快速模型建立的時間序列匯總。
- 整合式 Azure Data Factory （ADF）引擎，可輕鬆地建立自訂資料處理管線。

## <a name="application-development"></a>XBOX Video Application Development

這些 Api 包含 Swagger 技術檔。 如需所有 Api 及其對應的要求/回應的相關資訊，請參閱[Swagger](https://aka.ms/FarmBeatsDatahubSwagger) 。

這是 FarmBeats 資料中樞內所有物件/資源的摘要：

領域 | 伺服器陣列會對應至 FarmBeats 系統內的相關實體位置。 每個伺服器陣列都有伺服器陣列名稱和唯一的伺服器陣列識別碼。
--- | ---|
裝置  | 裝置對應至存在於伺服器陣列中的實體裝置。 每個裝置都有唯一的裝置識別碼。 裝置通常會布建到具有伺服器陣列識別碼的伺服器陣列。
DeviceModel  | Devicemodel 傳遞會對應至裝置的中繼資料，例如 [製造商]、[閘道] 或 [節點] 的裝置類型。
感應器  | 感應器會對應到記錄值的實體感應器。 感應器通常會連接到裝置識別碼為的裝置。
SensorModel  | SensorModel 對應于感應器的中繼資料，例如製造商、感應器類型、類比或數位、感應器量值（例如，環境溫度、壓力等等）。
遙測  | 遙測可讓您讀取特定感應器和時間範圍的遙測訊息。
作業  | 作業會對應至活動的任何工作流程，這些活動會在 FarmBeats 系統中執行，以取得所需的輸出。 每項作業都與作業識別碼和作業類型相關聯。
JobType  | JobType 對應到系統支援的不同工作類型。 這包括系統定義的 & 使用者定義的作業類型。
ExtendedType  | ExtendedType 對應至系統中的系統 & 使用者定義類型的清單。 這有助於在 FarmBeats 系統中設定新的感應器或場景或 Scenefile 類型。
合作夥伴  | 合作夥伴對應至 FarmBeats 的感應器/影像整合合作夥伴
切換  | 場景會對應至伺服器陣列內容中產生的任何輸出。 每個場景都有一個場景識別碼、場景來源、場景類型，以及與其相關聯的伺服器陣列識別碼。 每個場景識別碼都可以有多個相關聯的場景檔案。
SceneFile |SceneFile 會對應至針對單一場景產生的所有檔案。 單一場景識別碼可以有多個相關聯的 SceneFile 識別碼。
規則  |規則會對應至伺服器陣列相關資料的條件，以觸發警示。 每個規則都會在伺服器陣列資料的內容中。
警示  | 警示會對應到通知，這會在符合規則條件時產生。 每個警示都會在規則的內容中。
RoleDefinition  | RoleDefinition 會定義角色允許和不允許的動作。
RoleAssignment  |RoleAssignment 對應至使用者或服務主體的角色指派。

**資料格式**

JSON （JavaScript 物件標記法）是與語言無關的通用資料格式，可提供任意資料結構的簡單文字標記法。 如需詳細資訊，請參閱 json.org。

## <a name="authentication-and-authorization"></a>驗證和授權

REST API 的 HTTP 要求會使用 Azure Active Directory （Azure AD）來保護。
若要向 REST Api 提出已驗證的要求，用戶端程式代碼必須先使用有效的認證進行驗證，然後才能呼叫 API。 驗證會在各種動作專案之間進行協調，Azure AD，並為您的用戶端提供存取權杖，做為驗證的證明。 然後，權杖會在 REST API 要求的 HTTP 授權標頭中傳送。 若要深入瞭解 Azure AD 驗證，請參閱適用于開發人員的[Azure Active Directory](https://portal.azure.com) 。

存取權杖必須在標頭區段中的後續 API 要求中傳送，如下所示：

```
headers = {"Authorization": "Bearer " + **access_token**}
```

**HTTP 要求標頭**

以下是對 Azure FarmBeats 資料中樞進行 API 呼叫時，需要指定的最常見要求標頭：


**標頭** | **描述和範例**
--- | ---
Content-Type  | 適用于 Azure FarmBeats 資料中樞 Api 格式的要求格式（內容類型： application/<format>）為 json。 內容類型： application/json
授權  | 指定進行 API 呼叫所需的存取權杖。 **授權：持有人 < 存取權杖 >**
Accept | 回應格式。 針對 Azure FarmBeats 資料中樞 Api，格式為 json **Accept： application/json**

**API 要求**

若要提出 REST API 要求，請結合 HTTP （GET、POST、PUT 或 DELETE）方法、API 服務的 URL、要查詢的資源 URI、提交資料至、更新或刪除，以及一或多個 HTTP 要求標頭。

API 服務的 URL 是您的資料中樞 URL HTTPs://\<yourdatahub-網站名稱 >。您可以選擇在 GET 呼叫上包含查詢參數來篩選、限制的大小，以及排序回應中的資料。

以下範例要求是取得裝置的清單：

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

大部分的 GET、POST 和 PUT 呼叫都需要 JSON 要求主體。

以下範例要求是建立裝置（這具有具有要求主體的輸入 json）。

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

**查詢參數**

對於 REST **GET**呼叫，您可以在要求 URI 上包含一或多個查詢參數，以篩選、限制和排序 API 回應中的資料。 如需查詢參數，請參閱 API 檔，並查看個別的 GET 呼叫。
例如，查詢裝置清單時（/Device 上的 GET 呼叫），可以指定下列查詢參數：  

![專案伺服器陣列的節拍](./media/for-references/query-parameters-device-1.png)

**錯誤處理**

Azure FarmBeats 資料中樞 API 會傳回標準 HTTP 錯誤。 最常見的錯誤碼如下：

 |錯誤碼             | 描述 |
 |---                    | --- |
 |200                    | 成功 |
 |201                    | 建立（Post）成功 |
 |400                    | 不正確的要求。 要求中有錯誤 |
 |401                    | 未經授權。 API 的呼叫者未獲授權，無法存取資源 |
 |404                    | 找不到資源 |
 |5XX                    | 內部伺服器錯誤。 以5XX 開頭的錯誤碼表示伺服器上有一些錯誤。 如需詳細資訊，請參閱伺服器記錄和下一節。 |


除了標準 HTTP 錯誤以外，Azure FarmBeats 資料中樞 Api 也會以下列格式傳回內部錯誤：

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

範例：建立伺服器陣列時，未在輸入裝載中指定強制欄位 "Name"。 產生的錯誤訊息會是：

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="adding-users-or-app-registrations-to-azure-active-directory"></a>將使用者或應用程式註冊新增至 Azure Active Directory

Azure Active Directory 中的使用者或應用程式註冊可以存取 Azure FarmBeats Api。 若要在您的 Azure Active Directory 上建立應用程式註冊，請執行下列步驟：  

1. 移至[Azure 入口網站](https://portal.azure.com) **Azure Active Directory，應用程式註冊**，>**新的註冊**。 或者，您可以使用現有的帳戶。
2. 針對新帳戶，請確定完成下列步驟：

    - 輸入名稱
    - **僅選取此組織目錄中的帳戶（單一租使用者）**
    - 其餘欄位中的預設值
    - 選取 [**註冊**]

3. 從新的/現有的應用程式註冊，**總覽**，完成下列動作

    - 捕捉**用戶端識別碼**和**租使用者識別碼**。
    - 移至 [**憑證和密碼**] 以產生新的用戶端密碼，並捕捉**用戶端密碼**。
    - 返回 [總覽]，然後按一下 [**管理本機目錄中的應用程式**] 旁的連結
    - 移至 [**屬性**] 以捕獲**物件識別碼**

4. 移至您的[資料中樞 Swagger](https://<yourdatahub>.azurewebsites.net/swagger/index.html) ，然後執行下列步驟：
    - 流覽至**ROLEASSIGNMENT API**
    - 執行**POST** ，為您剛建立的**物件識別碼**建立 RoleAssignment。 –輸入 json 是：

  > [!NOTE]
  > 如需新增使用者和 AD 註冊的詳細資訊，請參閱[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) 。

完成上述步驟之後，您的應用程式註冊（用戶端）就可以透過持有人驗證，使用存取權杖來呼叫 Azure FarmBeats Api。  

使用存取權杖，在標頭區段的後續 API 要求中傳送它，如下所示：

```
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
