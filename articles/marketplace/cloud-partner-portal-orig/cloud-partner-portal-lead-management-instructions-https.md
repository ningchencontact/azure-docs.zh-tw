---
title: HTTPS 端點 | Microsoft Docs
description: 針對 Https 設定潛在客戶管理。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: fd13a7281c7e8702fd199364261ebcd458db0555
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805476"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>使用 HTTPS 端點設定潛在客戶管理

您可以使用 HTTPS 端點處理可以寫入到 CRM 系統的 Azure Marketplace 和 AppSource 潛在客戶。 此文章說明如何使用 Microsoft Flow 自動化服務設定潛在客戶管理。


## <a name="create-a-flow-using-microsoft-flow"></a>使用 Microsoft Flow 建立流程

1.  開啟 [Flow](https://flow.microsoft.com/) 網頁。 選取 [登入] 或選取 [免費註冊] 以建立免費的 Flow 帳戶。

2.  登入並選取功能表列上的 [我的流程]。

    ![我的流程](./media/cloud-partner-portal-lead-management-instructions-https/image001.png)

3.  選取 [從空白建立]。

    ![從空白建立](./media/cloud-partner-portal-lead-management-instructions-https/image003.png)


4.  選取 [要求/回應] 連接器，然後再搜尋要求觸發程序。 

    ![從空白建立](./media/cloud-partner-portal-lead-management-instructions-https/image005.png)

5. 選取 [要求] 觸發程序。
    ![要求觸發程序](./media/cloud-partner-portal-lead-management-instructions-https/image007.png)


6.  將此文章結尾的 **JSON 範例**複製到**要求本文 JSON 結構描述**。

7.  新增步驟，然後利用建立新記錄的動作，選擇 CRM 系統。 下一個螢幕擷取畫面會顯示 [Dynamics 365 - 建立新記錄] 做為範例。

    ![建立新的記錄](./media/cloud-partner-portal-lead-management-instructions-https/image009.png)

8.  提供連接器的連線輸入，然後選取 [潛在客戶] 實體。

    ![選取潛在客戶](./media/cloud-partner-portal-lead-management-instructions-https/image011.png)

9.  Flows 會顯示一個表單來提供潛在客戶資訊。 您可以選擇新增動態內容來對應輸入要求中的項目。

    ![新增動態內容](./media/cloud-partner-portal-lead-management-instructions-https/image013.png)

10.  對應您想要的欄位，然後選取 [儲存]，儲存您的流程。

11. 在要求中便會建立一個 HTTP POST URL。 複製這個 URL，並使用它作為 HTTPS 端點。

    ![HTTP Post URL](./media/cloud-partner-portal-lead-management-instructions-https/image015.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>設定您的供應項目，以便將潛在客戶傳送至 HTTPS 端點

當您為供應項目設定潛在客戶管理資訊時，請選取 [HTTPS 端點] 作為 [潛在客戶目的地]，並貼入您在上一個步驟中複製的 HTTP POST URL。  

![新增動態內容](./media/cloud-partner-portal-lead-management-instructions-https/image017.png)

產生潛在客戶時，Microsoft 會將潛在客戶傳送至 Flow，如此會路由傳送至您所設定的 CRM 系統。


## <a name="json-example"></a>JSON 範例

``` json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
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
