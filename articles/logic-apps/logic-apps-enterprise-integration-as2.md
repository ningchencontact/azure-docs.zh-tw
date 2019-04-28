---
title: 用于 B2B 企业集成的 AS2 消息 - Azure 逻辑应用
description: 交換 AS2 訊息儲存在 Azure Logic Apps 與企業整合套件
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: b494f6524e5105a95bc8a24a6fa2521abcca3f7b
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2019
ms.locfileid: "63760228"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>在採用 Enterprise Integration Pack 的 Azure Logic Apps 中交換適用於 B2B 企業整合的 AS2 訊息

若要使用 Azure Logic Apps 中的 AS2 訊息，您可以使用 AS2 連接器提供觸發程序和動作來管理 AS2 通訊。 例如，若要建立安全性和可靠性，傳輸訊息時，您可以使用這些動作：

* [**編碼為 AS2 訊息**動作](#encode)提供加密、 數位簽章，和通知透過訊息處置通知 (MDN)，這協助支援不可否認性。 比方說，此動作會套用 AS2/HTTP 標頭，並執行這些工作時設定：

  * 簽署外寄訊息。
  * 加密外寄訊息。
  * 壓縮訊息。
  * 傳輸 MIME 標頭中的檔案名稱。

* [**AS2 訊息解碼**動作](#decode)來提供解密、 數位簽章，以及通知透過訊息處置通知 (MDN)。 例如，此動作會執行下列工作： 

  * 處理 AS2/HTTP 標頭。
  * 協調收到的 Mdn 與原始輸出訊息。
  * 更新，並將不可否認性資料庫中的記錄相互關聯。
  * 寫入 AS2 狀態報告的記錄。
  * 輸出承載內容做為 base64 編碼。
  * 判斷 Mdn 是否必要。 根據 AS2 協議，就會判斷 Mdn 是否應該為同步或非同步。
  * 會根據 AS2 協議的同步或非同步 Mdn 的產生。
  * 設定 Mdn 相互關聯 token 和屬性。

  此動作也會執行這些工作時設定：

  * 驗證簽章。
  * 將訊息解密。
  * 將訊息解壓縮。 
  * 請檢查並不允許訊息識別碼重複。

本文說明如何新增 AS2 編碼和解碼動作加入現有的邏輯應用程式。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* 從您要使用 AS2 連接器和觸發程序啟動邏輯應用程式的工作流程邏輯應用程式。 AS2 連接器會提供只有動作，沒有觸發程序。 如果您不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps](../logic-apps/logic-apps-overview.md) 和[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* [整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)，具有與您 Azure 訂用帳戶相關聯，且連結至您打算使用 AS2 連接器的邏輯應用程式。 您的邏輯應用程式與整合帳戶必須存在於相同的位置或 Azure 區域中。

* 至少兩個[交易夥伴](../logic-apps/logic-apps-enterprise-integration-partners.md)，您已定義在整合帳戶中使用 AS2 識別辨識符號。

* 您可以使用 AS2 連接器之前，您必須建立 AS2[協議](../logic-apps/logic-apps-enterprise-integration-agreements.md)交易夥伴與存放區之間的整合帳戶中的協議。

* 如果您使用[Azure Key Vault](../key-vault/key-vault-overview.md)憑證管理，請檢查您的保存庫金鑰，允許**Encrypt**並**解密**作業。 否則，編碼與解碼動作失敗。

  在 Azure 入口網站中，移至金鑰保存庫中，檢視您的保存庫金鑰**允許的作業**，並確認**Encrypt**並**解密**選取作業。

  ![請檢查保存庫金鑰作業](media/logic-apps-enterprise-integration-as2/vault-key-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>編碼 AS2 訊息

1. 如果您還沒有這麼做，在[Azure 入口網站](https://portal.azure.com)，在邏輯應用程式設計工具開啟邏輯應用程式。

1. 在設計師中，請在邏輯應用程式中加入新的動作。 

1. 底下**選擇動作**和搜尋 方塊中，選擇**所有**。 在 [搜尋] 方塊中，輸入 [程式碼 as2]，然後選取此動作：**編碼為 AS2 訊息**。

   ![選取"編碼為 AS2 訊息 」](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. 如果您沒有現有的連接到整合帳戶中，系統會提示您立即建立該連線。 命名您的連線，請選取您想要連線，然後選擇 整合帳戶**建立**。

   ![建立整合帳戶連線](./media/logic-apps-enterprise-integration-as2/as2-create-connection.png)  
 
1. 現在會提供這些屬性的資訊：

   | 屬性 | 描述 |
   |----------|-------------|
   | **AS2-From** | 您的 AS2 協議所指定的訊息寄件者的的識別項 |
   | **AS2-To** | 您的 AS2 協議所指定的訊息收件者識別碼 |
   | **body** | 訊息內容 |
   |||

   例如︰

   ![訊息編碼的屬性](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

<a name="decode"></a>

## <a name="decode-as2-messages"></a>解碼 AS2 訊息

1. 如果您還沒有這麼做，在[Azure 入口網站](https://portal.azure.com)，在邏輯應用程式設計工具開啟邏輯應用程式。

1. 在設計師中，請在邏輯應用程式中加入新的動作。 

1. 底下**選擇動作**和搜尋 方塊中，選擇**所有**。 在 [搜尋] 方塊中，輸入 「 解碼 as2 」，然後選取此動作：**解碼 AS2 訊息**

   ![選取 「 解碼 AS2 訊息 」](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. 如果您沒有現有的連接到整合帳戶中，系統會提示您立即建立該連線。 命名您的連線，請選取您想要連線，然後選擇 整合帳戶**建立**。

   ![建立整合帳戶連線](./media/logic-apps-enterprise-integration-as2/as2-create-connection.png)  

1. 針對**主體**並**標頭**，從上一個觸發程序或動作的輸出中選取這些值。

   例如，假設您的邏輯應用程式收到的要求觸發程序的訊息。 您可以從該觸發程序，來選取輸出。

   ![從要求輸出選取內文和標頭](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png) 

## <a name="sample"></a>範例

若要嘗試部署可完整運作的邏輯應用程式和範例 AS2 案例，請參閱 [AS2 邏輯應用程式範本和案例](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)。

## <a name="connector-reference"></a>連接器參考

技術的詳細資訊，例如觸發程序、 動作和限制，如所述的連接器的 OpenAPI (以前稱為 Swagger) 檔案，請參閱 <<c0> [ 連接器的參考頁面](/connectors/as2/)。

## <a name="next-steps"></a>後續步驟

深入了解[企業整合套件](logic-apps-enterprise-integration-overview.md)
