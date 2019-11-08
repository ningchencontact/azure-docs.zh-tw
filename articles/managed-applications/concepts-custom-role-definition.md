---
title: Azure 受控應用程式中的自訂角色定義總覽 |Microsoft Docs
description: 說明為受控應用程式建立自訂角色定義的概念。
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: 7f4371bea467d6d4c99a776e03cdf13070d77ac6
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818386"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Azure 受控應用程式中的自訂角色定義成品

自訂角色定義是受控應用程式中的選擇性成品。 它可用來判斷受控應用程式執行其功能所需的許可權。

本文提供自訂角色定義成品及其功能的總覽。

## <a name="custom-role-definition-artifact"></a>自訂角色定義成品

您必須將自訂角色定義構件命名為 customRoleDefinition。 將它放在建立受控應用程式定義的 .zip 封裝中與 createUiDefinition 相同的層級和 mainTemplate。 若要瞭解如何建立 .zip 套件併發布受控應用程式定義，請參閱[發佈受控應用程式定義。](publish-managed-app-definition-quickstart.md)

## <a name="custom-role-definition-schema"></a>自訂角色定義架構

CustomRoleDefinition 檔案具有最上層的 `roles` 屬性，這是一組角色。 這些角色是受控應用程式必須運作的許可權。 目前只允許內建角色，但您可以指定多個角色。 角色定義的識別碼或角色名稱可以參考角色。

自訂角色定義的範例 JSON：

```json
{
    "contentVersion": "0.0.0.1",
    "roles": [
        {
            "properties": {
                "roleName": "Contributor"
            }
        },
        {
            "id": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
        },
        {
            "id": "/providers/Microsoft.Authorization/roledefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
        }
    ]
}
```

## <a name="roles"></a>角色

角色是由 `$.properties.roleName` 或 `id`所組成：

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!NOTE]
> 您可以使用 [`id`] 或 [`roleName`] 欄位。 只需要一個。 這些欄位是用來查閱應套用的角色定義。 如果同時提供這兩者，則會使用 [`id`] 欄位。

|屬性|必要？|說明|
|---------|---------|---------|
|id|是|內建角色的識別碼。 您可以使用完整識別碼，或只是 GUID。|
|RoleName|是|內建角色的名稱。|