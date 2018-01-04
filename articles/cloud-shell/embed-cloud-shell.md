---
title: "內嵌 Azure 雲端殼層 |Microsoft 文件"
description: "了解如何內嵌 Azure 雲端殼層。"
services: cloud-shell
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: juluk
ms.openlocfilehash: 78b539136971aa282e5447d7882ecb02f73f346b
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2017
---
# <a name="embed-azure-cloud-shell"></a>內嵌 Azure 雲端殼層

內嵌雲端介面可讓開發人員和內容的寫入器直接從專用的 URL，開啟雲端殼層[shell.azure.com](https://shell.azure.com)。這會立即帶的完整功能的雲端殼層驗證工具，以及最新的 Azure CLI/Azure PowerShell 工具給您的使用者。

[![](https://shell.azure.com/images/launchcloudshell.png "啟動 Azure 雲端命令介面")](https://shell.azure.com)

## <a name="how-to"></a>作法

複製下列整合 markdown 檔中雲端的殼層啟動按鈕：

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

將快顯的雲端殼層內嵌 HTML 如下：
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><image src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>自訂體驗

設定特定殼層經驗加強您的 URL。
|體驗   |URL   |
|---|---|
|最近使用的介面   |shell.azure.com           |
|Bash                       |shell.azure.com/bash       |
|PowerShell                 |shell.azure.com/powershell |

## <a name="next-steps"></a>後續步驟
[Cloud Shell 中 Bash 的快速入門](quickstart.md)<br>
[雲端殼層快速入門中的 PowerShell](quickstart-powershell.md)
