---
title: 使用適用于 JAVA 的 MSAL （MSAL4j）從權杖快取取得和移除帳戶
titleSuffix: Microsoft identity platform
description: 瞭解如何使用適用于 JAVA 的 Microsoft 驗證程式庫，從權杖快取中查看和移除帳戶。
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
manager: henrikm
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 343abd87d3b5e8b82989b8b370cef61ec6d051df
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905507"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java-msal4j"></a>使用適用于 JAVA 的 MSAL （MSAL4j）從權杖快取取得和移除帳戶

MSAL4J 預設會提供記憶體中的權杖快取。 記憶體中的權杖快取會持續在應用程式實例的持續時間內。

## <a name="see-which-accounts-are-in-the-cache"></a>查看快取中的帳戶

您可以藉由呼叫 `PublicClientApplication.getAccounts()` 來檢查快取中的帳戶，如下列範例所示：

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>從快取中移除帳戶

若要從快取中移除帳戶，請尋找需要移除的帳戶，然後呼叫 `PublicClientApplicatoin.removeAccount()`，如下列範例所示：

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>詳細資訊

如果您使用 MSAL for JAVA，請瞭解[MSAL For java 中的自訂權杖](msal-java-token-cache-serialization.md)快取序列化。
