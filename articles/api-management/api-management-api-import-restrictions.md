---
title: 在 Azure API 管理 API 匯入的限制和已知問題 | Microsoft Docs
description: 有關使用 Open API、WSDL 或 WADL 格式匯入 Azure API 管理的已知問題和限制的詳細資料。
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2019
ms.author: apimpm
ms.openlocfilehash: bf39e508b8e4c883934b51fdc99eaef96caf1235
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2019
ms.locfileid: "70018218"
---
# <a name="api-import-restrictions-and-known-issues"></a>API 匯入的限制和已知問題

## <a name="about-this-list"></a>關於本清單

匯入 API 時，您可能會遇到一些限制或識別問題，必須先解決這些問題，您才能成功匯入。 這篇文章說明這些項目，依 API 的匯入格式整理說明。

## <a name="open-api"> </a>OpenAPI/Swagger

如果您在匯入 OpenAPI 文件時收到錯誤，請確定您已事先對其進行了驗證。 您可使用 Azure 入口網站中的設計工具 ([設計] - [前端] - [OpenAPI 規格編輯器])，或使用協力廠商工具 (例如 <a href="https://editor.swagger.io">Swagger 編輯器</a>) 來進行。

### <a name="open-api-general"> </a>一般

-   整個路徑和查詢所需的參數必須具有唯一的名稱。 (在 OpenAPI 中，參數名稱只需要在某個位置內是唯一的，例如路徑、查詢、標頭。 不過，在 API 管理中，可透過路徑和查詢參數來區分作業 (OpenAPI 並不支援此做法)。 這就是為什麼參數名稱必須在整個 URL 範本內是唯一的。)
-   ref 指標無法參考外部檔案。 **\$**
-   **x-ms-paths** 和 **x-servers** 是唯二支援的副檔名。
-   在匯入時系統會忽略自訂副檔名，不會儲存或保留用於匯出。
-   **遞迴** - API 管理不支援以遞迴方式定義的定義 (例如，結構描述參考本身)。
-   來源檔案 URL (如果有的話) 會套用到相對的伺服器 URL。
-   系統會忽略安全性定義。

### <a name="open-api-v2"> </a>OpenAPI 第 2 版

-   僅支援 JSON 格式。

### <a name="open-api-v3"> </a>OpenAPI 第 3 版

-   如果已指定許多**伺服器**，API 管理會試著選取第一個 HTTPs URL。 如果沒有任何 HTTPs URLs - 第一個 HTTP URL。 如果沒有任何 HTTP URLs，則伺服器 URL 將會是空的。
-   不支援 **Examples**，但是支援 **example**。
-   **Multipart/form-data** 不受支援。

> [!IMPORTANT]
> 請參閱此[文件](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/)中 OpenAPI 匯入相關的重要資訊和祕訣。

## <a name="wsdl"> </a>WSDL

WSDL 檔案是用來建立 SOAP 傳遞和 SOAP 對 REST Api。

-   **SOAP 繫結** - 僅支援「文件」和「常值」編碼的 SOAP 繫結樣式。 不支援「rpc」樣式或 SOAP 編碼。
-   **WSDL:Import** - 不支援此屬性。 客戶應該將匯入的項目合併成一份文件。
-   **具有多個部分的訊息** - 不支援這些類型的訊息。
-   **WCF wsHttpBinding** - 使用 Windows Communication Foundation 建立的 SOAP 服務應該使用 basicHttpBinding - 不支援 wsHttpBinding。
-   **MTOM** - 使用 MTOM 的服務「可能」可以運作。 目前不提供官方支援。
-   **遞迴** - APIM 不支援遞迴定義的類型 (例如，參考自己的陣列)。
-   **多個命名空間** - 在結構描述中可使用多個命名空間，但只有目標命名空間可用來定義訊息部分。 命名空間若不是用來定義其他輸入或輸出元素的目標，則不會保留。 雖然這類 WSDL 文件可以匯入，但在匯出慈時，所有訊息部分都會有 WSDL 的目標命名空間。
-   **陣列**-SOAP 對 REST 轉換僅支援下列範例中所示的包裝陣列:

```xml
    <complexType name="arrayTypeName">
        <sequence>
            <element name="arrayElementValue" type="arrayElementType" minOccurs="0" maxOccurs="unbounded"/>
        </sequence>
    </complexType>
    <complexType name="typeName">
        <sequence>
            <element name="element1" type="someTypeName" minOccurs="1" maxOccurs="1"/>
            <element name="element2" type="someOtherTypeName" minOccurs="0" maxOccurs="1" nillable="true"/>
            <element name="arrayElement" type="arrayTypeName" minOccurs="1" maxOccurs="1"/>
        </sequence>
    </complexType>
```

## <a name="wadl"> </a>WADL

目前沒有任何已知的 WADL 匯入問題。
