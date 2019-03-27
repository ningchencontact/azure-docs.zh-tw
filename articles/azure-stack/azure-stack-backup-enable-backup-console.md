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
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 03/14/2019
ms.openlocfilehash: 98f793b7d94cd554d426a0eec30d8bb4553d3d81
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/19/2019
ms.locfileid: "58105398"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>從系統管理入口網站啟用 Azure Stack 的備份
透過系統管理入口網站啟用基礎結構備份服務，以便 Azure Stack 產生基礎結構備份。 發生[嚴重失敗](./azure-stack-backup-recover-data.md)情況時，硬體合作夥伴可以透過雲端復原使用這些備份來還原環境。 雲端復原的目的在於確保您的操作員和使用者在復原完成後，可以重新登入入口網站。 使用者將會還原其訂用帳戶，包括角色型存取權限與角色、原始方案、供應項目，以及先前定義的計算、儲存體、網路配額和 Key Vault 祕密。

不過，基礎結構備份服務不會備份 IaaS VM、網路設定與儲存體資源 (例如儲存體帳戶、Blob、資料表等)，因此雲端復原完成之後登入的使用者將不會看到其先前存在的任何資源。 服務也不會備份平台即服務 (PaaS) 資源和資料。 

系統管理員和使用者會負責在基礎結構備份程序之外，個別備份及還原 IaaS 和 PaaS 資源。 如需備份 IaaS 和 PaaS 資源的資訊，請參閱下列連結：

