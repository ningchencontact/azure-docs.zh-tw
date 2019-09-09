---
title: HTTPS 端點 |Azure Marketplace
description: 設定 HTTPS 端點的潛在客戶管理。
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: evansma
ms.openlocfilehash: 6a34bdcab5a13af682515bbae96e9a1800ccc37f
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2019
ms.locfileid: "69902191"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>使用 HTTPS 端點設定潛在客戶管理

如果您的客戶關係管理（CRM）系統未在合作夥伴中心內明確支援以接收 Azure Marketplace 和 AppSource 潛在客戶，您可以使用 MS Flow 中的 HTTPS 端點來處理這些潛在客戶。 有了 HTTPS 端點，這些潛在客戶可以用電子郵件通知的形式送出，或寫入 MS Flow 支援的客戶關係管理（CRM）系統。 本文中的指示將引導您完成使用 Microsoft Flow 建立新流程的基本程式，該程式將會產生您將在發佈入口網站中輸入的 HTTP POST URL，以供潛在客戶管理 > **HTTPS 端點 URL**欄位。 此外，您也可以透過稱為[Postman](https://www.getpostman.com/downloads/)的工具（可在線上找到）協助來測試流程。

## <a name="create-a-flow-using-microsoft-flow"></a>使用 Microsoft Flow 建立流程

1. 開啟 [Flow](https://flow.microsoft.com/) 網頁。 選取 [登**入**]，或如果您還沒有帳戶，請選取 [**免費註冊**] 以建立免費流程帳戶。

2. 登入並選取功能表列上的 [我的流程]。

3. 選取 [ **+ 自動化–從空白**]。

    ![我的流程 + 自動化-從空白](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

4. 在 [*組建自動化流程*] 視窗中，選取 [**略過**]。 

    ![組建自動化流程-略過](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

5. 在 [搜尋連接器與觸發程序] 欄位中，輸入「要求」以尋找要求連接器。
6. 在 [觸發程序] 底下，選取 [收到 HTTP 要求時]。 

    ![要求連接器-觸發程式](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

7. 在 [*收到 HTTP 要求時*] 視窗上，複製下列 json 架構，並將其貼到 [**要求本文 json 架構**] 文字方塊中。 Microsoft 會使用此架構來包含您的潛在客戶資料。

    ![要求連接器-觸發程式](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

    **JSON 結構描述**

    ```JSON
    {
      "$schema": "https://json-schema.org/draft-04/schema#",
      "definitions": {},
      "id": "http://example.com/example.json",
      "properties": {
        "ActionCode": {
          "id": "/properties/ActionCode",
          "type": "string"
        },
        "OfferTitle": {
          "id": "/properties/OfferTitle",
          "type": "string"
        },
        "LeadSource": {
          "id": "/properties/LeadSource",
          "type": "string"
        },
        "UserDetails": {
          "id": "/properties/UserDetails",
          "properties": {
            "Company": {
              "id": "/properties/UserDetails/properties/Company",
              "type": "string"
            },
            "Country": {
              "id": "/properties/UserDetails/properties/Country",
              "type": "string"
            },
            "Email": {
              "id": "/properties/UserDetails/properties/Email",
              "type": "string"
            },
            "FirstName": {
              "id": "/properties/UserDetails/properties/FirstName",
              "type": "string"
            },
            "LastName": {
              "id": "/properties/UserDetails/properties/LastName",
              "type": "string"
            },
            "Phone": {
              "id": "/properties/UserDetails/properties/Phone",
              "type": "string"
            },
            "Title": {
              "id": "/properties/UserDetails/properties/Title",
              "type": "string"
            }
          },
          "type": "object"
        }
      },
      "type": "object"
    }
    ```

>[!Note]
>此時，您可以選擇 [連線到 CRM 系統] 或 [設定電子郵件通知]。 根據您的選擇，遵循其餘的指示。

### <a name="to-connect-to-a-crm-system"></a>若要連線到 CRM 系統

1. 選取 [+ 新步驟]。
2. 藉由搜尋 [*搜尋連接器] 和 [動作*]，選擇您選擇的 CRM 系統，然後在 [*動作*] 區段底下選取 [執行]，以建立新的記錄。 下列螢幕擷取畫面顯示**Dynamics 365-建立**新記錄做為範例。

    ![建立新記錄](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

3. 提供與 CRM 系統相關聯的**組織名稱**。 從 [實體名稱] 下拉式清單選取 [潛在客戶]。

    ![選取潛在客戶](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

4. Flow 會顯示一個表單來提供潛在客戶資訊。 您可以選擇新增動態內容，以對應輸入要求中的專案。 下列螢幕擷取畫面顯示 **OfferTitle** 作為範例。

    ![新增動態內容](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

5. 對應您想要的欄位，然後選取 [儲存]，儲存您的流程。 隨即建立 HTTP POST URL，並可在 [*收到 HTTP 要求時*] 視窗中存取。 使用位於 HTTP POST URL 右邊的複製控制項來複製這個 URL –這很重要，因為您不會誤錯過整個 URL 的任何部分。 儲存此 URL，因為當您在發佈入口網站中設定潛在客戶管理時，將會需要它。

    ![收到 HTTP 要求時。](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="to-set-up-email-notification"></a>若要設定電子郵件通知

1. 既然您已完成 JSON 架構，請選取 [ **+ 新增步驟**]。
2. 在 [選擇動作] 底下，選取 [動作]。
3. 在 [**動作**] 底下，選取 **[傳送電子郵件（Office 365 Outlook）** ]。

    >[!Note]
    >如果您想要使用不同的電子郵件提供者搜尋，請改為選取 [*傳送電子郵件通知（郵件）* ] 做為動作。

    ![新增電子郵件動作](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

4. 在 [**傳送電子郵件**] 視窗中，設定下列必要欄位：

   - **若要**-輸入至少一個有效的電子郵件地址，將會在其中傳送潛在客戶。
   - **主旨** - Flow 可讓您選擇新增動態內容，像是以下螢幕擷取畫面中的 **LeadSource**。 一開始先輸入功能變數名稱，然後按一下快顯視窗中的 [動態內容挑選] 清單。 

        >[!Note] 
        > 加入功能變數名稱時，您可以使用 "：" 來追蹤每一個名稱，然後輸入來建立新的資料列。 加入功能變數名稱之後，您就可以從動態挑選清單新增每個相關聯的參數。

        ![使用動態內容新增電子郵件動作](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Body** -從動態內容挑選清單中，在電子郵件的本文中新增您想要的資訊。 例如，LastName、FirstName、Email 和 Company。 <br> <br> 當您完成設定電子郵件通知時，它看起來會類似以下螢幕擷取畫面中的範例。


       ![新增電子郵件動作](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

5. 選取 [儲存] 完成流程。 HTTP POST URL 已建立，並可在 [*收到 HTTP 要求時*] 視窗中存取。 使用位於 HTTP POST URL 右邊的複製控制項來複製這個 URL –這很重要，因為您不會誤錯過整個 URL 的任何部分。 儲存此 URL，因為當您在發佈入口網站中設定潛在客戶管理時，將會需要它。

   ![HTTP POST URL ](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>測試

您可以使用名為[Postman](https://app.getpostman.com/app/download/win64)的工具（可在線上下載），利用下列步驟來測試所有專案是否如預期般運作。 這適用于 Windows。 

1. 啟動 Postman，然後選取 [**新增** > **要求**] 以設定您的測試控管。 

   ![設定測試控管的要求](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

2. 填寫 [*儲存要求*] 表單，然後**儲存**至您所建立的資料夾。

   ![儲存要求](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

3. 從下拉式清單中選取 [ **POST** ]。 

   ![測試我的流程](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

4. 從您在 MS Flow 中建立的流程貼上 HTTP POST URL，其中會顯示 [*輸入要求 URL*]。

   ![貼上 HTTP POST URL](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

5. 回到[flow 並尋找](https://flow.microsoft.com/)您所建立的流程來傳送潛在客戶，方法是移至 [流程] 功能表列的 [我的**流程**]。  選取流程名稱旁邊的3個點，然後選取 [**編輯**]。

   ![我的流程-編輯](./media/commercial-marketplace-lead-management-instructions-https/my-flows-edit.png)

6. 選取右上角的 [**測試**]，選取 [我將執行觸發程式動作]，然後選取 [**測試**]。 您會在畫面頂端看到指出測試已啟動的指示。

   ![測試流程-觸發程式](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

7. 回到您的 Postman 應用程式，然後在您貼上 HTTPS URL 的上選取 [**傳送**]。

   ![測試我的流程-傳送](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

8. 返回您的流程並檢查結果。 如果所有專案都如預期般運作，您會看到訊息指出已成功。

   ![流程-檢查結果](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

9. 您也應該會收到一封電子郵件。 檢查您的電子郵件收件匣。 

    >[!Note] 
    >如果您沒有看到來自測試的電子郵件，請檢查您的垃圾郵件及垃圾郵件資料夾。 您會在下面看到設定電子郵件通知時所新增的欄位標籤。 如果這是從您的供應專案產生的實際潛在客戶，您也會在本文和主旨行中看到潛在客戶連絡人的實際資訊。

   ![收到的電子郵件](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>設定您的供應項目，以便將潛在客戶傳送至 HTTPS 端點

當您準備好在發佈入口網站中為您的供應專案設定潛在客戶管理資訊時，請遵循下列步驟：

1. 流覽至供應專案的 [**供應專案設定**] 頁面。
2. 選取 [潛在客戶管理] 區段下的 **[連接]** 。
3. 在 [連線詳細資料] 快顯視窗中，針對 [**潛在客戶目的地**] 選取 [ **HTTPS 端點**]，並將您在先前步驟中所建立的流程中的 HTTP POST url 貼入 [ **HTTPS 端點 URL** ] 欄位。
4. 選取 [ **儲存**]。 

>[!Note] 
>您必須先完成供應專案的其餘部分設定並加以發佈，才能接收供應專案的潛在客戶。

產生潛在客戶時，Microsoft 會將潛在客戶傳送至 Flow；如此一來，這些資訊就會路由傳送至您所設定的 CRM 系統或電子郵件地址。

![潛在客戶管理-connect](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![連線詳細資料](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![連線詳細資料](./media/commercial-marketplace-lead-management-instructions-https/connection-details-1.png)

