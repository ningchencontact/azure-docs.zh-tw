---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/25/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b878d54f0f52768459dbfc810e47d294b9c8d996
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50097761"
---
本文使用 PowerShell Cmdlet。 若要執行 Cmdlet，您可以使用 Azure Cloud Shell (免費的互動式殼層)。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 只要按一下 [複製] 即可複製程式碼，將它貼到 Cloud Shell 中，然後按 enter 鍵加以執行。 以下有幾種啟動 Cloud Shell 的方式：

|  |   |
|-----------------------------------------------|---|
| 按一下程式碼區塊右上角的 [試試看]。 | ![本文中的 Cloud Shell](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-powershell-try-it.png) |
| 在您的瀏覽器中開啟 Cloud Shell。 | [![https://shell.azure.com/powershell](./media/vpn-gateway-cloud-shell-powershell/launchcloudshell.png)](https://shell.azure.com/powershell) |
| 按一下 Azure 入口網站右上方功能表上的 [Cloud Shell] 按鈕。 | [![入口網站中的 Cloud Shell](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-menu.png)](https://portal.azure.com) |
|  |  |

如果您不想使用 Azure Cloud Shell，您可改為在本機安裝 PowerShell。 如果您選擇在本機安裝及使用 PowerShell，請務必安裝最新版的 Azure Resource Manager PowerShell Cmdlet。 PowerShell Cmdlet 會經常更新，您通常需要更新您的 PowerShell Cmdlet 才能取得最新的功能。 如果您未更新 PowerShell Cmdlet，指定的值可能會失敗。 若要尋找您在本機執行的 PowerShell 版本，請使用 'Get-Module -ListAvailable AzureRM' Cmdlet。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。 如需詳細資訊，請參閱 [如何安裝及設定 Azure PowerShell](/powershell/azure/overview)。