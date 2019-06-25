---
title: 管理 Azure DevTest Labs 中的自動關閉原則 |Microsoft Docs
description: 了解如何設定實驗室自動關閉原則，使虛擬機器會自動關閉時未使用。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2019
ms.author: spelluru
ms.openlocfilehash: 9adf8dd4a5a3c469ed130b29308a0d828aee40bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65873986"
---
# <a name="manage-autoshutdown-policies-for-a-lab-in-azure-devtest-labs"></a>管理 Azure DevTest Labs 中實驗室自動關閉原則
Azure DevTest Labs 可讓您管理每個實驗室的原則 (設定)，以控制實驗室的成本並儘可能避免浪費。 這篇文章會示範如何設定實驗室帳戶的自動關閉原則，並在 實驗室帳戶中設定實驗室自動關閉設定。 若要了解如何設定每個實驗室原則，請參閱[在 Azure DevTest Labs 中定義實驗室原則](devtest-lab-set-lab-policy.md)。  

## <a name="set-auto-shutdown-policy-for-a-lab"></a>設定實驗室自動關閉原則
身為實驗室擁有者，您可以在實驗室中設定所有 VM 的關機排程。 如此一來，您可經由執行未使用 (閒置) 的機器來節省成本。 您可以在所有的實驗室 VM 上集中強制執行關機原則，但也可讓實驗室使用者免於為其個別機器設定排程。 這項功能可讓您設定實驗室排程的原則，從完全無法控制，以至完全控制實驗室使用者。 身為實驗室擁有者，您可以採取下列步驟來設定此原則：

