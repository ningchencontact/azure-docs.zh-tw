---
title: 連接到 IBM MQ server-Azure Logic Apps
description: 傳送和擷取 Azure 或內部部署 IBM MQ server 與 Azure Logic Apps 的訊息
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: valrobb
ms.author: valthom
ms.reviewer: chrishou, LADocs
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: a2894799946d069916b27a4f5bcc7bd3244705b2
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273116"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>從 Azure Logic Apps 連接到 IBM MQ server

IBM MQ 連接器會傳送和擷取儲存在 IBM MQ server 在內部部署環境還是 Azure 中的訊息。 此連接器包含透過 TCP/IP 網路與遠端 IBM MQ Server 通訊的 Microsoft MQ 用戶端。 本文提供使用 MQ 連接器的入門指南。 您可以開始瀏覽佇列上的單一訊息，然後再嘗試其他動作。

IBM MQ 連接器包含這些動作，但會提供任何觸發程序：

- 瀏覽單一訊息，而不從 IBM MQ server 刪除訊息
- 瀏覽訊息批次，而不從 IBM MQ server 刪除訊息
- 接收單一訊息，並從 IBM MQ server 刪除訊息
- 接收一批訊息，並從 IBM MQ server 刪除訊息
- 將單一訊息傳送至 IBM MQ server

## <a name="prerequisites"></a>必要條件

* 如果您使用內部部署 MQ server 上，[安裝在內部部署資料閘道](../logic-apps/logic-apps-gateway-install.md)您網路內之伺服器上。 在內部部署資料閘道安裝所在的伺服器也必須擁有.NET Framework 4.6 MQ 連接器安裝才能運作。 您也必須建立在 Azure 中的資源，在內部部署資料閘道的選項。 如需詳細資訊，請參閱 <<c0> [ 設定資料閘道連線](../logic-apps/logic-apps-gateway-connection.md)。

  不過，如果公開可用或在 Azure 中使用 MQ server，您不必使用部署資料閘道。

* 正式支援的 IBM WebSphere MQ 版本：

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0

* 您要新增的 MQ 動作的邏輯應用程式。 此邏輯應用程式必須使用相同的位置，為您的內部部署資料閘道連線，而且必須已經具有啟動工作流程的觸發程序。 

  MQ 連接器沒有任何觸發程序，因此您必須先將觸發程序加入到您的邏輯應用程式。 例如，您可以使用循環觸發程序。 如果您不熟悉 logic apps，請試試[建立第一個邏輯應用程式的快速入門](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 

## <a name="browse-a-single-message"></a>瀏覽單一訊息

1. 在邏輯應用程式，以觸發程序或另一個動作，選擇**新增步驟**。 

1. 在 [搜尋] 方塊中，輸入"mq"，然後選取此動作：**瀏覽訊息**

   ![瀏覽訊息](media/connectors-create-api-mq/Browse_message.png)

1. 如果您沒有現有的 MQ 連線，建立連線：  

   1. 在動作中，選取**透過內部部署資料閘道連線**。
   
   1. 輸入 MQ server 的屬性。  

      針對 [伺服器]  ，您可以輸入 MQ Server 的名稱，或輸入 IP 位址，後面接著冒號和連接埠號碼。
    
   1. 開啟**閘道**清單中，它會顯示任何先前設定的閘道連線。 選取您的閘道。
    
   1. 完成之後，請選擇 [建立]  。 
   
      您的連線是由本範例所示：

      ![Connection Properties](media/connectors-create-api-mq/Connection_Properties.png)

1. 設定動作的屬性：

   * **佇列**:指定連接不同的佇列。

   * **MessageId**， **CorrelationId**， **GroupId**，和其他屬性：瀏覽訊息，根據不同 MQ 訊息屬性

   * **IncludeInfo**:指定 **，則為 True**在輸出中包含額外的訊息資訊。 或者，您也可以指定**False**不在輸出中包含額外的訊息資訊。

   * **Timeout**：輸入的值來決定等候訊息到達空白查詢中的時間長度。 如果未輸入任何內容，就會取出佇列中的第一個訊息，且不會花費時間等候要顯示的訊息。

     ![瀏覽訊息屬性](media/connectors-create-api-mq/Browse_message_Props.png)

1. **儲存**您的變更，然後**執行**邏輯應用程式。

   ![儲存並執行](media/connectors-create-api-mq/Save_Run.png)

   完成執行之後，會顯示執行中的步驟，以及您可以檢閱輸出。

1. 若要檢閱每個步驟的詳細資訊，請選擇綠色的核取記號。 若要檢閱的輸出資料的詳細資訊，請選擇**顯示原始輸出**。

   ![瀏覽訊息輸出](media/connectors-create-api-mq/Browse_message_output.png)  

   以下是一些範例原始輸出：

   ![瀏覽訊息原始輸出](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. 如果您設定**IncludeInfo**為 true，下列輸出會顯示：

   ![瀏覽訊息包含資訊](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>瀏覽多個訊息

[瀏覽訊息]  動作包含 **BatchSize** 選項，指出應該從佇列傳回的訊息數量。  如果 **BatchSize** 沒有項目，就會傳回所有的訊息。 傳回的輸出是陣列訊息。

1. 當您將加入**瀏覽訊息**動作，第一個先前預設會選取已設定的連線。 若要建立新的連接，請選擇**變更連接**。 或者，您也可以選取不同的連接。

1. 邏輯應用程式執行完成之後，以下是一些範例輸出**瀏覽訊息**動作：

   ![瀏覽訊息輸出](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>接收單一訊息

[接收訊息]  動作具有與 [瀏覽訊息]  動作相同的輸入和輸出。 使用 [接收訊息]  時，會從佇列將訊息刪除。

## <a name="receive-multiple-messages"></a>接收多個訊息

[接收訊息]  動作具有與 [瀏覽訊息]  動作相同的輸入和輸出。 使用 [接收訊息]  時，會從佇列將訊息刪除。

如果進行瀏覽或接收時佇列中沒有任何訊息，步驟會失敗，此輸出：  

![MQ 無訊息錯誤](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>傳送訊息

當您將加入**傳送訊息**動作，第一個先前預設會選取已設定的連線。 若要建立新的連接，請選擇**變更連接**。 或者，您也可以選取不同的連接。

1. 選取有效的訊息類型：**資料包**，**回覆**，或**要求**  

   ![傳送訊息屬性](media/connectors-create-api-mq/Send_Msg_Props.png)

1. 邏輯應用程式完成執行之後，以下是一些範例輸出**傳送訊息**動作：

   ![傳送訊息輸出](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>連接器參考

取得的技術詳細動作和限制的詳細資訊，其中會描述連接器的 OpenAPI (以前稱為 Swagger) 描述，檢閱連接器[參考頁面](/connectors/mq/)。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)
