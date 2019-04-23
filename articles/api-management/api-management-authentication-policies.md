---
title: Azure API 管理驗證原則 | Microsoft Docs
description: 了解「Azure API 管理」中可用的驗證原則。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 9ee4a9fb5c63061eed32389b5672652aad01208a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2019
ms.locfileid: "59994929"
---
# <a name="api-management-authentication-policies"></a>API 管理驗證原則
本主題提供下列「API 管理」原則的參考。 如需有關新增和設定原則的資訊，請參閱 [API 管理中的原則](https://go.microsoft.com/fwlink/?LinkID=398186)。  

##  <a name="AuthenticationPolicies"></a> 驗證原則  
  
-   [使用基本方法进行身份验证](api-management-authentication-policies.md#Basic) - 使用基本身份验证方法向后端服务进行身份验证。  
  
-   [使用用戶端憑證進行驗證](api-management-authentication-policies.md#ClientCertificate) - 使用用戶端憑證來驗證後端服務。  

-   [使用受控身分識別進行驗證](api-management-authentication-policies.md#ManagedIdentity)-向[受控身分識別](../active-directory/managed-identities-azure-resources/overview.md)API 管理服務。  
  
##  <a name="Basic"></a> 使用基本驗證進行驗證  
 使用 `authentication-basic` 原則以利用「基本」驗證向後端服務進行驗證。 此原則會將「HTTP 授權」標頭有效地設定為與此原則中所提供認證對應的值。  
  
### <a name="policy-statement"></a>原則陳述式  
  
```xml  
<authentication-basic username="username" password="password" />  
```  
  
### <a name="example"></a>範例  
  
```xml  
<authentication-basic username="testuser" password="testpassword" />  
```  
  
### <a name="elements"></a>元素  
  
|Name|描述|必要項|  
|----------|-----------------|--------------|  
|authentication-basic|根元素。|是|  
  
### <a name="attributes"></a>屬性  
  
|Name|描述|必要項|預設值|  
|----------|-----------------|--------------|-------------|  
|username|指定「基本驗證」認證的使用者名稱。|是|N/A|  
|password|指定「基本驗證」認證的密碼。|是|N/A|  
  
### <a name="usage"></a>使用量  
 此原則可用於下列原則[區段](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[範圍](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)。  
  
-   **原則區段︰** inbound  
  
-   **原則範圍︰** API  
  
##  <a name="ClientCertificate"></a> 使用用戶端憑證進行驗證  
 使用 `authentication-certificate` 原則以利用用戶端憑證向後端服務進行驗證。 憑證必須先[安裝到 API 管理中](https://go.microsoft.com/fwlink/?LinkID=511599)且會以其指紋作為識別。  
  
### <a name="policy-statement"></a>原則陳述式  
  
```xml  
<authentication-certificate thumbprint="thumbprint" />  
```  
  
### <a name="example"></a>範例  
  
```xml  
<authentication-certificate thumbprint="....." />  
```  
  
### <a name="elements"></a>元素  
  
|Name|描述|必要項|  
|----------|-----------------|--------------|  
|authentication-certificate|根元素。|是|  
  
### <a name="attributes"></a>屬性  
  
|Name|描述|必要項|預設值|  
|----------|-----------------|--------------|-------------|  
|thumbprint|用戶端憑證的指紋。|是|N/A|  
  
### <a name="usage"></a>使用量  
 此原則可用於下列原則[區段](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[範圍](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)。  
  
-   **原則區段︰** inbound  
  
-   **原則範圍︰** API  

##  <a name="ManagedIdentity"></a> 使用受控身分識別進行驗證  
 使用`authentication-managed-identity`向後端服務，使用 API 管理服務的受管理的身分識別的原則。 此原則有效地使用受管理的身分識別，來從 Azure Active Directory 取得存取權杖來存取指定的資源。 
  
### <a name="policy-statement"></a>原則陳述式  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>範例  
  
```xml  
<authentication-managed-identity resource="https://graph.windows.net" output-token-variable-name="test-access-token" ignore-error="true" /> 
```  
  
### <a name="elements"></a>元素  
  
|Name|描述|必要項|  
|----------|-----------------|--------------|  
|authentication-managed-identity |根元素。|是|  
  
### <a name="attributes"></a>屬性  
  
|Name|描述|必要項|預設值|  
|----------|-----------------|--------------|-------------|  
|resource|字串。 目標中的 web API （受保護的資源） Azure Active Directory 應用程式識別碼 URI。|是|N/A|  
|output-token-variable-name|字串。 將會收到權杖的值，做為物件類型的內容變數名稱`string`。|否|N/A|  
|ignore-error|布林值。 如果設定為`true`，原則管線將會繼續執行，即使未取得存取權杖。|否|false|  
  
### <a name="usage"></a>使用量  
 此原則可用於下列原則[區段](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[範圍](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)。  
  
-   **原則區段︰** inbound  
  
-   **原則範圍︰** 全域、產品、API、作業  

## <a name="next-steps"></a>後續步驟
如需使用原則的詳細資訊，請參閱︰

+ [API 管理中的原則](api-management-howto-policies.md)
+ [轉換 API](transform-api.md)
+ [原則參考文件](api-management-policy-reference.md)，取得原則陳述式及其設定的完整清單
+ [原則範例](policy-samples.md)   
