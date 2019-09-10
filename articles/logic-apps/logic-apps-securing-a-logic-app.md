---
title: 保護存取和資料-Azure Logic Apps
description: 在 Azure Logic Apps 中保護參數輸入、HTTP 要求觸發程式、執行歷程記錄、邏輯應用程式作業, 以及與其他服務的連接。
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: 6c16b38cce31c45158a5871c10dbd01339da9203
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845428"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>保護 Azure Logic Apps 中的存取和資料

若要控制存取和保護 Azure Logic Apps 中的資料, 您可以在下欄區域中設定安全性:

* [HTTP 要求觸發程式的存取權](#secure-triggers)
* [存取邏輯應用程式作業](#secure-operations)
* [執行歷程記錄輸入和輸出的存取權](#secure-run-history)
* [存取參數輸入](#secure-action-parameters)
* [存取從邏輯應用程式呼叫的服務和系統](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-http-request-triggers"></a>HTTP 要求觸發程式的存取權

當您的邏輯應用程式使用 HTTP 要求型觸發程式 (例如[要求](../connectors/connectors-native-reqres.md)或[Webhook](../connectors/connectors-native-webhook.md)觸發程式) 時, 您可以限制存取權, 讓只有經過授權的用戶端可以啟動您的邏輯應用程式。 邏輯應用程式收到的所有要求，都會使用安全通訊端層 (SSL) 通訊協定加密並保護。 

以下是您可以保護此觸發程式類型之存取的方式:

* [產生共用存取簽章](#sas)
* [限制連入 IP 位址](#restrict-incoming-ip-addresses)
* [新增 Azure Active Directory、OAuth 或其他安全性](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>產生共用存取簽章 (SAS)

邏輯應用程式上的每個要求端點在端點的 URL 中都有[共用存取簽章 (SAS)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) , 其遵循此格式:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

每個 URL 都`sp`包含`sv`、和`sig`查詢參數, 如下表所述:

| 查詢參數 | 描述 |
|-----------------|-------------|
| `sp` | 指定允許的 HTTP 方法所要使用的許可權。 |
| `sv` | 指定要用於產生簽章的 SAS 版本。 |
| `sig` | 指定要用來驗證觸發程式存取權的簽章。 此簽章的產生方式是在所有 URL 路徑和屬性上使用 SHA256 演算法搭配秘密存取金鑰。 永不公開或發佈, 此金鑰會保持加密狀態, 並與邏輯應用程式一起儲存。 您的邏輯應用程式只會針對包含使用祕密金鑰建立之有效簽章的觸發程序進行授權。 |
|||

如需使用共用存取簽章保護存取權的詳細資訊, 請參閱本主題中的下列各節:

* [重新產生存取金鑰](#access-keys)
* [建立即將到期的回呼 URL](#expiring-urls)
* [使用主要或次要金鑰建立 URL](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>重新產生存取金鑰

若要隨時產生新的安全存取金鑰, 請使用 Azure REST API 或 Azure 入口網站。 所有先前產生的使用舊金鑰的 Url 都會失效, 且不再有觸發邏輯應用程式的授權。 您在重新產生之後取得的 Url 會使用新的存取金鑰進行簽署。

1. 在 Azure 入口網站中，開啟具有要重新產生金鑰的邏輯應用程式。

1. 在邏輯應用程式功能表的 [設定] 下，選取 [存取金鑰]。

1. 選取您要重新產生的金鑰, 並完成程式。

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>建立即將過期的回呼 Url

如果您與其他人共用以 HTTP 要求為基礎之觸發程式的端點 URL, 您可以產生使用特定金鑰並具有到期日期的回呼 Url。 如此一來, 您就可以根據特定時間範圍, 順暢地變換金鑰或限制觸發邏輯應用程式的存取。 若要指定 URL 的到期日, 請使用[Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers), 例如:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

在主體中, 使用 JSON `NotAfter`日期字串包含屬性。 此屬性會傳回只在 `NotAfter` 日期與時間之前有效的回呼 URL。

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>使用主要或次要祕密金鑰建立 URL

當您產生或列出 HTTP 要求型觸發程式的回呼 Url 時, 您可以指定要用來簽署 URL 的金鑰。 若要產生以特定金鑰簽署的 URL, 請使用[Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers), 例如:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

在本文中，將屬性 `KeyType` 包含為 `Primary` 或 `Secondary`。 此屬性會傳回使用指定安全金鑰簽署的 URL。

<a name="restrict-incoming-ip"></a>

### <a name="restrict-incoming-ip-addresses"></a>限制傳入的 IP 位址

除了共用存取簽章, 您可能會想要限制可以呼叫邏輯應用程式的特定用戶端。 例如, 如果您使用 Azure API 管理來管理要求端點, 您可以限制邏輯應用程式僅接受來自 API 管理實例之 IP 位址的要求。

#### <a name="restrict-incoming-ip-ranges-in-azure-portal"></a>限制 Azure 入口網站中的傳入 IP 範圍

1. 在 Azure 入口網站的「邏輯應用程式設計工具」中，開啟邏輯應用程式。

1. 在邏輯應用程式功能表的 [設定] 底下，選取 [工作流程設定]。

1. 在 [存取控制設定] > [允許的輸入 IP 位址] 底下，選取 [特定 IP 範圍]。

1. 在 [觸發程序的 IP 範圍] 底下，指定觸發程序可接受的 IP 位址範圍。

   有效的 IP 範圍使用這些格式：*x.x.x.x/x* 或 *x.x.x.x-x.x.x.x*

如果您想要讓邏輯應用程式僅觸發為嵌套邏輯應用程式, 請從 [**允許的輸入 IP 位址**] 清單中, 只選取 [**其他 Logic Apps**]。 此選項會將空陣列寫入您的邏輯應用程式資源。 如此一來, 只有來自 Logic Apps 服務 (父邏輯應用程式) 的呼叫才能觸發嵌套邏輯應用程式。

> [!NOTE]
> 不論 IP 位址為何, 您仍然可以使用`/triggers/<trigger-name>/run`透過 Azure REST API 或透過 API 管理, 來執行具有 HTTP 要求型觸發程式的邏輯應用程式。 不過, 此案例仍然需要對 Azure REST API 進行驗證。 所有事件都會出現在 Azure Audit 記錄中。 請確定您已適當地設定存取控制原則。

#### <a name="restrict-incoming-ip-ranges-in-azure-resource-manager-template"></a>限制 Azure Resource Manager 範本中的傳入 IP 範圍

如果您使用[Azure Resource Manager 範本](../logic-apps/logic-apps-create-deploy-template.md)將邏輯應用程式部署自動化, 您可以在邏輯應用程式的資源定義`accessControl`中使用區段`triggers`搭配區段來指定 IP 範圍, 例如:

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     },
                     {
                        "addressRange": "2001:0db8::/64"
                     }
                  ]
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-oauth-or-other-security"></a>新增 Azure Active Directory、OAuth 或其他安全性

若要將更多授權通訊協定新增到您的邏輯應用程式，請考慮使用 [Azure API 管理](../api-management/api-management-key-concepts.md)。 這項服務可讓您將邏輯應用程式公開為 API, 並為任何端點提供豐富的監視、安全性、原則和檔。 API 管理可針對邏輯應用程式將公用或私人端點公開，並且可以使用 Azure Active Directory、OAuth 憑證或其他的安全性標準。 當 API 管理收到要求時，服務會傳送要求至您的邏輯應用程式，也會在過程中進行任何必要的轉換或限制。 若要只讓 API 管理觸發您的邏輯應用程式，您可以使用邏輯應用程式的連入 IP 範圍設定。

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>存取邏輯應用程式作業

您只能允許特定的使用者或群組執行特定作業, 例如管理、編輯和觀看邏輯應用程式。 若要控制其許可權, 請使用[Azure 角色型存取控制 (RBAC)](../role-based-access-control/role-assignments-portal.md) , 將自訂或內建角色指派給 azure 訂用帳戶中的成員:

* [邏輯應用程式參與者](../role-based-access-control/built-in-roles.md#logic-app-contributor)：可讓您管理邏輯應用程式, 但無法變更其存取。

* [邏輯應用程式操作員](../role-based-access-control/built-in-roles.md#logic-app-operator)：可讓您讀取、啟用及停用邏輯應用程式, 但無法編輯或更新它們。

若要防止其他人變更或刪除您的邏輯應用程式, 您可以使用[Azure 資源鎖定](../azure-resource-manager/resource-group-lock-resources.md), 以防止其他人變更或刪除生產資源。

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>執行歷程記錄資料的存取權

在邏輯應用程式執行期間, 所有資料都會在傳輸過程中使用[傳輸層安全性 (TLS)](https://azure.microsoft.com/updates/app-service-and-functions-hosted-apps-can-now-update-tls-versions/)和[待用](../security/fundamentals/encryption-atrest.md)加密。 當您的邏輯應用程式完成執行時, 您可以查看該回合的歷程記錄, 包括與每個動作的狀態、持續時間、輸入和輸出一起執行的步驟。 此豐富的詳細資料可讓您深入瞭解邏輯應用程式的執行方式, 以及您可以從何處開始針對發生的任何問題進行疑難排解。

當您存取邏輯應用程式的執行歷程記錄時, Logic Apps 會驗證您的存取權, 並從邏輯應用程式的執行中提供要求和回應的輸入和輸出連結。 不過, 針對處理任何密碼、秘密、金鑰或其他機密資訊的動作, 您會想要防止其他人查看和存取該資料。 例如, 如果您的邏輯應用程式從[Azure Key Vault](../key-vault/key-vault-whatis.md)取得要在驗證 HTTP 動作時使用的密碼, 您就會想要從 view 中隱藏該秘密。

若要在邏輯應用程式的執行歷程記錄中控制輸入和輸出的存取權, 您有下列選項:

* [依 IP 位址範圍限制存取](#restrict-ip)。

  此選項可讓您根據特定 IP 位址範圍的要求來保護對執行歷程記錄的存取。

* [使用混淆來隱藏執行歷程記錄中的資料](#obfuscate)。

  在許多觸發程式和動作中，您可以從邏輯應用程式的執行歷程記錄隱藏其輸入、輸出或兩者。

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>依 IP 位址範圍限制存取

您可以限制對邏輯應用程式執行歷程記錄中的輸入和輸出的存取, 如此一來, 只有來自特定 IP 位址範圍的要求才能看到該資料。 例如, 若要封鎖任何人存取輸入和輸出, 請指定 IP 位址範圍, 例如`0.0.0.0-0.0.0.0`。 只有具備系統管理員許可權的人員可以移除此限制, 這可讓您對邏輯應用程式的資料進行「即時」存取的可能性。 您可以使用 Azure 入口網站或在用於邏輯應用程式部署的 Azure Resource Manager 範本中, 指定要限制的 IP 範圍。

#### <a name="restrict-ip-ranges-in-azure-portal"></a>限制 Azure 入口網站中的 IP 範圍

1. 在 Azure 入口網站的「邏輯應用程式設計工具」中，開啟邏輯應用程式。

1. 在邏輯應用程式功能表的 [設定] 底下，選取 [工作流程設定]。

1. 在 [存取控制設定] > [允許的輸入 IP 位址] 底下，選取 [特定 IP 範圍]。

1. 在 [內容的 IP 範圍] 底下，指定可存取輸入和輸出內容的 IP 位址範圍。 

   有效的 IP 範圍使用這些格式：*x.x.x.x/x* 或 *x.x.x.x-x.x.x.x*

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>限制 Azure Resource Manager 範本中的 IP 範圍

如果您使用[Azure Resource Manager 範本](../logic-apps/logic-apps-create-deploy-template.md)將邏輯應用程式部署自動化, 您可以在邏輯應用程式的資源定義`accessControl`中使用區段`contents`搭配區段來指定 IP 範圍, 例如:

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "contents": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     },
                     {
                        "addressRange": "2001:0db8::/64"
                     }
                  ]
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="obfuscate"></a>

### <a name="hide-data-from-run-history-by-using-obfuscation"></a>使用混淆來隱藏執行歷程記錄中的資料

許多觸發程式和動作都具有從邏輯應用程式的執行歷程記錄中隱藏輸入、輸出或兩者的設定。 當您使用這些設定來保護這項資料時，以下是一些[要審查的考慮事項](#obfuscation-considerations)。

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>在設計工具中保護輸入和輸出

1. 如果您的邏輯應用程式尚未在[Azure 入口網站](https://portal.azure.com)中開啟, 請在邏輯應用程式設計工具中開啟邏輯應用程式。

   ![開啟範例邏輯應用程式](media/logic-apps-securing-a-logic-app/sample-logic-app.png)

1. 在您要保護資料的觸發程式或動作上, 選取省略號 ( **...** ) 按鈕, 然後選取 [**設定**]。

   ![開啟 [設定]](media/logic-apps-securing-a-logic-app/open-settings.png)

1. 開啟**安全輸入**、**保護輸出**或兩者。 完成之後，選取 [完成]。

   ![開啟安全的輸入或輸出](media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   動作或觸發程式現在會在標題列中顯示鎖定圖示。

   ![標題列上的鎖定圖示](media/logic-apps-securing-a-logic-app/title-bar-lock-icon.png)

   代表先前動作之受保護輸出的權杖也會顯示鎖定圖示。 例如, 當您從動態內容清單中選取要在動作中使用的輸出時, 該權杖會顯示鎖定圖示。

   ![選取輸出](media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. 邏輯應用程式執行之後, 您可以查看該執行的歷程記錄。

   1. 在邏輯應用程式的 **[總覽**] 窗格中, 選取您要查看的執行。

   1. 在 [**邏輯應用程式執行**] 窗格中, 展開您想要檢查的動作。

      如果您選擇保護輸入和輸出, 那些值現在會顯示為隱藏。

      ![執行歷程記錄中的隱藏資料](media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>在程式碼視圖中保護輸入和輸出

在基礎觸發程式或動作定義中，使用下列其中`runtimeConfiguration.secureData.properties`一個或兩個值來新增或更新陣列：

* `"inputs"`:保護執行歷程記錄中的輸入。
* `"outputs"`:保護執行歷程記錄中的輸出。

當您使用這些設定來保護這項資料時，以下是一些[要審查的考慮事項](#obfuscation-considerations)。

```json
"<trigger-or-action-name>": {
   "type": "<trigger-or-action-type>",
   "inputs": {
      <trigger-or-action-inputs>
   },
   "runtimeConfiguration": {
      "secureData": {
         "properties": [
            "inputs",
            "outputs"
         ]
      }
   },
   <other-attributes>
}
```

<a name="obfuscation-considerations"></a>

#### <a name="considerations-when-hiding-inputs-and-outputs"></a>隱藏輸入和輸出時的考慮

* 當您保護觸發程式或動作的輸入或輸出時, Logic Apps 不會將受保護的資料傳送至 Azure Log Analytics。 此外, 您也無法將[追蹤的屬性](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details)新增至該觸發程式或動作以進行監視。

* [用於處理工作流程歷程記錄的 LOGIC APPS API](https://docs.microsoft.com/rest/api/logic/)不會傳回安全的輸出。

* 在您保護輸入或明確使用安全輸出的動作中, 此動作會傳回包含此受保護資料之輸出的回應, 您必須手動開啟此動作中的**安全輸出**, 以保護這些輸出。

* 請確定您在下游動作中開啟**安全輸入**或**安全輸出**, 其中您預期執行歷程記錄會保護該資料。

  **安全輸出設定**

  當您以手動方式開啟觸發程式或動作中的**安全輸出**時, Logic Apps 會在執行歷程記錄中保護這些輸出。 如果下游動作明確使用這些受保護的輸出做為輸入, Logic Apps 會在執行歷程記錄中隱藏此動作的輸入, 但*不會啟用*動作的「**安全輸入**」設定。

  ![受保護的輸出做為輸入和下游對大部分動作的影響](media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  撰寫、剖析 JSON 和回應動作只有**安全輸入**設定。 開啟時, 此設定也會隱藏這些動作的輸出。 如果這些動作明確使用上游保護的輸出做為輸入, Logic Apps 會隱藏這些動作的輸入和輸出, 但*不會啟用*這些動作的**安全輸入**設定。 如果下游動作明確使用撰寫、剖析 JSON 或回應動作中的隱藏輸出做為輸入, Logic Apps*不會隱藏此下游動作的輸入或輸出*。

  ![受保護的輸出作為具有對特定動作之下游影響的輸入](media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **安全輸入設定**

  當您以手動方式開啟觸發程式或動作中的**安全輸入**時, Logic Apps 會在執行歷程記錄中保護這些輸入。 如果下游動作明確使用該觸發程式或動作的可見輸出做為輸入, Logic Apps 會在執行歷程記錄中隱藏此下游動作的輸入, 但*不會*在此動作中啟用**安全輸入**, 也不會隱藏此動作的產出.

  ![受保護的輸入和下游對大部分動作的影響](media/logic-apps-securing-a-logic-app/secure-inputs-flow.png)

  如果撰寫、剖析 JSON 和回應動作明確使用具有受保護輸入之觸發程式或動作的可見輸出, Logic Apps 會隱藏這些動作的輸入和輸出, 但*不會啟用*這些動作的**安全輸入**設. 如果下游動作明確使用撰寫、剖析 JSON 或回應動作中的隱藏輸出做為輸入, Logic Apps*不會隱藏此下游動作的輸入或輸出*。

  ![受保護的輸入和下游對特定動作的影響](media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>存取參數輸入

如果您在不同的環境之間部署, 請考慮將工作流程定義中的值參數化, 這會根據這些環境而有所不同。 如此一來，您就可以使用 [Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md#parameters)來部署邏輯應用程式, 藉由定義安全的參數來保護機密資訊，並使用[參數](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values)，分別透過範本的參數提供這些參數輸入檔案。

例如, 如果您使用[Azure Active Directory](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)來驗證 HTTP 動作, 您可以定義並保護接受用於驗證之用戶端識別碼和用戶端密碼的參數。 若要為您的邏輯應用程式定義這些參數`parameters` , 請使用邏輯應用程式的工作流程定義內的區段。 若要保護編輯邏輯應用程式或查看執行歷程記錄時不想顯示的參數值, 請使用`securestring`或`secureobject`類型來定義參數, 並視需要使用編碼方式。 具有此類型的參數不會隨資源定義一起傳回, 而且在部署之後查看資源時無法存取。 若要在執行時間期間存取這些參數值`@parameters('<parameter-name>')` , 請在您的工作流程定義內使用運算式。 此運算式只會在執行時間評估, 並由[工作流程定義語言](../logic-apps/logic-apps-workflow-definition-language.md)所描述。

> [!NOTE]
> 如果您在 HTTP 要求的標頭或本文中使用參數, 當您查看邏輯應用程式的執行歷程記錄和傳出 HTTP 要求時, 可能會顯示該參數。 請確定您也會據以設定您的內容存取原則。 授權標頭絕對不會透過輸入或輸出來顯示。 如此一來如果該處有使用密碼，就無法擷取密碼。

如需詳細資訊, 請參閱本主題稍後的[保護工作流程定義中的參數](#secure-parameters-workflow)。

當您使用[Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md#parameters)來自動化部署時, 您可以使用`securestring`和`secureobject`類型定義受保護的範本參數 (在部署時進行評估)。 若要定義範本參數, 請使用您範本的`parameters`最上層區段, 這是獨立的, 而且不同于`parameters`您的工作流程定義區段。 若要提供範本參數的值, 請使用個別的[參數](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values)檔。

例如, 如果您使用秘密, 您可以定義和使用安全的範本參數, 在部署時從[Azure Key Vault](../key-vault/key-vault-whatis.md)取出這些秘密。 接著, 您可以在參數檔案中參考金鑰保存庫和密碼。 如需詳細資訊，請參閱下列主題：

* [使用 Azure Key Vault 在部署時傳遞安全參數值](../azure-resource-manager/resource-manager-keyvault-parameter.md)
* 在本主題稍後的[Azure Resource Manager 範本中保護參數](#secure-parameters-deployment-template)

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>保護工作流程定義中的參數

若要保護邏輯應用程式的工作流程定義中的機密資訊, 請使用受保護的參數, 以便在您儲存邏輯應用程式之後看不到這項資訊。 例如, 假設您有 HTTP 動作需要基本驗證, 這會使用使用者名稱和密碼。 在工作流程定義中, `parameters`區段會使用`basicAuthPasswordParam` `securestring`類型`basicAuthUsernameParam`來定義和參數。 接著, 動作定義會參考`authentication`區段中的這些參數。

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
         "type": "Http",
         "inputs": {
            "method": "GET",
            "uri": "https://www.microsoft.com",
            "authentication": {
               "type": "Basic",
               "username": "@parameters('basicAuthUsernameParam')",
               "password": "@parameters('basicAuthPasswordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "basicAuthPasswordParam": {
         "type": "securestring"
      },
      "basicAuthUsernameParam": {
         "type": "securestring"
      }
   },
   "triggers": {
      "manual": {
         "type": "Request",
         "kind": "Http",
         "inputs": {
            "schema": {}
         }
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
}
```

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Azure Resource Manager 範本中的安全參數

邏輯應用程式的 Resource Manager 範本有多個`parameters`區段。 若要保護密碼、金鑰、秘密和其他機密資訊, 請使用`securestring`或`secureobject`類型, 在範本層級和工作流程定義層級定義受保護的參數。 然後您可以將這些值儲存在[Azure Key Vault](../key-vault/key-vault-whatis.md)中, 並使用[參數](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values)檔案來參考金鑰保存庫和密碼。 然後, 您的範本會在部署時抓取該資訊。 如需詳細資訊, 請參閱[在部署時使用 Azure Key Vault 傳遞安全的參數值](../azure-resource-manager/resource-manager-keyvault-parameter.md)。

以下是有關這些`parameters`區段的詳細資訊:

* 在範本的最上層, `parameters`區段會定義範本在*部署*時使用之值的參數。 例如, 這些值可以包含特定部署環境的連接字串。 然後您可以將這些值儲存在個別的[參數](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values)檔案中, 這樣會讓變更這些值變得更容易。

* 在邏輯應用程式的資源定義內部, 但在您的工作流程`parameters`定義外, 區段會指定工作流程定義參數的值。 在本節中, 您可以使用參考範本參數的範本運算式來指派這些值。 這些運算式會在部署時進行評估。

* 在您的`parameters`工作流程定義內, 區段會定義邏輯應用程式在執行時間使用的參數。 接著, 您可以在邏輯應用程式的工作流程中, 使用在執行時間評估的工作流程定義運算式來參考這些參數。

這個範例範本具有多個使用`securestring`類型的安全參數定義:

| 參數名稱 | 描述 |
|----------------|-------------|
| `TemplatePasswordParam` | 範本參數, 可接受隨後傳遞至工作流程定義`basicAuthPasswordParam`之參數的密碼 |
| `TemplateUsernameParam` | 此範本參數會接受使用者名稱, 然後傳遞至工作流程定義的`basicAuthUserNameParam`參數 |
| `basicAuthPasswordParam` | 針對 HTTP 動作中的基本驗證接受密碼的工作流程定義參數 |
| `basicAuthUserNameParam` | 在 HTTP 動作中接受基本驗證使用者名稱的工作流程定義參數 |
|||

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "TemplatePasswordParam": {
         "type": "securestring"
      },
      "TemplateUsernameParam": {
         "type": "securestring"
      },
      "LogicAppLocation": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "allowedValues": [
            "[resourceGroup().location]",
            "eastasia",
            "southeastasia",
            "centralus",
            "eastus",
            "eastus2",
            "westus",
            "northcentralus",
            "southcentralus",
            "northeurope",
            "westeurope",
            "japanwest",
            "japaneast",
            "brazilsouth",
            "australiaeast",
            "australiasoutheast",
            "southindia",
            "centralindia",
            "westindia",
            "canadacentral",
            "canadaeast",
            "uksouth",
            "ukwest",
            "westcentralus",
            "westus2"
         ],
         "metadata": {
            "description": "Location of the Logic App."
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-0601/workflowdefinition.json#",
               "actions": {
                  "HTTP": {
                     "type": "Http",
                     "inputs": {
                        "method": "GET",
                        "uri": "https://www.microsoft.com",
                        "authentication": {
                           "type": "Basic",
                           "username": "@parameters('basicAuthUsernameParam')",
                           "password": "@parameters('basicAuthPasswordParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "basicAuthPasswordParam": {
                     "type": "securestring"
                  },
                  "basicAuthUsernameParam": {
                     "type": "securestring"
                  }
               },
               "triggers": {
                  "manual": {
                     "type": "Request",
                     "kind": "Http",
                     "inputs": {
                        "schema": {}
                     }
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "basicAuthPasswordParam": {
                  "value": "[parameters('TemplatePasswordParam')]"
               },
               "basicAuthUsernameParam": {
                  "value": "[parameters('TemplateUsernameParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-requests"></a>

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>存取從邏輯應用程式呼叫的服務和系統

以下是一些方法, 可讓您保護邏輯應用程式需要存取傳送要求的端點:

* 在輸出要求上新增驗證。

  當您使用 HTTP、HTTP + Swagger (Open API) 或 Webhook 動作時, 您可以將驗證新增至邏輯應用程式所傳送的要求。 例如，您可以使用基本驗證、憑證驗證或 Azure Active Directory 驗證。 如需詳細資訊，請參閱[驗證觸發程序或動作](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)。

* 限制來自邏輯應用程式 IP 位址的存取。

  從邏輯應用程式對端點的所有呼叫, 都是源自以您的邏輯應用程式區域為基礎的特定指定 IP 位址。 您可以新增篩選，以只接受來自那些 IP 位址的要求。 若要取得這些 IP 位址, 請參閱[Azure Logic Apps 的限制和](logic-apps-limits-and-config.md#configuration)設定。

* 以受控識別身分驗證和存取資源。

  若要存取其他 Azure Active Directory (Azure AD) 租使用者中的資源, 您的邏輯應用程式可以使用受控識別 (先前稱為受控服務識別或 MSI) (而非認證或秘密) 來驗證您的身分識別, 而不需要登入。 Azure 會為您管理此身分識別，並協助保護您的認證，因為您不需要提供或輪替使用祕密。 如需如何為您的邏輯應用程式設定及使用系統指派的受控識別的詳細資訊, 請參閱[在 Azure Logic Apps 中使用受控識別來驗證和存取資源](../logic-apps/create-managed-service-identity.md)。

* 保護內部部署系統的連接。

  Azure Logic Apps 提供與下列服務的整合，以提供安全可靠的內部部署通訊。

  * 內部部署資料閘道

    Azure Logic Apps 中的許多受控連接器都會提供與內部部署系統 (例如檔案系統、SQL、SharePoint 和 DB2) 的安全連線。 閘道會在加密通道上經過 Azure 服務匯流排傳送來自內部部署來源的資料。 源自閘道代理程式的所有流量都是安全輸出流量。 了解[內部部署資料閘道的運作方式](logic-apps-gateway-install.md#gateway-cloud-service)。

  * 透過 Azure API 管理連接

    [Azure API 管理](../api-management/api-management-key-concepts.md)提供內部部署連線選項，例如站對站虛擬私人網路和 ExpressRoute 整合，以對內部部署系統提供安全的 Proxy 和通訊。 從邏輯應用程式設計工具的邏輯應用程式工作流程中, 您可以選取 API 管理所公開的 API, 以提供對內部部署系統的快速存取。

## <a name="next-steps"></a>後續步驟

* [建立部署範本](logic-apps-create-deploy-template.md)  
* [監視邏輯應用程式](logic-apps-monitor-your-logic-apps.md)  
* [診斷邏輯應用程式失敗和問題](logic-apps-diagnosing-failures.md)  
* [自動化邏輯應用程式部署](logic-apps-azure-resource-manager-templates-overview.md)
