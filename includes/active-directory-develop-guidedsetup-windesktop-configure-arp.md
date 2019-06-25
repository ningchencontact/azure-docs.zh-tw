---
title: 包含檔案
description: 包含檔案
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 5c2bce5635dfe488c1725efdd2954ecd81cf8e79
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205857"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>將應用程式的註冊資訊新增到您的應用程式

在這個步驟中，您需要將應用程式識別碼新增到您的專案。

1. 開啟 `App.xaml.cs` 並將包含 `ClientId` 的那一行取代為：

```csharp
private static string ClientId = "[Enter the application Id here]";
```