- [虛擬機器](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [App Service](https://docs.microsoft.com/azure/app-service/manage-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


## <a name="enable-or-reconfigure-backup"></a>啟用或重新設定備份

1. 開啟 [Azure Stack 系統管理入口網站](azure-stack-manage-portals.md)。
2. 選取 [所有服務]，然後在 [管理] 類別下，選取 [基礎結構備份]。 在 [基礎結構備份] 刀鋒視窗中選擇 [設定]。
3. 輸入**備份儲存位置**的路徑。 針對裝載在不同裝置的檔案共用路徑，請使用通用命名慣例 (UNC) 字串。 UNC 字串會指定資源的位置，例如共用的檔案或裝置。 針對服務，您可以使用 IP 位址。 為了在災害發生之後確保備份資料的可用性，裝置應該位於不同的位置。

    > [!Note]  
    > 若您的環境支援從 Azure Stack 基礎結構網路到您企業環境的名稱解析，就可以使用 FQDN，而不必使用 IP。

4. 使用有足夠存取權可讀取和寫入檔案的網域和使用者名稱來輸入**使用者名稱**。 例如： `Contoso\backupshareuser`。
5. 輸入使用者的**密碼**。
6. 再次輸入密碼以**確認密碼**。
7. **頻率 (小時)** 可決定建立備份的頻率。 預設值為 12。 排程器所支援的最大值為 12，最小值為 4。 
8. **保留期間 (天)** 可決定在外部位置保留多少天的備份。 預設值為 7。 排程器所支援的最大值為 14，最小值為 2。 備份如果比保留期間舊，系統就會自動從外部位置刪除該備份。

    > [!Note]  
    > 如果您想要將比保留期間舊的備份封存，請務必在排程器刪除備份之前備份檔案。 如果您縮短備份保留期間 (例如從 7 天縮短成 5 天)，排程器將會刪除比新保留期間舊的所有備份。 請先確定您同意刪除那些備份，再更新此值。 

9. 在 [加密設定] 的憑證 .cer 檔案方塊中提供憑證。 備份檔案是使用憑證中的此公開金鑰加密的。 設定備份設定時，應提供僅包含公開金鑰部分的憑證。 第一次設定此憑證或將來輪替憑證後，您只能檢視憑證的指紋。 您無法下載或檢視上傳的憑證檔案。 若要建立憑證檔案，請執行下列 PowerShell 命令以使用公開和私密金鑰建立自我簽署的憑證，並匯出僅包含公開金鑰部分的憑證。

    ```powershell

        $cert = New-SelfSignedCertificate `
            -DnsName "www.contoso.com" `
            -CertStoreLocation "cert:\LocalMachine\My"

        New-Item -Path "C:\" -Name "Certs" -ItemType "Directory" 
        Export-Certificate `
            -Cert $cert `
            -FilePath c:\certs\AzSIBCCert.cer 
    ```

   > [!Note]
   > **1901 與更新版本**：Azure Stack 可接受憑證來加密基礎結構備份資料。 請務必將包含公開和私密金鑰的憑證儲存在安全的位置。 基於安全性理由，建議您不要使用包含公開和私密金鑰的憑證來設定備份設定。 如需如何管理此憑證生命週期的詳細資訊，請參閱[基礎結構備份服務的最佳做法](azure-stack-backup-best-practices.md)。
   > 
   > **1811 或更早版本**：Azure Stack 接受以對稱金鑰為基礎結構備份資料加密。 請使用 [New-AzsEncryptionKey64 Cmdlet 建立金鑰](https://docs.microsoft.com/en-us/powershell/module/azs.backup.admin/new-azsencryptionkeybase64)。 從 1811 升級至 1901 之後，備份設定將會保留加密金鑰。 建議您更新備份設定以使用憑證。 加密金鑰支援現已淘汰。 您至少會有 3 個版本可更新設定以使用憑證。 

10. 選取 [確定] 以儲存備份控制器設定。

![Azure Stack - 備份控制器設定](media/azure-stack-backup/backup-controller-settings-certificate.png)


## <a name="start-backup"></a>開始備份
若要開始備份，請按一下 [立即備份] 來開始隨選備份。 隨選備份不會修改下一個已排定備份的時間。 在工作完成之後，您可以在 [基本資訊]中確認設定：

![Azure Stack - 隨選備份](media/azure-stack-backup/scheduled-backup.png)

您也可以在 Azure Stack 系統管理電腦上執行 PowerShell Cmdlet **Start-AzsBackup**。 如需詳細資訊，請參閱[備份 Azure Stack](azure-stack-backup-back-up-azure-stack.md)。

## <a name="enable-or-disable-automatic-backups"></a>啟用或停用自動備份
當您啟用備份時，便會自動排定備份。 您可以在 [基本資訊] 中查看下一個排程備份時間。 

![Azure Stack - 隨選備份](media/azure-stack-backup/on-demand-backup.png)

如果您需要停用未來的已排定備份，請按一下 [停用自訂備份]。 停用自動備份會保留已設定的備份設定，並且會保留備份排程。 此動作只是告訴排程器略過未來的備份。 

![Azure Stack - 停用已排定的備份](media/azure-stack-backup/disable-auto-backup.png)

確認在 [基本資訊] 中已停用未來的已排定備份：

![Azure Stack - 確認已停用備份](media/azure-stack-backup/confirm-disable.png)

按一下 [啟用自動備份] 以通知排程器在排定的時間開始未來的備份。 

![Azure Stack - 啟用已排定的備份](media/azure-stack-backup/enable-auto-backup.png)


> [!Note]  
> 如果您在更新至 1807 之前已設定基礎結構備份，自動備份將會停用。 如此一來，由 Azure Stack 啟動的備份就不會與外部工作排程引擎所啟動的備份發生衝突。 在您停用任何外部工作排程器之後，請按一下 [啟用自動備份]。

## <a name="update-backup-settings"></a>更新備份設定
1901 之後，對加密金鑰的支援已過時。 如果您是在 1901 第一次設定備份，則必須使用憑證。 僅在更新至 1901 之前就已設定金鑰，Azure Stack 才支援加密密鑰。 回溯相容性模式將針對三個版本繼續。 在那之後，將不再支援加密金鑰。 

### <a name="default-mode"></a>預設模式
在加密設定中，如果在安裝或更新至 1901 後第一次設定基礎結構備份，則必須使用憑證設定備份。 不再支援使用加密密鑰。 

若要更新用來加密備份資料的憑證，可以上傳具有公開金鑰部分的新 .CER 檔案，然後選取 [確定] 以儲存設定。 

新的備份將開始使用新憑證中的公開金鑰。 對使用先前的憑證建立的所有現有備份沒有影響。 請務必將較舊的憑證保存在安全的位置，以防進行雲端復原時需要使用。

![Azure Stack - 檢視憑證指紋](media/azure-stack-backup/encryption-settings-thumbprint.png)

### <a name="backwards-compatibility-mode"></a>回溯相容性模式
如果在更新至 1901 之前設定備份，則會繼續進行設定而不會變更行為。 在此情況下，會針對回溯相容性支援加密金鑰。 您可以選擇更新加密金鑰，或切換為使用憑證。 您至少會有三個版本可繼續更新加密金鑰。 使用此時間轉換為憑證。 若要建立新的加密金鑰，請使用 [New-AzsEncryptionKeyBase64 Cmdlet](https://docs.microsoft.com/en-us/powershell/module/azs.backup.admin/new-azsencryptionkeybase64)。

![Azure Stack - 在回溯相容性模式中使用加密金鑰](media/azure-stack-backup/encryption-settings-backcompat-encryption-key.png)

> [!Note]  
> 從加密金鑰更新到憑證是單向作業。 完成此變更之後，您無法切換回加密金鑰。 所有現有的備份將使用先前的加密金鑰維持加密狀態。 

![Azure Stack - 在回溯相容性模式中使用加密憑證](media/azure-stack-backup/encryption-settings-backcompat-certificate.png)

## <a name="next-steps"></a>後續步驟

了解如何執行備份。 請參閱[備份 Azure Stack](azure-stack-backup-back-up-azure-stack.md)

了解如何確認備份已執行完成。 請參閱[在系統管理入口網站中確認備份已完成](azure-stack-backup-back-up-azure-stack.md)
