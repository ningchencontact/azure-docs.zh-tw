---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/13/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c8f45e4bb16c05c9f322dd04d2c80f6144744e64
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/23/2019
ms.locfileid: "66147005"
---
本文使用 PowerShell Cmdlet。 若要執行 cmdlet，您可以使用 Azure Cloud Shell。 Azure Cloud Shell 是免費的互動式 shell 具有預先安裝及設定您的帳戶搭配使用共用 Azure 工具。 只要按一下**複製**複製程式碼，將它貼到 Cloud Shell 中，並再按 enter 鍵加以執行。 以下有幾種啟動 Cloud Shell 的方式：

|  |   |
|-----------------------------------------------|---|
| 按一下程式碼區塊右上角的 [試試看]。 | ![本文中的 Cloud Shell](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-powershell-try-it.png) |
| 在您的瀏覽器中開啟 Cloud Shell。 | [![https://shell.azure.com/powershell](./media/vpn-gateway-cloud-shell-powershell/launchcloudshell.png)](https://shell.azure.com/powershell) |
| 按一下 Azure 入口網站右上方功能表上的 [Cloud Shell] 按鈕。 | [![入口網站中的 Cloud Shell](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-menu.png)](https://portal.azure.com) |
|  |  |

**在本機執行 PowerShell**

您也可以安裝，並在本機執行 Azure PowerShell cmdlet，在您的電腦上。 PowerShell cmdlet 會經常更新。 如果您不會執行最新版本，指示中所指定的值可能會失敗。 若要尋找您在本機執行的 PowerShell 版本，請使用`Get-Module -ListAvailable Az`cmdlet。 若要安裝或更新，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。