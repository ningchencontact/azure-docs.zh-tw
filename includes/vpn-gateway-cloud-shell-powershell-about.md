---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 01358d13d30358a9950cbe35622df065fc5a6de5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133187"
---
本文使用 PowerShell Cmdlet。 若要執行 Cmdlet，您可以透過瀏覽器使用裝載於 Azure 中的互動式殼層環境 Azure Cloud Shell。 Azure Cloud Shell 隨附於預先安裝的 Azure PowerShell Cmdlet。

若要在 Azure Cloud Shell 上執行本文所包含的任何程式碼，請開啟 Cloud Shell 工作階段、使用某個程式碼區塊上的 [複製]  按鈕來複製程式碼，然後使用 __Ctrl+Shift+V__ (在 Windows 和 Linux 上) 或 __Cmd+Shift+V__ (在 macOS 上) 將程式碼貼到 Cloud Shell 工作階段中。 貼上的文字不會自動執行，因此請按 **Enter** 來執行程式碼。

您可以使用下列方式來啟動 Azure Cloud Shell：

|  |   |
|-----------------------------------------------|---|
| 選取程式碼區塊右上角的 [試試看]  。 這__不會__自動將文字複製到 Cloud Shell。 | ![Azure Cloud Shell 的試試看範例](./media/cloud-shell-try-it/cli-try-it.png) |
| 在瀏覽器中開啟 [shell.azure.com](https://shell.azure.com)。 | [![啟動 Azure Cloud Shell 按鈕](./media/cloud-shell-try-it/launchcloudshell.png)](https://shell.azure.com) |
| 選取 [Azure 入口網站](https://portal.azure.com)右上角功能表上的 [Cloud Shell]  按鈕。 | ![Azure 入口網站中的 [Cloud Shell] 按鈕](./media/cloud-shell-try-it/cloud-shell-menu.png) |

**在本機執行 PowerShell**

您也可以安裝，並在本機執行 Azure PowerShell cmdlet，在您的電腦上。 PowerShell cmdlet 會經常更新。 如果您不會執行最新版本，指示中所指定的值可能會失敗。 若要尋找的電腦上安裝 Azure PowerShell 版本，請使用`Get-Module -ListAvailable Az`cmdlet。 若要安裝或更新，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。
