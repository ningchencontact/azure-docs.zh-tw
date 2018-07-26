---
title: 適用於 Windows 使用者的 Azure Cloud Shell | Microsoft Docs
description: 引導不熟悉 Linux 系統之使用者的指南
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/03/2018
ms.author: damaerte
ms.openlocfilehash: 5e318a0f64033aa0c4b306e547c11e1994afa229
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2018
ms.locfileid: "37861087"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>Azure Cloud Shell 中的 PowerShell (適用於 Windows 使用者)

在 2018 年 5 月，Microsoft [宣佈](https://azure.microsoft.com/blog/pscloudshellrefresh/)變更 Azure Cloud Shell 中的 PowerShell。  Azure Cloud Shell 中的 PowerShell 體驗現為 Linux 中的 PowerShell Core 6。
變更後，Cloud Shell 中的 PowerShell 與 Windows PowerShell 5.1 在某些層面上可能會出現不同於預期的差異。

## <a name="case-sensitivity"></a>區分大小寫

在 Windows 中，檔案系統不區分大小寫。  在 Linux 中，檔案系統會區分大小寫。
這表示先前系統會將 `file.txt` 與 `FILE.txt` 視為同一個檔案，不過現在則會視為不同的檔案。
在檔案系統中以 `tab` 鍵完成命令時，必須使用正確的大小寫。  PowerShell 特有的用法 (如 `tab` Cmdlet) 則不區分大小寫。 

## <a name="windows-powershell-alias-vs-linux-utilities"></a>Windows PowerShell 別名與 Linux 公用程式

Linux 中的現有命令 (如 `ls`、`sort` 及 `sleep`)，其優先順序高於其在 PowerShell 的別名。  以下是已移除的常見別名及對等的命令：  

|已移除的別名   |對等命令   |
|---|---|
|`ls`     | `dir` <br> `Get-ChildItem` |
|`sort`   | `Sort-Object` |
|`sleep`  | `Start-Sleep` |

## <a name="persisting-home-vs-homeclouddrive"></a>保存 $home 與 $home\clouddrive

若使用者將指令碼與其他檔案保存在雲端磁碟機，則現在所有工作階段均會保存 $HOME 目錄。

## <a name="powershell-profile"></a>PowerShell 設定檔

根據預設，系統不會建立 PowerShell 設定檔。  若要建立設定檔，請在 `$HOME/.config` 之下建立 `PowerShell` 目錄。  在 `$HOME/.config/PowerShell` 中，您可以在 `Microsoft.PowerShell_profile.ps1` 名稱之下建立您的設定檔。

## <a name="whats-new-in-powershell-core-6"></a>PowerShell Core 6 的新功能

如需 PowerShell Core 6 新功能的詳細資訊，請參考 [PowerShell 文件](https://docs.microsoft.com/powershell/scripting/whats-new/what-s-new-in-powershell-core-60?view=powershell-6)和[開始使用 PowerShell Core](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/) 的部落格文章
