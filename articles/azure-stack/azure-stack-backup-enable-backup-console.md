---
title: "從系統管理入口網站啟用 Azure Stack 的備份 | Microsoft Docs"
description: "透過系統管理入口網站啟用基礎結構備份服務，以便在發生失敗時，可以將 Azure Stack 還原。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 56C948E7-4523-43B9-A236-1EF906A0304F
ms.service: azure-stack
ms.workload: naS
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: 456a0db9771f5963c8d4375d54a22257f6ca1c56
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2018
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>從系統管理入口網站啟用 Azure Stack 的備份

*適用於：Azure Stack 整合系統和 Azure Stack 開發封裝*

透過系統管理入口網站啟用基礎結構備份服務，以便 Azure Stack 產生備份。 發生失敗情況時，您可以使用這些備份來還原環境。

> [!Note]  
> 請先設定備份服務，才能透過主控台啟用備份。 您可以使用 PowerShell 設定備份服務。 了解如何[使用 PowerShell 來啟用 Azure Stack 的備份](azure-stack-backup-enable-backup-powershell.md)以取得更多資訊。

## <a name="enable-backup"></a>啟用備份

1. 在 [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external) 開啟 Azure Stack 系統管理員入口網站。
2. 選取 [更多服務] > [基礎結構的備份]。 在 [基礎結構備份] 刀鋒視窗中選擇 [設定]。

    ![Azure Stack - 備份控制器設定](media\azure-stack-backup\azure-stack-backup-settings.png).

3. 輸入**備份儲存位置**的路徑。 針對裝載在不同裝置的檔案共用路徑，請使用通用命名慣例 (UNC) 字串。 UNC 字串會指定資源的位置，例如共用的檔案或裝置。 針對服務，您可以使用 IP 位址。 為了在災害發生之後確保備份資料的可用性，裝置應該位於不同的位置。
    > [!Note]  
    > 若您的環境支援從 Azure Stack 基礎結構網路到您企業環境的名稱解析，就可以使用 FQDN，而不必使用 IP。
4. 使用網域和使用者名稱來輸入**使用者名稱**。 例如： `Contoso\administrator`。
5. 輸入使用者的**密碼**。
5. 再次輸入密碼以**確認密碼**。
6. 在 [加密金鑰] 方塊中提供預先共用的金鑰。 備份檔案會使用此金鑰來加密。 請務必將此金鑰儲存在安全的位置。 您第一次設定此金鑰後或在未來更換金鑰時，都無法再從這個介面檢視此金鑰。 如需產生預先共用的詳細指示，請遵照[使用 PowerShell 來啟用 Azure Stack 的備份](azure-stack-backup-enable-backup-powershell.md#generate-a-new-encryption-key)中的指令碼。 
7. 選取 [確定] 以儲存備份控制器設定。

若要執行備份，您必須下載 Azure Stack 工具，然後在系統管理節點上執行 PowerShell Cmdlet **Start-AzSBackup**。 如需詳細資訊，請參閱[備份 Azure Stack](azure-stack-backup-back-up-azure-stack.md )。

## <a name="next-steps"></a>後續步驟

 - 了解如何執行備份。 請參閱[備份 Azure Stack](azure-stack-backup-back-up-azure-stack.md )。
- 了解如何確認備份已執行完成。 請參閱[在系統管理入口網站中確認備份已完成](azure-stack-backup-back-up-azure-stack.md )。
