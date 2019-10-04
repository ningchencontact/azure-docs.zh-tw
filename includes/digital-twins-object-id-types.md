---
title: 包含檔案
description: 包含檔案
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: 1f4cdf45fb643499f7d6ab717c6f36395ad292c8
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827720"
---
`objectIdType` (或 **物件識別碼類型**) 係指給予角色的身分識別類型。 除了 `DeviceId` 和 `UserDefinedFunctionId` 類型之外，物件識別碼類型還會對應至 Azure Active Directory 物件的屬性。

下表包含 Azure Digital Twins 中支援的物件識別碼類型：

| Type | 描述 |
| --- | --- |
| UserId | 會為使用者指派角色。 |
| DeviceId | 會為裝置指派角色。 |
| DomainName | 會為網域名稱指派角色。 每個具有指定網域名稱的使用者，都會有對應角色的存取權限。 |
| TenantId | 會為租用戶指派角色。 每個屬於指定 Azure AD 租用戶識別碼的使用者，都會有對應角色的存取權限。 |
| ServicePrincipalId | 會為服務主體物件識別碼指派角色。 |
| UserDefinedFunctionId | 會為使用者定義函式 (UDF) 指派角色。 |