1. 在實驗室首頁上，選取 [設定與原則]  。
2. 左側功能表的 [排程]  區段中選取 [自動關機原則]  。
3. 選取其中一個選項。 下列各節提供有關這些選項的詳細資料：設定原則只適用於實驗室中建立新的 Vm 和不到現有的 Vm。 

    ![自動關閉原則選項](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

## <a name="configure-auto-shutdown-settings"></a>設定自動關閉設定
自動關閉原則有助於將實驗室浪費的降至最低，可讓您指定這個實驗室的 Vm 關閉的時間。

若要檢視 (及變更) 實驗室的原則，請依照下列步驟操作：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [所有服務]  ，然後從清單中選取 [DevTest Labs]  。
3. 從實驗室清單中，選取所需的實驗室。   
4. 選取 [組態和原則]  。

    ![原則設定窗格](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. 在實驗室的**組態和原則**窗格中，選取**自動關機**之下**排程**。
   
    ![自動關閉](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. 選取 [開啟]  來啟用此原則，以及選取 [關閉]  來停用它。
7. 如果您啟用這個原則，請指定時間 (和時區) 以關閉目前實驗室中的所有 VM。
8. 指定 **[是]** 或是**否**選項來指定自動關閉時間之前 15 分鐘傳送通知。 如果您選擇 [是]  ，請輸入 Webhook URL 端點或電子郵件地址，指定您要在哪裡張貼或傳送通知。 使用者會收到通知，並且給予延遲關機的選項。 如需詳細資訊，請參閱 <<c0> [ 通知](#notifications)一節。 
9. 選取 [ **儲存**]。

    根據預設，這個原則一經啟用，就會套用到目前實驗室的所有 VM。 若要從特定的 VM 中移除此設定，請開啟 VM 的 [管理] 窗格並變更其**自動關閉**設定。

### <a name="user-sets-a-schedule-and-can-opt-out"></a>使用者可設定排程，且可退出
如果您將實驗室設定為此原則，則實驗室使用者可以覆寫或選擇不使用實驗室排程。 此選項可讓實驗室使用者完全控制其 VM 的自動關機排程。 實驗室使用者在其 VM 自動關機排程頁面中看不到任何變更。

![自動關閉原則選項-1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>使用者可設定排程，但無法退出
如果您將實驗室設定為此原則，則實驗室使用者可以覆寫實驗室排程。 不過，他們無法選擇不使用自動關機原則。 此選項可確保您實驗室中的每部機器都會依循自動關機排程。 實驗室使用者可以更新其 VM 的自動關機排程，以及設定關機通知。

![自動關閉原則選項-2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>使用者無法控制實驗室管理員所設定的排程
如果您將實驗室設定為此原則，則實驗室使用者不能覆寫或選擇不使用實驗室排程。 此選項可讓實驗室管理員完全控制實驗室中每部機器的排程。 實驗室使用者只可以設定其 VM 的自動關機通知。

![自動關閉原則選項-3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="notifications"></a>通知
一旦實驗室擁有者所設定的自動關閉，通知會傳送至實驗室使用者之前，觸發任何其 Vm 自動關閉的 15 分鐘會受到影響。 此選項可讓實驗室使用者可以儲存在關機之前工作機會。 通知也會提供連結每個 VM 執行下列動作：

- 略過這一次的自動關閉
- 將延遲的自動關閉 1 小時或 2 個小時，以便他們可以繼續使用在 VM 上。

透過設定的 web 攔截端點時傳送通知或實驗室擁有者的自動關閉設定中所指定的電子郵件地址。 Webhook 可讓您建立或設定訂閱特定事件的整合。 其中一個事件觸發時，DevTest Labs 將 HTTP POST 將內容傳送至 webhook 的已設定的 URL。 如需 webhook 的詳細資訊，請參閱[建立 webhook 或 API Azure 函式](../azure-functions/functions-create-a-web-hook-or-api-function.md)。 

我們建議您將使用 web 勾點，因為它們廣泛支援的各種應用程式 （例如 Slack、 Azure Logic Apps，與等），而且可讓您實作自己的方法來傳送通知。 例如，本文將逐步引導您了解如何使用 Azure Logic Apps，收到電子郵件自動關閉通知。 首先，讓我們快速瀏覽的基本步驟，以啟用您的實驗室中的自動關閉通知。   

## <a name="create-a-logic-app-that-receives-email-notifications"></a>建立邏輯應用程式，以接收電子郵件通知
[Azure Logic Apps](../logic-apps/logic-apps-overview.md)提供許多的立即可用的連接器，可讓您輕鬆地整合服務與其他用戶端，例如 Office 365 和 twitter。 概括而言，若要設定電子郵件通知的邏輯應用程式的步驟可以分成四個階段： 

- 建立邏輯應用程式。 
- 設定內建的範本。
- 整合您的電子郵件用戶端
- 取得 Webhook URL。

### <a name="create-a-logic-app"></a>建立邏輯應用程式
若要開始，您的 Azure 訂用帳戶中建立邏輯應用程式使用下列步驟：

1. 選取  **+ 建立資源**在左側功能表中，選取**整合**，然後選取**邏輯應用程式**。 

    ![新的邏輯應用程式功能表](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. 在 **邏輯應用程式-建立**頁面上，請遵循下列步驟： 
    1. 請輸入**名稱**邏輯應用程式。
    2. 選取您的 Azure **訂用帳戶**。
    3. 建立新的**資源群組**，或選取現有的資源群組。 
    4. 選取 **位置**邏輯應用程式。 

        ![新的邏輯應用程式-設定](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. 在 **通知**，選取**移至資源**通知。 

    ![前往資源](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. 選取 **邏輯應用程式設計工具**下方**部署工具**類別目錄。

    ![選取 HTTP 要求/回應](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. 在  **HTTP 要求-回應**頁面上，選取**使用此範本**。 

    ![選取 使用此範本選項](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. 複製下列 JSON**要求本文 JSON 結構描述**區段： 

    ```json
    {
        "$schema": "http://json-schema.org/draft-04/schema#",
        "properties": {
            "delayUrl120": {
                "type": "string"
            },
            "delayUrl60": {
                "type": "string"
            },
            "eventType": {
                "type": "string"
            },
            "guid": {
                "type": "string"
            },
            "labName": {
                "type": "string"
            },
            "owner": {
                "type": "string"
            },
            "resourceGroupName": {
                "type": "string"
            },
            "skipUrl": {
                "type": "string"
            },
            "subscriptionId": {
                "type": "string"
            },
            "text": {
                "type": "string"
            },
            "vmName": {
                "type": "string"
            }
        },
        "required": [
            "skipUrl",
            "delayUrl60",
            "delayUrl120",
            "vmName",
            "guid",
            "owner",
            "eventType",
            "text",
            "subscriptionId",
            "resourceGroupName",
            "labName"
        ],
        "type": "object"
    }
    ```
    
    ![要求本文 JSON 結構描述](./media/devtest-lab-auto-shutdown/request-json.png)
7. 選取  **+ 新步驟**在設計師中，並遵循下列步驟：
    1. 搜尋**Office 365 Outlook-傳送電子郵件**。 
    2. 選取 **傳送電子郵件**從**動作**。 
    
        ![傳送電子郵件選項](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. 選取 **登入**登入您的電子郵件帳戶。 
    4. 選取  **TO**欄位，然後選擇 擁有者。
    5. 選取 **主旨**，並輸入電子郵件通知的主旨。 例如: "關機的實驗室機器 vmName: labName。 」
    6. 選取 **主體**，並定義電子郵件通知的內文內容。 例如:"vmName 排定在 15 分鐘內關機。 按一下以略過此關機：URL。 一小時的延遲關機： delayUrl60。 2 小時的延遲關機： delayUrl120。 」

        ![要求本文 JSON 結構描述](./media/devtest-lab-auto-shutdown/email-options.png)
1. 在工具列上選取 [儲存]  。 現在，您可以複製**HTTP POST URL**。 選取 [複製] 按鈕，將 URL 複製到剪貼簿。 

    ![WebHook URL](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="next-steps"></a>後續步驟
若要了解如何設定所有的原則，請參閱[在 Azure DevTest Labs 中定義實驗室原則](devtest-lab-set-lab-policy.md)。
