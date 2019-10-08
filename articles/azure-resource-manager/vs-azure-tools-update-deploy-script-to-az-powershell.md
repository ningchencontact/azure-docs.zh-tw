---
title: 更新 Visual Studio 的範本部署指令碼以使用 Az Powershell
description: 將 Visual Studio 範本部署指令碼從 AzureRM 更新為 Az Powershell
author: cweining
ms.service: azure-resource-manager
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: cweining
ms.openlocfilehash: 483b20f0003994f88ec35cc9f31a77cf571804f3
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695682"
---
# <a name="updating-the-visual-studio-template-deployment-script-to-use-az-powershell"></a>更新 Visual Studio 範本部署指令碼以使用 Az Powershell

Visual Studio 16.4 支援在範本部署指令碼中使用 Az Powershell。 Visual Studio 不會安裝 Az Powershell 或自動更新資源群組專案中的部署指令碼。 若要使用較新的 Az Powershell，您必須執行下列四項作業：
1. 將 AzureRM Powershell 解除安裝
1. 安裝 Az Powershell
1. 更新至 Visual Studio 16.4
1. 更新您專案中的部署指令碼。

## <a name="uninstall-azurerm-powershell"></a>將 AzureRM Powershell 解除安裝
請依照這些[指示](https://docs.microsoft.com/powershell/azure/uninstall-az-ps?view=azps-2.7.0#uninstall-the-azurerm-module)將 AzureRM Powershell 解除安裝。

## <a name="install-az-powershell"></a>安裝 Az Powershell
請依照這些[指示](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.7.0)來安裝 Az Powershell。

## <a name="update-visual-studio-to-164"></a>將 Visual Studio 更新至 16.4
更新至 Visual Studio 16.4 (在其可供更新時)。 在升級期間，請確定 Azure Powershell 元件並未勾選。 由於您已透過資源庫安裝 Az Powershell，因此不應使用安裝 Visual Studio 的 AzureRM 版 Powershell。

如果您已升級至16.4，並勾選了 Azure Powershell 元件，您可以執行 Visual Studio 安裝程式並取消勾選 Azure 工作負載中的 Azure Powershell 元件，以解除安裝該元件，或在個別元件頁面上解除安裝。

## <a name="update-the-deployment-script-in-your-project"></a>更新您專案中的部署指令碼
將所有出現的 'AzureRm’ 字串取代為您部署指令碼中的 'Az'。 您可以參考此 [gist](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) 中的修訂以查看變更。 如需將指令碼升級至 Az Powershell 的詳細資訊，請參閱此[文件](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-2.5.0)。


