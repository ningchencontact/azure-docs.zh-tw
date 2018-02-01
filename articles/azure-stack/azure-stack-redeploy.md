---
title: "重新部署 Azure Stack | Microsoft Docs"
description: "重新部署 Azure Stack。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 795af5ea-892d-40b1-a080-42e4472e4bba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: jeffgilb
ms.openlocfilehash: 0dec5ea70376ff1c8cf488689f1a66190256f6ff
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2018
---
# <a name="redeploy-azure-stack"></a>重新部署 Azure Stack
如果部署 Azure Stack 時收到錯誤，您可以用以下 PowerShell 命令來重新執行安裝：`.\InstallAzureStackpoc.ps1 -rerun`。 此命令會從先前失敗之處重新啟動 Azure Stack 安裝，而不需要從頭開始。 如果再次收到相同的安裝錯誤，則可能必須執行完整的重新部署以解決問題。 

若要重新部署 Azure Stack，您必須從頭開始進行，如下所述。

## <a name="steps-to-redeploy-azure-stack"></a>重新部署 Azure Stack 的步驟
1. 在開發套件主機上，開啟提升權限的 PowerShell 主控台 > 瀏覽至 asdk installer.ps1 指令碼 > 執行此程式碼 > 按一下 [重新開機]。
2. 選取基礎作業系統 (非 Azure Stack) 並按一下 [下一步]。
3. 開發套件主機重新開機之後，刪除先前部署過程中所使用的 CloudBuilder.vhdx 檔案。
4. [部署開發套件](azure-stack-run-powershell-script.md)。

## <a name="next-steps"></a>後續步驟
[連接至 Azure Stack](azure-stack-connect-azure-stack.md)

