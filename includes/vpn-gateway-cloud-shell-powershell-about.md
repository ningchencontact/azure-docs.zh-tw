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
ms.openlocfilehash: f6af5e5e42a16548b1997845ea5076ddd4dd3be5
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735946"
---
本文使用 PowerShell Cmdlet。 若要執行 cmdlet，您可以使用 Azure Cloud Shell 中，裝載於 Azure，並透過瀏覽器的互動式殼層環境。 Azure Cloud Shell 會隨附預先安裝的 Azure PowerShell cmdlet。

若要執行 Azure Cloud Shell 在本文中所包含的任何程式碼，開啟 Cloud Shell 工作階段，請使用**複製**按鈕以複製程式碼，並將它貼到 Cloud Shell 工作階段，使用程式碼區塊__Ctrl + Shift + V__上Windows 和 Linux，或是__Cmd + Shift + V__在 macOS 上。 貼上的文字不會自動執行，因此請按下**Enter**執行程式碼。

您可以啟動 Azure Cloud Shell 中使用：

|  |   |
|-----------------------------------------------|---|
| 選取程式碼區塊右上角的 [試試看]  。 這__不__自動將文字複製到 Cloud Shell。 | ![Azure Cloud Shell 的試試看範例](./media/cloud-shell-try-it/cli-try-it.png) |
| 開啟[shell.azure.com](https://shell.azure.com)瀏覽器中。 | [![啟動 Azure Cloud Shell 按鈕](./media/cloud-shell-try-it/launchcloudshell.png)](https://shell.azure.com) |
| 選取 [Azure 入口網站](https://portal.azure.com)右上角功能表上的 [Cloud Shell]  按鈕。 | ![Azure 入口網站中的 [Cloud Shell] 按鈕](./media/cloud-shell-try-it/cloud-shell-menu.png) |

**在本機執行 PowerShell**

您也可以安裝，並在本機執行 Azure PowerShell cmdlet，在您的電腦上。 PowerShell cmdlet 會經常更新。 如果您不會執行最新版本，指示中所指定的值可能會失敗。 若要尋找您在本機執行的 PowerShell 版本，請使用`Get-Module -ListAvailable Az`cmdlet。 若要安裝或更新，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。
