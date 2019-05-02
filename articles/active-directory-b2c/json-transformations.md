---
title: 適用於 Azure Active Directory B2C 識別體驗架構結構描述的 JSON 宣告轉換範例 | Microsoft Docs
description: 適用於 Azure Active Directory B2C 識別體驗架構結構描述的 JSON 宣告轉換範例。
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 7574327f8acbd2215080e43a57b0b9c7cdd8b423
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64710245"
---
# <a name="json-claims-transformations"></a>JSON 宣告轉換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

此文章提供在 Azure Active Directory (Azure AD) B2C 中，使用識別體驗架構結構描述 JSON 宣告轉換的範例。 如需詳細資訊，請參閱 [ClaimsTransformations](claimstransformations.md)。

## <a name="getclaimfromjson"></a>GetClaimFromJson

從 JSON 資料中取得指定的元素。

| Item | TransformationClaimType | 数据类型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | 宣告轉換用來取得項目的 ClaimType。 |
| InputParameter | claimToExtract | string | 要擷取的 JSON 元素名稱。 |
| OutputClaim | extractedClaim | string | 叫用此宣告轉換之後所產生的 ClaimType，元素值指定於 _claimToExtract_ 輸入參數中。 |

在下列範例中，宣告轉換會從 JSON 資料擷取 `emailAddress` 元素：`{"emailAddress": "someone@example.com", "displayName": "Someone"}`

```XML
<ClaimsTransformation Id="GetEmailClaimFromJson" TransformationMethod="GetClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="customUserData" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="emailAddress" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="extractedEmail" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
  - **inputJson**：{"emailAddress": "someone@example.com", "displayName":"Someone"}
- 輸入參數：
    - **claimToExtract**：emailAddress
- 輸出宣告： 
  - **extractedClaim**：someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

從 JSON 資料中取得指定元素的清單。

| Item | TransformationClaimType | 数据类型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | jsonSourceClaim | string | 宣告轉換用來取得宣告的 ClaimType。 |
| InputParameter | errorOnMissingClaims | boolean | 指定如果遺漏其中一個宣告，是否要擲回錯誤。 |
| InputParameter | includeEmptyClaims | string | 指定是否要包含空的宣告。 |
| InputParameter | jsonSourceKeyName | string | 元素索引鍵名稱 |
| InputParameter | jsonSourceValueName | string | 元素值名稱 |
| OutputClaim | 集合 | string、int、boolean 及 datetime |要擷取的宣告清單。 宣告的名稱應該等於 _jsonSourceClaim_ 輸入宣告中所指定的宣告名稱。 |

在下列範例中，宣告轉換會從 JSON 資料中擷取下列宣告：email (string)、displayName (string)、membershipNum (int)、active (boolean) 及 birthdate (datetime)。

```JSON
[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
```

```XML
<ClaimsTransformation Id="GetClaimsFromJson" TransformationMethod="GetClaimsFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="jsonSourceClaim" TransformationClaimType="jsonSource" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="errorOnMissingClaims" DataType="boolean" Value="false" />
    <InputParameter Id="includeEmptyClaims" DataType="boolean" Value="false" />
    <InputParameter Id="jsonSourceKeyName" DataType="string" Value="key" />
    <InputParameter Id="jsonSourceValueName" DataType="string" Value="value" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="membershipNum" />
    <OutputClaim ClaimTypeReferenceId="active" />
    <OutputClaim ClaimTypeReferenceId="birthdate" />
  </OutputClaims>
</ClaimsTransformation>
```    

- 輸入宣告：
  - **jsonSourceClaim**：[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value": true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
- 輸入參數：
    - **errorOnMissingClaims**：false
    - **includeEmptyClaims**：false
    - **jsonSourceKeyName**：key
    - **jsonSourceValueName**：value
- 輸出宣告：
  - **email**："someone@example.com"
  - **displayName**："Someone"
  - **membershipNum**：6353399
  - **active**：true
  - **birthdate**：1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

從 JSON 資料中取得指定的數值 (long) 元素。

| Item | TransformationClaimType | 数据类型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | 宣告轉換用來取得宣告的 ClaimType。 |
| InputParameter | claimToExtract | string | 要擷取的 JSON 元素名稱。 |
| OutputClaim | extractedClaim | long | 叫用此 ClaimsTransformation 之後所產生的 ClaimType，元素的值指定於 _claimToExtract_ 輸入參數中。 |

在下列範例中，宣告轉換會從 JSON 資料中擷取 `id` 元素。

```JSON
{
    "emailAddress": "someone@example.com", 
    "displayName": "Someone", 
    "id" : 6353399
}
```

```XML
<ClaimsTransformation Id="GetIdFromResponse" TransformationMethod="GetNumericClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="exampleInputClaim" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="id" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="membershipId" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
  - **inputJson**：{"emailAddress": "someone@example.com", "displayName":"Someone", "id" :6353399}
- 輸入參數
    - **claimToExtract**：id
- 輸出宣告： 
    - **extractedClaim**：6353399

## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

從 JSON 資料陣列中取得第一個元素。

| Item | TransformationClaimType | 数据类型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJsonClaim | string | 宣告轉換用來從 JSON 陣列中取得項目的 ClaimType。 |
| OutputClaim | extractedClaim | string | 叫用此 ClaimsTransformation 之後所產生的 ClaimType，JSON 陣列中的第一個元素。 |

在下列範例中，宣告轉換會從 JSON 陣列 `["someone@example.com", "Someone", 6353399]` 中擷取第一個元素 (電子郵件地址)。

```XML
<ClaimsTransformation Id="GetEmailFromJson" TransformationMethod="GetSingleValueFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userData" TransformationClaimType="inputJsonClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
  - **inputJsonClaim**["someone@example.com", "Someone", 6353399]
- 輸出宣告： 
  - **extractedClaim**：someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

將 XML 資料轉換為 JSON 格式。

| Item | TransformationClaimType | 数据类型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Xml | string | 宣告轉換用來將資料從 XML 轉換為 JSON 格式的 ClaimType。 |
| OutputClaim | json | string | 叫用此 ClaimsTransformation 之後所產生的 ClaimType，JSON 格式的資料。 |

```XML
<ClaimsTransformation Id="ConvertXmlToJson" TransformationMethod="XmlStringToJsonString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="intpuXML" TransformationClaimType="xml" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="outputJson" TransformationClaimType="json" />
  </OutputClaims>
</ClaimsTransformation>
```

在下列範例中，宣告轉換會將下列 XML 資料轉換為 JSON 格式。

#### <a name="example"></a>範例
輸入宣告：

```XML
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

輸出宣告：

```JSON
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```

