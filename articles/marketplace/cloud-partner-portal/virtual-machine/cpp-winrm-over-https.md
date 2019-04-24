---
title: 透過 Azure 的 HTTPS 進行 Windows 遠端管理 | Microsoft Docs
description: 上也提供本文中使用的原始碼。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: pbutlerm
ms.openlocfilehash: 99c41dc010ae589cc43c093646fd1c05c1333f7e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60342809"
---
# <a name="windows-remote-management-over-https"></a>透過 HTTPS 進行 Windows 遠端管理

本節說明如何設定 Azure 託管的 Windows 型 VM，以便使用 PowerShell 從遠端管理和部署。  若要啟用 PowerShell 遠端處理，目標 VM 必須公開 Windows 遠端管理 (WinRM) 的 HTTPS 端點。  如需有關 PowerShell 遠端處理的詳細資訊，請參閱[執行遠端命令](https://docs.microsoft.com/powershell/scripting/core-powershell/running-remote-commands?view=powershell-6)。  如需有關 WinRM 的詳細資訊，請參閱 [Windows 遠端管理](https://docs.microsoft.com/windows/desktop/WinRM/portal)。

如果您使用其中一種「傳統的」Azure 方法 (Azure Service Manager 入口網站或已淘汰的 [Azure 服務管理 API](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100))) 建立 VM，則會自動使用 WinRM 端點設定。  不過，如果您使用下列其中一種「新型的」Azure 方法建立 VM，則「不會」透過 HTTPS 為 WinRM 設定您的 VM。  

- 使用 [Azure 入口網站](https://portal.azure.com/) (通常是來自已核准的基礎)，如[建立與 Azure 相容的 VHD](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd) 一節中所述
- [使用 Azure Resource Manager 範本](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)
- 使用 Azure PowerShell 或 Azure CLI 命令殼層。  如需範例，請參閱[快速入門：使用 PowerShell 在 Azure 中建立 Windows 虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell)和[快速入門：使用 Azure CLI 建立 Linux 虛擬機器](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli)。

執行認證工具套件將 VM 上架也需要此 WinRM 端點，如[認證您的 VM 映像](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm)中所述。

相反地，通常 Linux VM 是使用 [Azure CLI](https://docs.microsoft.com/cli/azure) 從遠端管理，或從 SSH 主控台的 Linux 命令管理。  Azure 也提供數種替代方法，[在 Linux VM 中執行指令碼](https://docs.microsoft.com/azure/virtual-machines/linux/run-scripts-in-vm)。  若是更複雜的情況，則有許多自動化和整合解決方案可用於 Windows 或 Linux 型的 VM。


## <a name="configure-and-deploy-with-winrm"></a>使用 WinRM 設定及部署

Windows 型 VM 的 WinRM 端點可以在其開發的兩個不同階段期間設定：

- 建立期間 - 在將 VM 部署到現有的 VHD 期間。  這是對新供應項目慣用的方法。  此方法需要建立 Azure 憑證、使用提供的 Azure Resource Manager 範本，以及執行自訂的 PowerShell 指令碼。 
- 部署之後 - 在 Azure 上託管的現有 VM 上。  如果您已經在 Azure 上部署 VM 解決方案，而且為其需要啟用 Windows 遠端管理，請使用此方法。  此方法需要在 Azure 入口網站中手動變更，並在目標 VM 上執行指令碼。 


## <a name="next-steps"></a>後續步驟
如果您要建立新的 VM，您可以在[從其 VHD 部署 VM](./cpp-deploy-vm-vhd.md) 期間啟用 WinRM。  否則，可以在現有的 VM 中啟用 WinRM  
