---
title: 從系統管理入口網站啟用 Azure Stack 的備份 | Microsoft Docs
description: 透過系統管理入口網站啟用基礎結構備份服務，以便在發生失敗時，可以將 Azure Stack 還原。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 56C948E7-4523-43B9-A236-1EF906A0304F
ms.service: azure-stack
ms.workload: naS
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: jeffgilb
ms.openlocfilehash: ce5fd2feaa30948042cc0570a4b0ea7f0ab7ad77
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/18/2018
ms.locfileid: "34302251"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>從系統管理入口網站啟用 Azure Stack 的備份
透過系統管理入口網站啟用基礎結構備份服務，以便 Azure Stack 產生備份。 發生[嚴重失敗](.\azure-stack-backup-recover-data.md)情況時，您可以透過雲端復原使用這些備份來還原環境。 雲端復原的目的在於確保您的操作員和使用者在復原完成後，可以重新登入入口網站。 使用者將會還原其訂用帳戶，包括角色型存取權限和角色、原始方案、供應項目，以及先前定義的計算、儲存體和網路配額。

不過，基礎結構備份服務不會備份 IaaS VM、網路設定和儲存體資源 (例如儲存體帳戶、Blob、資料表等)，因此雲端復原完成之後登入的使用者將不會看到其先前存在的任何資源。 服務也不會備份平台即服務 (PaaS) 資源和資料。 

系統管理員和使用者會負責在基礎結構備份程序之外，個別備份及還原 IaaS 和 PaaS 資源。 如需備份 IaaS 和 PaaS 資源的資訊，請參閱下列連結：

- [虛擬機器](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [App Service](https://docs.microsoft.com/azure/app-service/web-sites-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


> [!Note]  
> 請先設定備份服務，才能透過主控台啟用備份。 您可以使用 PowerShell 設定備份服務。 了解如何[使用 PowerShell 來啟用 Azure Stack 的備份](azure-stack-backup-enable-backup-powershell.md)以取得更多資訊。

## <a name="enable-backup"></a>啟用備份

1. 開啟位於 [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external) 的 Azure Stack 系統管理入口網站。
2. 選取 [更多服務] > [基礎結構的備份]。 在 [基礎結構備份] 刀鋒視窗中選擇 [設定]。

    ![Azure Stack - 備份控制器設定](media\azure-stack-backup\azure-stack-backup-settings.png).

3. 輸入**備份儲存位置**的路徑。 針對裝載在不同裝置的檔案共用路徑，請使用通用命名慣例 (UNC) 字串。 UNC 字串會指定資源的位置，例如共用的檔案或裝置。 針對服務，您可以使用 IP 位址。 為了在災害發生之後確保備份資料的可用性，裝置應該位於不同的位置。
    > [!Note]  
    > 若您的環境支援從 Azure Stack 基礎結構網路到您企業環境的名稱解析，就可以使用 FQDN，而不必使用 IP。
4. 使用有足夠存取權可讀取和寫入檔案的網域和使用者名稱來輸入**使用者名稱**。 例如： `Contoso\backupshareuser`。
5. 輸入使用者的**密碼**。
5. 再次輸入密碼以**確認密碼**。
6. 在 [加密金鑰] 方塊中提供預先共用的金鑰。 備份檔案會使用此金鑰來加密。 請務必將此金鑰儲存在安全的位置。 您第一次設定此金鑰後或在未來更換金鑰時，都無法再從這個介面檢視此金鑰。 如需產生預先共用的詳細指示，請遵照[使用 PowerShell 來啟用 Azure Stack 的備份](azure-stack-backup-enable-backup-powershell.md#generate-a-new-encryption-key)中的指令碼。 
7. 選取 [確定] 以儲存備份控制器設定。

若要執行備份，您必須下載 Azure Stack 工具，然後在系統管理節點上執行 PowerShell Cmdlet **Start-AzSBackup**。 如需詳細資訊，請參閱[備份 Azure Stack](azure-stack-backup-back-up-azure-stack.md )。

## <a name="next-steps"></a>後續步驟

- 了解如何執行備份。 請參閱[備份 Azure Stack](azure-stack-backup-back-up-azure-stack.md )。
- 了解如何確認備份已執行完成。 請參閱[在系統管理入口網站中確認備份已完成](azure-stack-backup-back-up-azure-stack.md)。
