---
title: Azure 保留資源名稱的錯誤 | Microsoft Docs
description: 說明當提供的資源名稱包含保留字時應如何解決錯誤。
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: 922389b7c6c1bb7ad1d9b8f6ec35ccc1c5656723
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "64683941"
---
# <a name="resolve-reserved-resource-name-errors"></a>解決保留資源名稱的錯誤

本文說明當您部署的資源名稱包含保留字時會發生的錯誤。

## <a name="symptom"></a>徵狀

當您部署的資源可透過公用端點取得時，可能會遇到下列錯誤：

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>原因

如果資源具有公用端點，其名稱就不能使用保留字或商標。

下列為保留字：

* ACCESS
* AZURE
* BING
* BIZSPARK
* BIZTALK
* CORTANA
* DIRECTX
* DOTNET
* DYNAMICS
* EXCEL
* EXCHANGE
* FOREFRONT
* GROOVE
* HOLOLENS
* HYPERV
* KINECT
* LYNC
* MSDN
* O365
* OFFICE
* OFFICE365
* ONEDRIVE
* ONENOTE
* OUTLOOK
* POWERPOINT
* SHAREPOINT
* SKYPE
* VISIO
* VISUALSTUDIO

下列為不得在名稱中作為全字拼寫或子字串的字：

* LOGIN
* MICROSOFT
* WINDOWS
* XBOX

## <a name="solution"></a>解決方法

提供名稱時，不要使用任何一個保留字。