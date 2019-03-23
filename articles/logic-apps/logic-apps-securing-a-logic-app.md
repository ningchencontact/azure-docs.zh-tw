---
title: 安全存取 Azure Logic Apps | Microsoft Docs
description: 為 Azure Logic Apps (包括觸發程序、輸入與輸出、參數和其他服務) 新增安全性
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.topic: article
ms.date: 02/05/2019
ms.openlocfilehash: 6f878e05255be8f65868d8d1dcdbacb3ccfa2729
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369933"
---
# <a name="secure-access-in-azure-logic-apps"></a>在 Azure Logic Apps 中安全存取

以下是您在邏輯應用程式中可以安全存取的元素：

* [要求或 Webhook 觸發程序](#secure-triggers)
* [作業，例如管理、編輯或檢視](#secure-operations)您的邏輯應用程式
* 邏輯應用程式執行歷程記錄的[輸入或輸出](#secure-run-history)
* [動作參數和輸入](#secure-action-parameters)
* 從邏輯應用程式[取得要求的服務](#secure-requests)

<a name="secure-triggers"></a>

## <a name="secure-access-to-request-triggers"></a>保護要求觸發程序的存取

當您的邏輯應用程式使用 HTTP 要求型觸發程序 (例如[要求](../connectors/connectors-native-reqres.md)或 [Webhook](../connectors/connectors-native-webhook.md) 觸發程序) 時，您可以將存取限制為僅限已授權的用戶端才可啟動邏輯應用程式。 邏輯應用程式收到的所有要求，都會使用安全通訊端層 (SSL) 通訊協定加密並保護。 以下是您可以保護此觸發程序類型存取的不同方式：

* [產生共用存取簽章](#sas)
* [限制連入 IP 位址](#restrict-incoming-ip-addresses)
* [新增 Azure Active Directory、OAuth 或其他安全性](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures"></a>產生共用存取簽章

邏輯應用程式上的每個要求端點都會在端點 URL 中包含[共用存取簽章](../storage/common/storage-dotnet-shared-access-signature-part-1.md) (SAS)。 每個 URL 都包含 `sp`、`sv` 和 `sig` 查詢參數：

* `sp` 指定權限，其對應至允許使用的 HTTP 方法。
* `sv` 指定用於產生簽章的版本。
* `sig` 用於驗證對觸發程序的存取。

簽章是使用 SHA256 演算法搭配祕密存取金鑰所產生，位於所有的 URL 路徑和屬性上。 祕密金鑰永遠不會公開或發佈，並且會維持加密狀態，儲存於邏輯應用程式中。 您的邏輯應用程式只會針對包含使用祕密金鑰建立之有效簽章的觸發程序進行授權。 

以下是安全存取共用存取簽章的相關詳細資訊：

* [重新產生存取金鑰](#access-keys)
* [建立即將到期的回呼 URL](#expiring-URLs)
* [使用主要或次要金鑰建立 URL](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>重新產生存取金鑰

若要隨時產生新的安全存取金鑰，請使用 Azure REST API 或 Azure 入口網站。 之前使用舊金鑰產生的所有 URL 都將失效，並且不再有觸發邏輯應用程式的授權。 重新產生之後，您所擷取的 URL 會使用新的存取金鑰進行簽署。

1. 在 Azure 入口網站中，開啟具有要重新產生金鑰的邏輯應用程式。

1. 在邏輯應用程式功能表的 [設定] 下，選取 [存取金鑰]。

1. 選取您要重新產生的金鑰，並完成程序。

<a name="expiring-urls"></a>

#### <a name="create-callback-urls-with-expiration-dates"></a>建立具有到期日期的回呼 URL

如果您與其他人共用要求型觸發程序端點 URL，您可以視需要使用特定金鑰和到期日期來產生回呼 URL。 您接著可以順暢地輪替金鑰，或將觸發邏輯應用程式的存取限制為特定時間範圍。 您可以使用 [Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers) 指定 URL 的到期日期，例如：

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

在本文中，包含使用 JSON 日期字串的 `NotAfter` 屬性。 此屬性會傳回只在 `NotAfter` 日期與時間之前有效的回呼 URL。

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>使用主要或次要祕密金鑰建立 URL

當您針對要求式觸發程序產生或是列出回呼 URL 時，您也可以指定要用來簽署 URL 的金鑰。 您可以使用 [Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers)，產生使用特定金鑰簽署的 URL，例如：

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

在本文中，將屬性 `KeyType` 包含為 `Primary` 或 `Secondary`。 此屬性會傳回使用指定安全金鑰簽署的 URL。

<a name="restrict-incoming-ip"></a>

### <a name="restrict-incoming-ip-addresses"></a>限制傳入的 IP 位址

搭配使用共用存取簽章，您可能想限制可呼叫邏輯應用程式的特定用戶端。  
例如，如果您使用 Azure API 管理來管理要求端點，您可以限制邏輯應用程式僅接受來自 API 管理執行個體之 IP 位址的要求。 

#### <a name="set-ip-ranges---azure-portal"></a>設定 IP 範圍 - Azure 入口網站

若要在 Azure 入口網站中設定此限制，請移至邏輯應用程式的設定： 

1. 在 Azure 入口網站的「邏輯應用程式設計工具」中，開啟邏輯應用程式。 

1. 在邏輯應用程式功能表的 [設定] 底下，選取 [工作流程設定]。

1. 在 [存取控制設定] > 
[允許的輸入 IP 位址] 底下，選取 [特定 IP 範圍]。

1. 在 [觸發程序的 IP 範圍] 底下，指定觸發程序可接受的 IP 位址範圍。 有效的 IP 範圍使用這些格式：*x.x.x.x/x* 或 *x.x.x.x-x.x.x.x* 

如果您希望您的邏輯應用程式僅作為巢狀邏輯應用程式來引發，從 [允許的輸入 IP 位址] 清單選取 [只有其他邏輯應用程式]。 此選項會將空陣列寫入您的邏輯應用程式資源，如此一來只有從 Logic Apps 服務 (父邏輯應用程式) 呼叫時，才能觸發巢狀邏輯應用程式。

> [!NOTE]
> 不論 IP 位址為何，您仍然可以使用 `/triggers/{triggerName}/run` 透過 Azure REST API 或透過 API 管理來執行具有要求型觸發程序的邏輯應用程式。 不過，此情況仍然需要針對 Azure REST API 進行驗證，且所有事件都會出現在 Azure 稽核記錄中。 請務必適當地設定存取控制原則。

#### <a name="set-ip-ranges---logic-app-deployment-template"></a>設定 IP 範圍 - 邏輯應用程式部署範例

如果您要使用 [Azure Resource Manager 部署範本](../logic-apps/logic-apps-create-deploy-template.md)將邏輯應用程式部署自動化，您可以在該範本中設定 IP 範圍，例如：

``` json
{
   "properties": {
      "definition": {},
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
   },
   "type": "Microsoft.Logic/workflows",
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-oauth-or-other-security"></a>新增 Azure Active Directory、OAuth 或其他安全性

若要將更多授權通訊協定新增到您的邏輯應用程式，請考慮使用 [Azure API 管理](https://azure.microsoft.com/services/api-management/)。 此服務提供任何端點豐富的監視、安全性、原則和文件，並可讓您將邏輯應用程式公開作為 API。 API 管理可針對邏輯應用程式將公用或私人端點公開，並且可以使用 Azure Active Directory、OAuth 憑證或其他的安全性標準。 當 API 管理收到要求時，服務會傳送要求至您的邏輯應用程式，也會在過程中進行任何必要的轉換或限制。 若要只讓 API 管理觸發您的邏輯應用程式，您可以使用邏輯應用程式的連入 IP 範圍設定。 

<a name="secure-operations"></a>

## <a name="secure-access-to-logic-app-operations"></a>保護邏輯應用程式作業的存取

若要只讓特定使用者或群組在邏輯應用程式上執行作業，您可以限制工作 (例如管理、編輯和檢視) 的存取權。 Logic Apps 支援 [Azure 角色型存取控制 (RBAC)](../role-based-access-control/role-assignments-portal.md)，其可讓您自訂或指派內建角色給訂用帳戶中的成員，例如：

* **邏輯應用程式參與者**：使用者可以檢視、編輯和更新邏輯應用程式。 此角色無法刪除邏輯應用程式或執行系統管理員作業。
* **邏輯應用程式操作員**：使用者可以檢視邏輯應用程式和執行歷程記錄，並啟用或停用邏輯應用程式。 此角色無法編輯或更新邏輯應用程式。

若要防止變更或刪除邏輯應用程式，您可以使用 [Azure 資源鎖定](../azure-resource-manager/resource-group-lock-resources.md)。 這項功能可協助您防止其他人變更或刪除生產資源。

<a name="secure-run-history"></a>

## <a name="secure-access-to-logic-app-run-history"></a>保護邏輯應用程式執行歷程記錄的存取

若要保護作為輸入傳遞的內容，或先前邏輯應用程式執行輸出的內容，您可以將存取限制為特定 IP 位址範圍。 此功能提供您更詳細的存取控制。 在邏輯應用程式中執行的所有資料，在傳送過程和待用時都會加密。 當您要求邏輯應用程式的執行歷程記錄時，Logic Apps 會驗證該要求，並提供來自要求的輸入和輸出連結，以及邏輯應用程式工作流程中的回應。 您可以保護這些連結，只讓來自特定 IP 位址的要求傳回該內容。 舉例來說，您甚至可以指定如 `0.0.0.0-0.0.0.0` 的 IP 位址，如此一來就沒有人可以存取輸入和輸出。 只有具備系統管理員權限的人才能移除此限制，提供邏輯應用程式內容「即時」存取的可能性。

### <a name="set-ip-ranges---azure-portal"></a>設定 IP 範圍 - Azure 入口網站

若要在 Azure 入口網站中設定此限制，請移至邏輯應用程式的設定：

1. 在 Azure 入口網站的「邏輯應用程式設計工具」中，開啟邏輯應用程式。 

1. 在邏輯應用程式功能表的 [設定] 底下，選取 [工作流程設定]。

1. 在 [存取控制設定]****> 
   [允許的輸入 IP 位址]**** 底下，選取 [特定 IP 範圍]****。

1. 在 [內容的 IP 範圍] 底下，指定可存取輸入和輸出內容的 IP 位址範圍。 
   有效的 IP 範圍使用這些格式：*x.x.x.x/x* 或 *x.x.x.x-x.x.x.x* 

### <a name="set-ip-ranges---logic-app-deployment-template"></a>設定 IP 範圍 - 邏輯應用程式部署範例

如果您要使用 [Azure Resource Manager 部署範本](../logic-apps/logic-apps-create-deploy-template.md)將邏輯應用程式部署自動化，您可以在該範本中設定 IP 範圍，例如：

``` json
{
   "properties": {
      "definition": {},
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
   },
   "type": "Microsoft.Logic/workflows",
}
```

<a name="secure-action-parameters"></a>

## <a name="secure-action-parameters-and-inputs"></a>保護動作參數和輸入

在各種環境中部署時，您可能會想要將邏輯應用程式工作流程定義中的特定元素參數化。 如此一來，您就可以根據所使用的環境提供輸入，並保護敏感性資訊。 例如，如果您要使用 [Azure Active Directory](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication) 驗證 HTTP 動作，而定義並保護接受用於驗證之用戶端識別碼和用戶端祕密的參數。 針對這些參數，您的邏輯應用程式定義有自己的 `parameters` 區段。
若要在執行階段期間存取參數值，您可以使用 `@parameters('parameterName')` 運算式 (由[工作流程定義語言](https://aka.ms/logicappsdocs)提供)。 

若要保護您不希望在編輯邏輯應用程式或檢視執行記錄時顯示的參數和值，您可以使用 `securestring` 型別定義參數，並視需要使用編碼。 具有此型別的參數無法透過資源定義傳回，且部署之後也無法在檢視資源時存取。

> [!NOTE]
> 如果您在要求的標頭或本文中使用參數，當存取邏輯應用程式的執行歷程記錄及連出 HTTP 要求時，可能會看見該參數。 另外請務必適當地設定您的內容存取原則。
> 授權標頭絕對不會透過輸入或輸出來顯示。 如此一來如果該處有使用密碼，就無法擷取密碼。

如需保護邏輯應用程式定義中之參數的詳細資訊，請參閱此頁面稍後的[保護邏輯應用程式定義中的參數](#secure-parameters-workflow)。

如果您使用 [Azure Resource Manager 部署範本](../azure-resource-manager/resource-group-authoring-templates.md#parameters)將部署自動化，您也可以在那些範本中使用受保護的參數。 例如，您可以使用參數來取得建立邏輯應用程式時的 KeyVault 祕密。 您的部署範本定義擁有自己的 `parameters` 區段，不同於您邏輯應用程式的 `parameters` 區段。 如需保護部署範本中之參數的詳細資訊，請參閱此頁面稍後的[保護部署範本中的參數](#secure-parameters-deployment-template)。

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-logic-app-definitions"></a>保護邏輯應用程式定義中的參數

若要保護您邏輯應用程式工作流程定義中的敏感性資訊，使用受保護的參數可讓此資訊在您儲存邏輯應用程式之後隱藏。 例如，假設您在 HTTP 動作定義中使用 `Basic` 驗證。 此範例包括定義動作定義之參數的 `parameters` 區段，加上接受 `username` 和 `password` 參數值的 `authentication` 區段。 若要提供這些參數的值，您可以使用不同的參數檔，例如：

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
               "username": "@parameters('usernameParam')",
               "password": "@parameters('passwordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "passwordParam": {
         "type": "securestring"
      },
      "userNameParam": {
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

如果您使用祕密，您可以使用 [Azure Resource Manager KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) 取得部署階段的那些祕密。

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-deployment-templates"></a>保護 Azure Resource Manager 部署範本中的參數

此範例顯示使用多個執行階段參數搭配 `securestring` 類型的 Resource Manager 部署範本：

* `armTemplatePasswordParam`，這是邏輯應用程式定義之 `logicAppWfParam` 參數的輸入

* `logicAppWfParam`，這是使用基本驗證的 HTTP 動作輸入

此範例包含內部的 `parameters` 區段，其屬於邏輯應用程式的工作流程定義，而外部的 `parameters` 區段屬於部署範本。 若要指定參數的環境值，您可以使用不同的參數檔。 

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "logicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "armTemplatePasswordParam": {
         "type": "securestring"
      },
      "logicAppLocation": {
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
         "name": "[parameters('logicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('logicAppLocation')]",
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
                           "username": "@parameters('usernameParam')",
                           "password": "@parameters('logicAppWfParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "logicAppWfParam": {
                     "type": "securestring"
                  },
                  "userNameParam": {
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
               "logicAppWfParam": {
                  "value": "[parameters('armTemplatePasswordParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

如果您使用祕密，您可以使用 [Azure Resource Manager KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) 取得部署階段的那些祕密。

<a name="secure-requests"></a>

## <a name="secure-access-to-services-receiving-requests"></a>保護接受要求之服務的存取

以下是一些保護任何端點的方式，即您的邏輯應用程式需要存取和傳送要求的位置。

### <a name="add-authentication-on-outbound-requests"></a>在外送要求上新增驗證

當使用 HTTP、HTTP + Swagger (Open API)，或 Webhook 動作時，您可以將驗證新增到邏輯應用程式所傳送的要求。 例如，您可以使用基本驗證、憑證驗證或 Azure Active Directory 驗證。 如需詳細資訊，請參閱[驗證觸發程序或動作](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)。

### <a name="restrict-access-to-logic-app-ip-addresses"></a>限制對邏輯應用程式 IP 位址的存取

邏輯應用程式的所有呼叫都來自以區域為基礎的特定 IP 位址。 您可以新增篩選，以只接受來自那些 IP 位址的要求。 如需那些 IP 位址，請參閱 [Azure Logic Apps 的限制和設定](logic-apps-limits-and-config.md#configuration)。

### <a name="secure-on-premises-connectivity"></a>保護內部部署連線

Azure Logic Apps 提供與下列服務的整合，以提供安全可靠的內部部署通訊。

#### <a name="on-premises-data-gateway"></a>內部部署資料閘道

Azure Logic Apps 的許多受控連接器提供對內部部署系統 (例如檔案系統、SQL、SharePoint、DB2 和其他系統) 的安全連線。 閘道會在加密通道上經過 Azure 服務匯流排傳送來自內部部署來源的資料。 源自閘道代理程式的所有流量都是安全輸出流量。 了解[內部部署資料閘道的運作方式](logic-apps-gateway-install.md#gateway-cloud-service)。

#### <a name="azure-api-management"></a>Azure API 管理

[Azure API 管理](https://azure.microsoft.com/services/api-management/)提供內部部署連線選項，例如站對站虛擬私人網路和 ExpressRoute 整合，以對內部部署系統提供安全的 Proxy 和通訊。 在邏輯應用程式設計工具中，您可以在邏輯應用程式的工作流程中，選取從 API 管理公開的 API，提供對內部部署系統的快速存取。

## <a name="next-steps"></a>後續步驟

* [建立部署範本](logic-apps-create-deploy-template.md)  
* [例外狀況處理](logic-apps-exception-handling.md)  
* [監視邏輯應用程式](logic-apps-monitor-your-logic-apps.md)  
* [診斷邏輯應用程式失敗和問題](logic-apps-diagnosing-failures.md)  
