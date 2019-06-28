---
title: 適用於 B2B 企業整合-Azure Logic Apps 的 RosettaNet 訊息
description: Azure Logic Apps 與企業整合套件中的 Exchange RosettaNet 訊息
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 88e02f3fbbca8007fdf479bb973f50c42a878d6e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332638"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>適用於 B2B 企業整合 Azure Logic Apps 中的 Exchange RosettaNet 訊息 

[RosettaNet](https://resources.gs1us.org)是非營利的協會已建立分享商務資訊的標準程序。 這些標準常用於供應鏈流程，並會廣泛半導體、 電子裝置及物流產業中。 RosettaNet 協會建立，並維護交易夥伴介面程序 (Pip)，可提供常見的商務程序定義，對所有 RosettaNet 訊息交換。 RosettaNet 以 XML 為基礎，並定義訊息指導方針，商務程序的介面和公司之間進行通訊的實作架構。

在  [Azure Logic Apps](../logic-apps/logic-apps-overview.md)，RosettaNet 連接器可協助您建立支援 RosettaNet 標準的整合解決方案。 連接器以 RosettaNet 實作架構 (RNIF) 版本 2.0.01 版上。 RNIF 是開放型的網路應用程式架構，可讓商務夥伴協同執行 RosettaNet Pip。 此架構會定義訊息結構、 通知、 多用途網際網路郵件延伸標準 (MIME) 編碼及數位簽章的需求。

具體來說，連接器會提供這些功能：

* 編碼或接收 RosettaNet 訊息。
* 解碼，或傳送 RosettaNet 訊息。
* 等候的回應 」 和 「 失敗通知的產生。

這些功能，連接器便可支援 RNIF 2.0.01 版所定義的所有 Pip。 與夥伴通訊可以是同步或非同步。

## <a name="rosettanet-concepts"></a>RosettaNet 概念

以下是一些概念和詞彙都是獨一無二的 RosettaNet 規格，且建立 RosettaNet 架構整合時很重要：

* **PIP**

  RosettaNet 組織建立並維護交易夥伴介面程序 (Pip)，可提供常見的商務程序定義，對所有 RosettaNet 訊息交換。 每個 PIP 規格都提供文件類型定義 (DTD) 檔案以及訊息指導方針文件。 DTD 檔案定義的服務內容訊息結構。 訊息指導方針文件，也就是人類看得懂的 HTML 檔案，指定項目層級條件約束。 結合這些檔案可提供完整的商務程序的定義。

   Pip 分為高階商務功能，或叢集中，並在子函式，或區段。 比方說，「 3A4 」 是訂單的 PIP、"3"時為訂單管理函式和"3A"是報價以及訂單輸入子函式。 如需詳細資訊，請參閱 < [RosettaNet 網站](https://resources.gs1us.org)。

* **Action**

  組件的 PIP，動作訊息為合作夥伴之間交換的商務訊息。

* **Signal**

   組件的 PIP，信號訊息是傳送以回應動作訊息的通知。

* **單一動作和雙向動作**

  對於單向動作 PIP，唯一的回應是通知信號訊息。 雙向動作 PIP，啟動者接收回應訊息以及除了單向動作訊息流程之外的通知會回覆。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* [整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)來儲存您的合約及其他 B2B 成品。 此整合帳戶必須與您 Azure 訂用帳戶相關聯。

* 至少兩個[合作夥伴](../logic-apps/logic-apps-enterprise-integration-partners.md)整合帳戶中所定義且設有底下的 「 DUNS"限定詞**商務身分識別**

* PIP 程序組態，才能傳送或接收 RosettaNet 訊息，在您的整合帳戶中。 流程組態會儲存所有的 PIP 組態特性。 然後，當您與夥伴建立協議時，您可以參考此組態。 若要建立整合帳戶中的 PIP 程序組態，請參閱[新增 PIP 程序組態](#add-pip)。

* 選擇性[憑證](../logic-apps/logic-apps-enterprise-integration-certificates.md)加密、 解密或簽署您上傳到整合帳戶的訊息。 只有當您是使用簽章或加密需要憑證。

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>新增 PIP 程序組態

若要加入您的整合帳戶中的 PIP 程序組態，請遵循下列步驟：

1. 在  [Azure 入口網站](https://portal.azure.com)，尋找並開啟您的整合帳戶。

1. 在 **概觀**窗格中，選取**RosettaNet PIP**圖格。

   ![選擇 RosettaNet 圖格](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. 底下**RosettaNet PIP**，選擇**新增**。 提供您 PIP 的詳細資料。

   ![新增 RosettaNet PIP 的詳細資料](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | 屬性 | 必要項 | 描述 |
   |----------|----------|-------------|
   | **名稱** | 是 | 您的 PIP 名稱 |
   | **PIP 代碼** | 是 | PIP 三位數代碼。 如需詳細資訊，請參閱 < [RosettaNet Pip](https://docs.microsoft.com/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips)。 |
   | **PIP 版本** | 是 | PIP 的版本號碼，都可以根據您所選的 PIP 代碼 |
   ||||

   如需有關這些 PIP 屬性的詳細資訊，請瀏覽[RosettaNet 網站](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg)。

1. 當您完成時，選擇**確定**，這會建立 PIP 組態。

1. 若要檢視或編輯流程組態，請選取 PIP，然後選擇**編輯為 JSON**。

   所有處理程序的組態設定都來自 PIP 規格。 Logic Apps 會填入大部分的設定是最常用的值，這些屬性的預設值。

   ![編輯 RosettaNet PIP 組態](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. 確認設定對應至適當的 PIP 規格中的值，而且您的業務需求。 如有必要，請更新 JSON 中的值，並儲存這些變更。

## <a name="create-rosettanet-agreement"></a>建立 RosettaNet 協議

1. 在 [Azure 入口網站](https://portal.azure.com)中，尋找並開啟您的整合帳戶 (若它尚未開啟)。

1. 在 **概觀**窗格中，選取**協議**圖格。

   ![選擇合約圖格](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. 在 [合約]  之下，選擇 [新增]  。 提供您合約詳細資料。

   ![新增合約詳細資料](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | 屬性 | 必要項 | 描述 |
   |----------|----------|-------------|
   | **名稱** | 是 | 協議名稱 |
   | **合約類型** | 是 | 選取  **RosettaNet**。 |
   | **主機合作夥伴** | 是 | 合約需要主機合作夥伴和來賓合作夥伴。 主機夥伴代表設定合約的組織。 |
   | **主機識別** | 是 | 主控夥伴的識別碼 |
   | **來賓合作夥伴** | 是 | 合約需要主機合作夥伴和來賓合作夥伴。 來賓夥伴代表與主控夥伴有生意往來的組織。 |
   | **來賓身分識別** | 是 | 來賓合作夥伴的識別碼 |
   | **接收設定** | 視情況而異 | 這些屬性套用至主控夥伴接收的所有訊息 |
   | **傳送設定** | 視情況而異 | 這些屬性套用至主控夥伴傳送的所有訊息 |  
   | **RosettaNet PIP 的參考** | 是 | PIP 的協議參考。 所有 RosettaNet 訊息都需要 PIP 的設定。 |
   ||||

1. 若要設定您的合約，從來賓合作夥伴接收內送訊息，請選取**接收設定**。

   ![接收設定](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. 若要啟用簽章或加密內送訊息，如底下**訊息**，選取**訊息應該簽署**或**訊息應該加密**分別。

      | 屬性 | 必要項 | 描述 |
      |----------|----------|-------------|
      | **訊息應該簽署** | 否 | 登入選取的憑證的內送訊息。 |
      | **[MSSQLSERVER 的通訊協定內容]** | 是，如果簽章會啟用 | 要用於簽署的憑證 |
      | **啟用訊息加密** | 否 | 加密內送訊息與選取的憑證。 |
      | **[MSSQLSERVER 的通訊協定內容]** | 是，如果已啟用加密 | 要用於加密的憑證 |
      ||||

   1. 在每個選取項目中，選取 個別[憑證](./logic-apps-enterprise-integration-certificates.md)，您先前新增至您的整合帳戶，要用於簽署或加密。

1. 若要設定您的合約將訊息傳送至來賓夥伴，請選取**傳送設定**。

   ![傳送設定](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. 若要啟用簽章或加密外寄訊息，如底下**訊息**，選取**啟用訊息簽署**或**啟用訊息加密**分別。 在每個選取項目中，選取 個別演算法及[憑證](./logic-apps-enterprise-integration-certificates.md)，您先前新增至您的整合帳戶，要用於簽署或加密。

      | 屬性 | 必要項 | 描述 |
      |----------|----------|-------------|
      | **啟用訊息簽署** | 否 | 簽署外寄訊息的已選取的簽署演算法與憑證。 |
      | **簽署演算法** | 是，如果簽章會啟用 | 簽署演算法，若要使用，根據選取的憑證 |
      | **[MSSQLSERVER 的通訊協定內容]** | 是，如果簽章會啟用 | 要用於簽署的憑證 |
      | **啟用訊息加密** | 否 | 加密外寄的所選取的加密演算法與憑證。 |
      | **加密演算法** | 是，如果已啟用加密 | 要使用加密演算法，根據選取的憑證 |
      | **[MSSQLSERVER 的通訊協定內容]** | 是，如果已啟用加密 | 要用於加密的憑證 |
      ||||

   1. 底下**端點**，指定所需的 Url，用來傳送動作訊息和通知。

      | 屬性 | 必要項 | 描述 |
      |----------|----------|-------------|
      | **動作 URL** |  是 | 要用來傳送動作訊息的 URL。 URL 是必要的欄位的同步和非同步的訊息。 |
      | **通知 URL** | 是 | 要用來傳送通知訊息的 URL。 URL 是必要的欄位，針對非同步的訊息。 |
      ||||

1. 若要設定您的 RosettaNet PIP 參考適用於合作夥伴的合約，選取**RosettaNet PIP 參考**。 底下**PIP 名稱**，選取您先前建立的 pip 名稱。

   ![PIP 的參考](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   您的選擇會填入其餘的屬性，會根據您設定整合帳戶中的 PIP。 如果有必要，您可以變更**PIP 角色**。

   ![選取的 PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

完成這些步驟之後，您即可傳送或接收 RosettaNet 訊息。

## <a name="rosettanet-templates"></a>RosettaNet 範本

加速開發和整合模式的建議，您可以使用邏輯應用程式範本用於解碼和編碼 RosettaNet 訊息。 當您建立邏輯應用程式時，您可以從邏輯應用程式設計工具中的範本資源庫中選取。 您也可以找到這些範本，在[GitHub 存放庫，適用於 Azure Logic Apps](https://github.com/Azure/logicapps)。

![RosettaNet 範本](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>接收，或將 RosettaNet 訊息解碼

1. [建立空白邏輯應用程式](quickstart-create-first-logic-app-workflow.md)。

1. [將整合帳戶連結](logic-apps-enterprise-integration-create-integration-account.md#link-account)在邏輯應用程式。

1. 您可以新增要解碼的 RosettaNet 訊息的動作之前，您必須新增觸發程序來啟動邏輯應用程式，例如要求觸發程序。

1. 加入觸發程序之後, 選擇**新增步驟**。

   ![新增要求觸發程序](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. 在 [搜尋] 方塊中，輸入 「 rosettanet 」，然後選取此動作：**RosettaNet 解碼**

   ![尋找並選取 「 RosettaNet 解碼 」 動作](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. 提供動作的屬性的資訊：

   ![提供動作的詳細資料](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | 屬性 | 必要項 | 描述 |
   |----------|----------|-------------|
   | **訊息** | 是 | 要解碼的 RosettaNet 訊息  |
   | **標頭** | 是 | 提供的值的版本，也就是 RNIF 版本，以及回應類型，這表示合作夥伴之間的通訊類型，而且可以是同步或非同步的 HTTP 標頭 |
   | **角色** | 是 | 角色的主機中的交易夥伴的 PIP |
   ||||

   從 RosettaNet 解碼 動作中，輸出，以及其他屬性，包括**輸出信號**，可以選擇要編碼和傳回給夥伴，或該輸出上採取任何其他動作。

## <a name="send-or-encode-rosettanet-messages"></a>傳送或將 RosettaNet 訊息編碼

1. [建立空白邏輯應用程式](quickstart-create-first-logic-app-workflow.md)。

1. [將整合帳戶連結](logic-apps-enterprise-integration-create-integration-account.md#link-account)在邏輯應用程式。

1. 您可以新增要編碼的 RosettaNet 訊息的動作之前，您必須新增觸發程序來啟動邏輯應用程式，例如要求觸發程序。

1. 加入觸發程序之後, 選擇**新增步驟**。

   ![新增要求觸發程序](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. 在 [搜尋] 方塊中，輸入 「 rosettanet 」，然後選取此動作：**RosettaNet 編碼**

   ![尋找並選取 「 RosettaNet 編碼 」 動作](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. 提供動作的屬性的資訊：

   ![提供動作的詳細資料](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | 屬性 | 必要項 | 描述 |
   |----------|----------|-------------|
   | **訊息** | 是 | 要編碼的 RosettaNet 訊息  |
   | **主機合作夥伴** | 是 | 主機夥伴名稱 |
   | **來賓合作夥伴** | 是 | 來賓夥伴名稱 |
   | **PIP 代碼** | 是 | PIP 代碼 |
   | **PIP 版本** | 是 | PIP 版本 |  
   | **PIP 執行個體的身分識別** | 是 | 此 PIP 訊息的唯一識別項 |  
   | **訊息類型** | 是 | 要編碼之訊息的型別 |  
   | **角色** | 是 | 主控夥伴的角色 |
   ||||

   現在準備好要傳送給夥伴已編碼的訊息。

1. 若要傳送已編碼的訊息，此範例會使用**HTTP**動作，也就是重新命名為 「 HTTP-編碼的傳送訊息給夥伴 」。

   ![傳送 RosettaNet 訊息的 HTTP 動作](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   根據 RosettaNet 標準 PIP 定義的所有步驟都都完成時，只是完成商務交易。

1. 主應用程式會將編碼的訊息傳送給夥伴之後，主應用程式等待訊號通知。 若要完成這項工作中，新增**RosettaNet 等候回應**動作。

   ![新增 「 RosettaNet 等候回應 」 動作](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   要用於等候與重試次數的持續時間為基礎的整合帳戶中的 PIP 組態。 如果未收到回應，這個動作會產生失敗的通知。 若要處理的重試次數，一律放**編碼**並**等待回應**中的動作**直到**迴圈。

   ![Until 迴圈與 RosettaNet 動作](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>後續步驟

* 了解驗證和轉換方式，以及[企業整合套件](../logic-apps/logic-apps-enterprise-integration-overview.md)中的其他訊息作業
* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
