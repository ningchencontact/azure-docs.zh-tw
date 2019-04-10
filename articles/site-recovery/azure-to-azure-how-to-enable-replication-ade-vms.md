---
title: 在 Azure Site Recovery 中設定已啟用 Azure 磁碟加密 (ADE) 之 VM 的複寫 | Microsoft Docs
description: 此文章說明如何使用 Site Recovery 將 ADE VM 從一個 Azure 區域複寫到另一個區域。
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: b3e997a37bb5d030d559b6771b2c0e2f74cc62ab
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2019
ms.locfileid: "59277678"
---
# <a name="replicate-azure-disk-encryption-ade-enabled-virtual-machines-to-another-azure-region"></a>將已啟用 Azure 磁碟加密 (ADE) 的虛擬機器複寫到另一個 Azure 區域

此文章說明如何將已啟用 Azure 磁碟加密 (ADE) 的 VM 從一個 Azure 區域複寫到另一個區域。

>[!NOTE]
>Azure Site Recovery 目前僅支援執行 Windows OS 及[可以使用 Azure AD 應用程式進行加密](https://aka.ms/ade-aad-app)的 Azure VM。
>

## <a name="required-user-permissions"></a>必要的使用者權限
Azure Site Recovery 要求使用者有權限在目標區域建立金鑰保存庫，並將金鑰複製到區域。

若要從入口網站啟用 ADE VM 的複寫，使用者應該要具有下列權限。
- 金鑰保存庫權限
    - list
    - 建立
    - 取得

-   金鑰保存庫祕密權限
    - 列出
    - 建立
    - 取得

- 金鑰保存庫金鑰權限 (只有在 VM 會使用金鑰加密金鑰來對磁碟加密金鑰進行加密的情況下才需要)
    - 列出
    - 取得
    - 建立
    - 加密
    - 解密

您可以透過在入口網站中瀏覽至金鑰保存庫資源，並為使用者新增必要權限來管理權限。 例如：下方的逐步指南說明如何啟用在來源區域中的金鑰保存庫「ContosoWeb2Keyvault」。


-  瀏覽至「[首頁] > [金鑰保存庫] > ContosoWeb2KeyVault> [存取原則]」

![金鑰保存庫權限](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)



- 您可以看到沒有使用者權限，因此依序按一下 [新增] 及 [使用者] 和 [權限] 來增加上述的權限

![金鑰保存庫權限](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

如果啟用災害復原 (DR) 的使用者沒有複製金鑰的必要權限，則可以將下方的指令碼給予具有適當權限的安全性系統管理員，以將加密祕密和金鑰複製到目標區域。

請參閱[此文章](#trusted-root-certificates-error-code-151066)為權限問題疑難排解。
>[!NOTE]
>若要從入口網站啟用 ADE VM 的複寫，您針對金鑰保存庫、祕密及金鑰必須至少具有 "List" 權限
>

## <a name="copy-ade-keys-to-dr-region-using-powershell-script"></a>使用 PowerShell 指令碼將 ADE 金鑰複製到 DR 區域

1. 按一下[這個連結](https://aka.ms/ade-asr-copy-keys-code)以在瀏覽器視窗中開啟 'CopyKeys' 原始指令碼。
2. 將指令碼複製到檔案中，並將它命名為 'Copy-keys.ps1'。
2. 開啟 Windows PowerShell 應用程式，然後移至該檔案所在的資料夾位置。
3. 啟動 'Copy-keys.ps1'
4. 提供 Azure 登入認證。
5. 選取您 VM 的 **Azure 訂用帳戶**。
6. 等候資源群組載入完成，然後選取您 VM 的**資源群組**。
7. 從所顯示的 VM 清單中選取 VM。 只有已啟用 Azure 磁碟加密的 VM 會顯示在清單中。
8. 選取**目標位置**。
9. **磁碟加密金鑰保存庫**：依預設，Azure Site Recovery 會根據來源 VM 磁碟加密金鑰，在目標區域中建立名稱尾端有 "asr" 的新金鑰保存庫。 如果 Azure Site Recovery 建立的金鑰保存庫已經存在，則會重複使用。 如有必要，您可以從清單中選取不同的金鑰保存庫。
10. **金鑰加密金鑰保存庫**：依預設，Azure Site Recovery 會根據來源 VM 金鑰加密金鑰，在目標區域中建立名稱尾端有 "asr" 的新金鑰保存庫。 如果 Azure Site Recovery 建立的金鑰保存庫已經存在，則會重複使用。 如有必要，您可以從清單中選取不同的金鑰保存庫。

## <a name="enable-replication"></a>啟用複寫

此程序假設主要 Azure 區域為東亞，而次要區域為東南亞。

1. 在保存庫中，按一下 [+複寫]。
2. 請注意以下欄位：
    - **來源**：VM 的起點，在此案例中為 **Azure**。
    - **來源位置**：您想要保護虛擬機器的 Azure 區域。 在此圖例中，來源位置是 [東亞]
    - **部署模型**：來源機器的 Azure 部署模型。
    - **來源訂用帳戶**：來源虛擬機器所屬的訂用帳戶。 這可以是您的復原服務保存庫所在的相同 Azure Active Directory 租用戶內的任何訂用帳戶。
    - **資源群組**：來源虛擬機器所屬的資源群組。 下一個步驟會列出所選取資源群組下的所有 VM，以供保護。

3. 在 [虛擬機器] > [選取虛擬機器] 中，按一下並選取您要複寫的每部 VM。 您只能選取可以啟用複寫的機器。 然後按 [下一步] 。

4. 在 [設定] 中，您可以選擇性地設定目標網站設定：

    - **目標位置**：將複寫來源虛擬機器資料的位置。 端視您選取的機器位置而定，Site Recovery 將提供適當目標區域的清單。 建議您讓目標位置與復原服務保存庫位置保持相同。
    - **目標訂用帳戶**：用於災害復原的目標訂用帳戶。 根據預設，目標訂用帳戶會與來源訂用帳戶相同。
    - **目標資源群組**：所有已複寫虛擬機器所屬的資源群組。 根據預設，Azure Site Recovery 會在目標區域中建立名稱尾碼為 "asr" 的新資源群組。 如果 Azure Site Recovery 建立的資源群組已經存在，則會重複使用。 您也可以選擇對它進行自訂，如下所示。 目標資源群組的位置可以是任何 Azure 區域，但是裝載您來源虛擬機器所在的區域除外。
    - **目標虛擬網路**：根據預設，Site Recovery 會在目標區域中建立名稱包含 "asr" 尾碼的新虛擬網路。 這會對應至您的來源網路，並用來進行任何未來的保護。 [深入了解](site-recovery-network-mapping-azure-to-azure.md)網路對應。
    - **目標儲存體帳戶 (如果來源 VM 不使用受控磁碟)**：根據預設，Site Recovery 會建立新的目標儲存體帳戶，以模擬您的來源 VM 儲存體設定。 如果儲存體帳戶已經存在，就會重複使用。
    - **複本受控磁碟 (如果您的來源 VM 使用受控磁碟)**：Site Recovery 會在目標區域中建立新的複本受控磁碟，建立與來源 VM 的受控磁碟相同儲存類型 (標準或進階) 之來源 VM 受控磁碟的鏡像。
    - **快取儲存體帳戶**：Site Recovery 需要在來源區域中有額外的儲存體帳戶 (稱為快取儲存體)。 在來源 VM 上發生的所有變更都會受到追蹤，並傳送到快取儲存體帳戶，然後複寫到目標位置。
    - **可用性設定組**：根據預設，Azure Site Recovery 會在目標區域中建立名稱尾碼為 "asr" 的新可用性設定組。 如果 Azure Site Recovery 建立的可用性設定組已經存在，則會重複使用。
    - **磁碟加密金鑰保存庫**：依預設，Azure Site Recovery 會根據來源 VM 磁碟加密金鑰，在目標區域中建立名稱尾端有 "asr" 的新金鑰保存庫。 如果 Azure Site Recovery 建立的金鑰保存庫已經存在，則會重複使用。
    - **金鑰加密金鑰保存庫**：依預設，Azure Site Recovery 會根據來源 VM 金鑰加密金鑰，在目標區域中建立名稱尾端有 "asr" 的新金鑰保存庫。 如果 Azure Site Recovery 建立的金鑰保存庫已經存在，則會重複使用。
    - **複寫原則**：這會定義復原點保留期歷程記錄和應用程式一致快照集頻率的設定。 根據預設，Azure Site Recovery 會對於復原點保留期使用「24 小時」的預設設定來建立新的複寫原則，並對於應用程式一致快照集頻率使用「60 分鐘」。



## <a name="customize-target-resources"></a>自訂目標資源

您可以修改 Site Recovery 所使用的預設目標設定。


1. 按一下「目標訂用帳戶」旁的 [自訂] 來修改預設目標訂用帳戶。 在同一個 Azure Active Directory (AAD) 租用戶中，從所有可用的訂用帳戶清單中選取訂用帳戶。

2. 按一下 [資源群組、網路、儲存體和可用性設定組] 旁的 [自訂:] 來修改下列預設設定：
    - 在 [目標資源群組] 中，從訂用帳戶目標位置上的所有資源群組清單中選取資源群組。
    - 在 [目標虛擬網路] 中，從目標位置上的所有虛擬網路清單中選取網路。
    - 在 [可用性設定組] 中，如果 VM 是來源區域中可用性設定組的一部分，即可將可用性設定組新增至其中。
    - 在 [目標儲存體帳戶] 中，選取您要使用的帳戶。


2. 按一下 [加密設定] 旁的 [自訂:] 來修改下列預設設定：
   - 在 [目標磁碟加密金鑰保存庫] 中，從訂用帳戶目標位置中所有金鑰保存庫的清單中，選取目標磁碟加密金鑰保存庫。
     - 在 [目標金鑰加密金鑰保存庫] 中，從訂用帳戶目標位置中所有金鑰保存庫的清單中，選取目標金鑰加密金鑰保存庫。

3. 按一下 [建立目標資源] > [啟用複寫]。
4. 啟用 VM 以進行複寫之後，您就可以在 [複寫的項目] 下方檢查 VM 健康情況的狀態。

>[!NOTE]
>在初始複寫期間，狀態可能需要一些時間才會重新整理，期間不會有任何進展。 按一下 [重新整理] 按鈕來取得最新狀態。
>

## <a name="update-target-vm-encryption-settings"></a>更新目標 VM 加密設定
在下面的案例中，您必須更新目標 VM 加密設定。
  - 您已在 VM 上啟用 Site Recovery 複寫，並在日後於來源 VM 上啟用 Azure 磁碟加密 (ADE)
  - 您已在 VM 上啟用 Site Recovery 複寫，並在日後變更來源 VM 上的磁碟加密金鑰和/或金鑰加密金鑰

您可以使用[指令碼](#copy-ade-keys-to-dr-region-using-powershell-script)來將加密金鑰複製到目標區域，然後在 [復原服務保存庫] -> [已複寫的項目] -> [屬性] -> [計算與網路] 中更新目標加密設定。

![update-ade-settings](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a name="trusted-root-certificates-error-code-151066"></a>Azure 對 Azure VM 複寫期間的金鑰保存庫權限問題疑難排解

**原因 1：** 您可能是從不具有必要權限的目標區域中，選取了已建立的金鑰保存庫。
如果您要在目標區域中選取已建立的金鑰保存庫，而非讓 Azure Site Recovery 建立。 請確保金鑰保存庫具備先前所述的權限。</br>
*例如*：使用者嘗試複寫的 VM 在來源區域上的金鑰保存庫顯示「ContososourceKeyvault」。
使用者在來源區域上具備所有權限，但他在保護期間選取已建立的金鑰保存庫「ContosotargetKeyvault」，因此不具備權限，而保護則會擲回錯誤。</br>
**修正方式：** 前往「[首頁] > [金鑰保存庫] > [ContososourceKeyvault] > [存取原則]」，並新增權限，如上所示。

**原因 2：** 您可能是從沒有解密加密權限的目標區域中，選取了已建立的金鑰保存庫。
如果您要在目標區域中選取已建立的金鑰保存庫，而非讓 Azure Site Recovery 建立。 如果您也要在來源區域上為金鑰加密，請確保使用者具有解密加密權限。</br>
*例如*：使用者嘗試複寫的 VM 在來源區域上的金鑰保存庫顯示「ContososourceKeyvault」。
使用者在來源區域上具備所有權限，但他在保護期間選取已建立的金鑰保存庫「ContosotargetKeyvault」，因此不具備解密和加密權限。</br>
**修正方式：** 前往「[首頁] > [金鑰保存庫] > [ContososourceKeyvault] > [存取原則]」，並在 [金鑰權限] 底下 > [密碼編譯作業] 新增權限。

## <a name="next-steps"></a>後續步驟

[深入了解](site-recovery-test-failover-to-azure.md)執行測試容錯移轉。
