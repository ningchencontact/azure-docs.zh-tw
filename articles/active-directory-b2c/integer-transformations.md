---
title: 適用於 Azure Active Directory B2C 識別體驗架構結構描述的整數宣告轉換範例 | Microsoft Docs
description: 適用於 Azure Active Directory B2C 識別體驗架構結構描述的整數宣告轉換範例。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
origin.date: 09/10/2018
ms.date: 04/04/2019
ms.author: v-junlch
ms.subservice: B2C
ms.openlocfilehash: 358ee07b8fd32edded084d406e490cae9f557fdd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60397136"
---
# <a name="integer-claims-transformations"></a>整數宣告轉換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文會提供數個使用範例，介紹 Azure Active Directory (Azure AD) B2C 中識別體驗架構結構描述的整數宣告轉換。 如需詳細資訊，請參閱 [ClaimsTransformations](claimstransformations.md)。

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim 

將 Lng 資料類型轉換為字串資料類型。

| Item | TransformationClaimType | 数据类型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | long | 要轉換為字串的 ClaimType。 |
| OutputClaim | outputClaim | string | 叫用此 ClaimsTransformation 之後所產生的 ClaimType。 |

在此範例中，值類型為 Long 的 `numericUserId` 宣告會轉換為值類型為 String (字串) 的 `UserId` 宣告。

```XML
<ClaimsTransformation Id="CreateUserId" TransformationMethod="ConvertNumberToStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="UserId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
    - **inputClaim**：12334 (長整數)
- 輸出宣告： 
    - **outputClaim**："12334" (字串)


