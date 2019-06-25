---
title: AS2 郵件設定-Azure Logic Apps
description: 參考指南 AS2 傳送和接收 Azure Logic Apps 與企業整合套件中的設定
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: ead92094b9af1dff56ff68e1ff58a3a4cdd9dca5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "63769447"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>Azure Logic Apps 與企業整合套件中的 AS2 訊息設定的參考

此參考描述您可以設定用於指定 AS2 協議的傳送和接收交易夥伴之間訊息的處理方式的屬性。 設定這些屬性，根據您與夥伴交換訊息，與您的合約。

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>AS2 接收設定

![選取 [接收設定]](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| 屬性 | 必要項 | 描述 |
|----------|----------|-------------|
| **覆寫訊息屬性** | 否 | 屬性設定，覆寫內送訊息上的屬性。 |
| **訊息應該簽署** | 否 | 指定是否必須以數位方式簽署所有傳入訊息。 如果您需要簽章，從**憑證**清單中，選取現有來賓夥伴公開憑證來驗證訊息簽章。 如果您沒有憑證，會將進一步了解[將憑證新增](../logic-apps/logic-apps-enterprise-integration-certificates.md)。 |
| **訊息應該加密** | 否 | 指定是否必須加密所有內送訊息。 未加密的訊息會遭到拒絕。 如果您需要加密，從**憑證**清單中，選取現有主控夥伴私人憑證以解密內送訊息。 如果您沒有憑證，會將進一步了解[將憑證新增](../logic-apps/logic-apps-enterprise-integration-certificates.md)。 |
| **訊息應該壓縮** | 否 | 指定是否必須壓縮所有的內送訊息。 未壓縮的訊息會遭到拒絕。 |
| **不允許訊息識別碼重複** | 否 | 指定是否要允許的訊息重複的識別碼。 如果您不允許重複的識別碼，請選取 檢查之間的天數。 您也可以選擇是否要擱置重複的項目。 |
| **MDN 文字** | 否 | 指定預設郵件處置通知 (MDN)，您想要傳送給訊息傳送者。 |
| **傳送 MDN** | 否 | 指定是否要傳送的訊息接收同步 Mdn。  |
| **傳送簽署的 MDN** | 否 | 指定是否要傳送已簽署的 Mdn 接收之訊息。 如果您需要簽章，從**MIC 演算法**清單中，選取要用於簽署訊息的演算法。 |
| **傳送非同步 MDN** | 否 | 指定是否要以非同步方式傳送 Mdn。 如果您在選取非同步的 Mdn **URL**方塊中，指定要傳送 Mdn 的 URL。 |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>AS2 傳送設定

![選取 [傳送設定]](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| 屬性 | 必要項 | 描述 |
|----------|----------|-------------|
| **啟用訊息簽署** | 否 | 指定是否必須以數位方式簽署所有外寄訊息。 如果您需要簽署，請選取下列值： <p>-從**簽署演算法**清單中，選取要用於簽署訊息的演算法。 <br>-從**憑證**清單中，選取現有主控夥伴私人憑證以簽署訊息。 如果您沒有憑證，會將進一步了解[將憑證新增](../logic-apps/logic-apps-enterprise-integration-certificates.md)。 |
| **啟用訊息加密** | 否 | 指定是否必須加密所有外寄訊息。 如果您需要加密，請選取下列值： <p>-從**加密演算法**清單中，選取 來賓夥伴公開憑證演算法，要用來加密訊息。 <br>-從**憑證**清單中，選取現有來賓合作夥伴私人憑證來加密外寄訊息。 如果您沒有憑證，會將進一步了解[將憑證新增](../logic-apps/logic-apps-enterprise-integration-certificates.md)。 |
| **啟用訊息壓縮** | 否 | 指定是否必須壓縮所有外寄訊息。 |
| **展開 HTTP 標頭** | 否 | 將 HTTP`content-type`放在一行的標頭。 |
| **傳輸 MIME 標頭中的檔案名稱** | 否 | 指定是否要包含在 MIME 標頭中的檔案名稱。 |
| **要求 MDN** | 否 | 指定是否要接收的所有外寄訊息的訊息處置通知 (Mdn)。 |
| **要求簽署的 MDN** | 否 | 指定是否要接收的所有外寄訊息已簽署的 Mdn。 如果您需要簽章，從**MIC 演算法**清單中，選取要用於簽署訊息的演算法。 |
| **要求非同步 MDN** | 否 | 指定是否要以非同步方式接收 Mdn。 如果您在選取非同步的 Mdn **URL**方塊中，指定要傳送 Mdn 的 URL。 |
| **啟用 NRR** | 否 | 指定是否需要不可否認性接收 (NRR)。 此種通訊屬性提供的辨識項收到的資料。 |
| **SHA2 演算法格式** | 否 | 指定要用於簽署外寄 AS2 訊息或 MDN 的標頭中的 MIC 演算法格式 |
||||

## <a name="next-steps"></a>後續步驟

[交換 AS2 訊息](../logic-apps/logic-apps-enterprise-integration-as2.md)
