---
title: 適用於 Azure Active Directory B2C 識別體驗架構結構描述的一般宣告轉換範例 | Microsoft Docs
description: 適用於 Azure Active Directory B2C 識別體驗架構結構描述的一般宣告轉換範例。
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 831ecf2924ab6c321f69bb50c4a95a4156482585
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683962"
---
# <a name="general-claims-transformations"></a>一般宣告轉換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

此文章提供在 Azure Active Directory (Azure AD) B2C 中，使用識別體驗架構結構描述一般宣告轉換的範例。 如需詳細資訊，請參閱 [ClaimsTransformations](claimstransformations.md)。

## <a name="doesclaimexist"></a>DoesClaimExist

檢查 **inputClaim** 是否存在，並據以將 **outputClaim** 設定為 True 或 False。

| Item | TransformationClaimType | 数据类型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |任意 | 必須驗證其存在的輸入宣告。 |
| OutputClaim | outputClaim | boolean | 叫用此 ClaimsTransformation 之後所產生的 ClaimType。 |

使用此宣告轉換來檢查某個宣告是否存在或包含任何值。 傳回值是布林值，會指出宣告是否存在。 下列範例會檢查電子郵件地址是否存在。

```XML
<ClaimsTransformation Id="CheckIfEmailPresent" TransformationMethod="DoesClaimExist">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim" />
  </InputClaims>                    
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isEmailPresent" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
  - **inputClaim**：someone@contoso.com
- 輸出宣告： 
    - **outputClaim**：true

## <a name="hash"></a>雜湊

使用 salt 和祕密，針對提供的純文字進行雜湊處理。

| Item | TransformationClaimType | 数据类型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | plaintext | string | 要加密的輸入宣告 |
| InputClaim | salt | string | Salt 參數。 您可以使用 `CreateRandomString` 宣告轉換來建立隨機值。 |
| InputParameter | randomizerSecret | string | 指向現有的 Azure AD B2C **原則金鑰**。 建立新金鑰：在您的 Azure AD B2C 租用戶中，選取 [B2C 設定] > [識別體驗架構]。 若要檢視租用戶中可用的金鑰，請選取 [原則金鑰]。 選取 [新增] 。 針對 [選項]，選取 [手動]。 提供名稱 (可能會自動新增前置詞 B2C_1A_)。 在 [祕密] 方塊中，輸入任何您想要使用的祕密，例如 1234567890。 針對 [金鑰使用方法] 選取 [祕密]。 選取 [建立] 。 |
| OutputClaim | 雜湊 | string | 叫用此宣告轉換之後所產生的 ClaimType。 設定於 `plaintext` inputClaim 中的宣告。 |

```XML
<ClaimsTransformation Id="HashPasswordWithEmail" TransformationMethod="Hash">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="password" TransformationClaimType="plaintext" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="salt" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="randomizerSecret" DataType="string" Value="B2C_1A_AccountTransformSecret" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hashedPassword" TransformationClaimType="hash" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
    - **plaintext**：MyPass@word1
    - **salt**：487624568
    - **randomizerSecret**：B2C_1A_AccountTransformSecret
- 輸出宣告： 
    - **outputClaim**：CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=



