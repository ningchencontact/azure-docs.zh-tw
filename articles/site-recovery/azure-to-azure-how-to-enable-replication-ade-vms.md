---
title: Azure Site Recovery 中設定 Azure 磁碟加密啟用 Vm 的複寫 |Microsoft Docs
description: 本文說明如何使用 Site Recovery 中設定的 Azure 磁碟加密啟用的 Vm 從一個 Azure 區域之間複寫。
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 4943b730bb46ee00200d84faf95a7ccb069d3aa8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60790973"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>啟用 Azure 磁碟加密的虛擬機器複寫到另一個 Azure 區域

本文說明如何從一個 Azure 區域的 Azure 磁碟加密啟用 Vm 複寫至另一個。

>[!NOTE]
>Azure Site Recovery 目前僅支援 Azure Vm 執行 Windows OS，以及屬於[進行加密與 Azure Active Directory (Azure AD)](https://aka.ms/ade-aad-app)。

## <a name="required-user-permissions"></a>必要的使用者權限
Site Recovery 需要有在目標區域和複製金鑰至區域中建立金鑰保存庫的權限的使用者。

若要啟用從 Azure 入口網站中啟用磁碟加密的 Vm 的複寫，使用者會需要下列權限：

- 金鑰保存庫權限
    - 列出
    - 建立
    - 取得

-   金鑰保存庫祕密權限
    - 列出
    - 建立
    - 取得

- 金鑰保存庫金鑰權限 （在 Vm 使用金鑰加密金鑰來加密磁碟加密金鑰時，才需要）
    - 列出
    - 取得
    - 建立
    - 加密
    - 解密

若要管理權限，請移至入口網站中的金鑰保存庫資源。 新增使用者的必要權限。 下列範例示範如何啟用金鑰保存庫的權限*ContosoWeb2Keyvault*，這是來源區域中。

1. 移至**首頁** > **Keyvaults** > **ContosoWeb2KeyVault > 存取原則**。

   ![金鑰保存庫權限 視窗](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. 您可以看到不有任何使用者權限。 選取 [新增]  。 輸入的使用者和權限資訊。

   ![金鑰保存庫的權限](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

如果已啟用災害復原 (DR) 的使用者沒有權限來複製金鑰，安全性系統管理員擁有適當的權限可以使用下列指令碼，將加密祕密和金鑰複製到目標區域。

若要疑難排解權限，請參閱[金鑰保存庫權限問題](#trusted-root-certificates-error-code-151066)本文稍後。

>[!NOTE]
>若要啟用磁碟加密啟用的 Vm，從入口網站的複寫，您需要至少 「 列出 」 金鑰保存庫、 密碼和金鑰的權限。

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>使用 PowerShell 指令碼複製到 DR 區域的磁碟加密金鑰

1. [開啟 「 CopyKeys 「 未經處理的指令碼](https://aka.ms/ade-asr-copy-keys-code)。
2. 將指令碼複製到檔案，並將它命名**複製 keys.ps1**。
3. 開啟 Windows PowerShell 應用程式，並移至您儲存檔案的資料夾。
4. 執行複製 keys.ps1。
5. 提供 Azure 認證來登入。
6. 選取您 VM 的 **Azure 訂用帳戶**。
7. 等候的資源群組，若要載入，然後按**資源群組**的 Vm。
8. 從清單中顯示選取的 Vm。 已啟用磁碟加密的 Vm 會在清單上。
9. 選取 **目標位置**。

    - **磁碟加密的金鑰保存庫**
    - **金鑰加密的金鑰保存庫**

   根據預設，Site Recovery 會在目標區域中建立新的金鑰保存庫。 保存庫的名稱有"asr"尾碼的來源 VM 磁碟加密金鑰為基礎。 如果金鑰保存庫已存在所建立的 Site Recovery，就會重複使用。 從清單中，如有必要，選取不同的金鑰保存庫。

## <a name="enable-replication"></a>啟用複寫

此範例中，主要的 Azure 區域為東亞，以及次要區域為東南亞。

1. 在 保存庫中，選取 **+ 複寫**。
2. 請注意下列欄位。
    - **來源**：VM 的起點，在此案例中為 **Azure**。
    - **來源位置**：您想要用來保護您的虛擬機器的 Azure 區域。 此範例中，來源位置是 「 東亞。 」
    - **部署模型**：來源機器的 Azure 部署模型。
    - **來源訂用帳戶**：來源虛擬機器所屬的訂用帳戶。 它可以是與您的復原服務保存庫相同的 Azure Active Directory 租用戶中的任何訂用帳戶。
    - **資源群組**：來源虛擬機器所屬的資源群組。 選取的資源群組中的所有 Vm 會都列出在下一個步驟中的保護。

3. 在 **虛擬機器** > **選取 虛擬機器**，選取您想要複寫的每部 VM。 您只能選取可以啟用複寫的機器。 然後選取 [確定]  。

4. 在 **設定**，您可以設定下列目標站台設定。

    - **目標位置**：將複寫來源虛擬機器資料的位置。 Site Recovery 會提供選取的機器位置為基礎的適當目標區域的清單。 我們建議您為復原服務保存庫的位置，使用相同的位置。
    - **目標訂用帳戶**：目標訂用帳戶用於災害復原。 根據預設，目標訂用帳戶會與來源訂用帳戶相同。
    - **目標資源群組**：所有已複寫虛擬機器所屬的資源群組。 根據預設，Site Recovery 會在目標區域中建立新的資源群組。 名稱取得"asr"尾碼。 如果資源群組已經存在所建立的 Azure Site Recovery，就會重複使用。 下一節中所示，您也可以選擇進行自訂。 目標資源群組的位置可以是任何 Azure 區域以外的來源虛擬機器的裝載位置的區域。
    - **目標虛擬網路**：根據預設，Site Recovery 會在目標區域中建立新的虛擬網路。 名稱取得"asr"尾碼。 它已對應至您的來源網路，並用於任何未來的保護。 [深入了解](site-recovery-network-mapping-azure-to-azure.md)網路對應。
    - **目標儲存體帳戶 （如果您的來源 VM 未使用受控磁碟）** :根據預設，Site Recovery 會建立新的目標儲存體帳戶，藉由模擬來源 VM 儲存體組態。 如果儲存體帳戶已經存在，就會重複使用。
    - **複本受控磁碟 （如果來源 VM 使用受控的磁碟）** :Site Recovery 會建立新的複本受控磁碟，以反映來源 VM 的受控的磁碟的相同儲存體類型 （標準或進階） 做為來源 VM 的受控磁碟的目標區域中。
    - **快取儲存體帳戶**：Site Recovery 需要額外的儲存體帳戶名*快取儲存體*來源區域中。 來源 Vm 上的所有變更會追蹤並傳送至快取儲存體帳戶。 它們會再複寫到目標位置。
    - **可用性設定組**：根據預設，Site Recovery 會建立新的可用性設定的目標區域中。 名稱有"asr"尾碼。 如果已由 Site Recovery 所建立的可用性設定組存在，就會重複使用。
    - **磁碟加密金鑰保存庫**：根據預設，Site Recovery 會在目標區域中建立新的金鑰保存庫。 它具有"asr"尾碼的來源 VM 磁碟加密金鑰為基礎。 如果已由 Azure Site Recovery 所建立的金鑰保存庫存在，就會重複使用。
    - **金鑰加密金鑰保存庫**：根據預設，Site Recovery 會在目標區域中建立新的金鑰保存庫。 名稱有"asr"尾碼為基礎的來源 VM 的金鑰加密金鑰。 如果金鑰保存庫，Azure Site Recovery 建立的已存在，就會重複使用。
    - **複寫原則**：定義復原點保留歷程記錄和應用程式一致快照集頻率的設定。 根據預設，Site Recovery 會建立新的複寫原則的預設*24 小時*復原點保留並*60 分鐘*應用程式一致快照集頻率。

## <a name="customize-target-resources"></a>自訂目標資源

請依照下列步驟，以修改站台復原預設的目標設定。

1. 選取 **自訂**旁邊"目標訂用帳戶 」 來修改預設的目標訂用帳戶。 從 Azure AD 租用戶中可用的訂用帳戶的清單中選取訂用帳戶。

2. 選取 **自訂**旁"資源群組、 網路、 儲存體和可用性設定組 」 來修改下列的預設值：
    - 針對**目標資源群組**，從目標位置的訂用帳戶中的資源群組清單中選取的資源群組。
    - 針對**目標虛擬網路**，從目標位置中的虛擬網路清單中選取網路。
    - 針對**可用性設定組**，您可以加入可用性設定的 vm，如果它們是可用性設定組位於來源區域的一部分。
    - 針對**目標儲存體帳戶**，選取要使用的帳戶。

2. 選取 **自訂**旁 加密設定 來修改下列的預設值：
   - 針對**目標磁碟加密金鑰保存庫**，從目標位置的訂用帳戶中的金鑰保存庫的清單中選取目標磁碟加密金鑰的保存庫。
   - 針對**目標金鑰加密的金鑰保存庫**，從目標位置的訂用帳戶中的金鑰保存庫的清單中選取目標金鑰加密金鑰保存庫。

3. 選取 **建立目標資源** > **啟用複寫**。
4. Vm 已啟用複寫之後，您可以檢查 Vm 健康情況狀態，底下**複寫的項目**。

>[!NOTE]
>在初始複寫期間狀態可能需要一些時間才能重新整理，而不明顯的進度。 按一下 **重新整理**以取得最新狀態。

## <a name="update-target-vm-encryption-settings"></a>更新目標 VM 加密設定
在下列案例中，您將需要更新的目標 VM 的加密設定：
  - 您已啟用 VM 上的 Site Recovery 複寫。 稍後，您已啟用來源 VM 上的磁碟加密。
  - 您已啟用 VM 上的 Site Recovery 複寫。 稍後，您要變更的磁碟加密金鑰或金鑰加密金鑰來源 VM 上。

您可以使用[指令碼](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script)複製到目標區域的加密金鑰，並進行更新中的目標加密設定**復原服務保存庫** > *複寫的項目* > **屬性** > **計算與網路**。

![更新 ADE 設定對話方塊 視窗](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a id="trusted-root-certificates-error-code-151066"></a>疑難排解 Azure 至 Azure VM 複寫期間的金鑰保存庫權限問題

**原因 1：** 您可能會從所選取的目標區域的已建立金鑰保存庫沒有必要的權限，而不是讓 Site Recovery 建立一個。 請確定金鑰保存庫具有需要權限，如先前所述。

*例如*：您嘗試將複寫的 VM 具有金鑰保存庫*ContososourceKeyvault*來源區域。
對來源區域的 key vault 中的所有權限。 但在保護期間，您可以選取已建立金鑰保存庫 ContosotargetKeyvault，沒有權限。 發生錯誤。

**修正方式：** 移至**首頁** > **Keyvaults** > **ContososourceKeyvault** > **存取原則**並新增適當的權限。

**原因 2：** 您可能會從所選取目標區域的已建立金鑰保存庫沒有解密加密而不是讓 Site Recovery 建立的權限。 請確定您已解密的加密權限如果您也要加密之金鑰的來源地區。</br>

*例如*：您嘗試將複寫的 VM 具有金鑰保存庫*ContososourceKeyvault*來源區域。 您在來源區域的金鑰保存庫上有所有必要的權限。 但在保護期間，您可以選取已建立金鑰保存庫 ContosotargetKeyvault，沒有權限來解密和加密。 發生錯誤。</br>

**修正方式：** 移至**首頁** > **Keyvaults** > **ContososourceKeyvault** > **存取原則**。 新增權限之下**金鑰權限** > **密碼編譯作業**。

## <a name="next-steps"></a>後續步驟

[深入了解](site-recovery-test-failover-to-azure.md)執行測試容錯移轉。
