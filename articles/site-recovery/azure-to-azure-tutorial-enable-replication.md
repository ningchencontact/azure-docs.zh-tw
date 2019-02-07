---
title: 使用 Azure Site Recovery 設定 Azure VM 到次要 Azure 區域的災害復原
description: 了解如何使用 Azure Site Recovery 服務，設定 Azure VM 到不同 Azure 區域的災害復原。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 0e73c0f94e0aa240349aec45b4a146ba5eb37dab
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2019
ms.locfileid: "55700769"
---
# <a name="set-up-disaster-recovery-for-azure-vms-to-a-secondary-azure-region"></a>設定 Azure VM 到次要 Azure 區域的災害復原

[Azure Site Recovery](site-recovery-overview.md) 服務可藉由管理及協調內部部署電腦與 Azure 虛擬機器 (VM) 的複寫、容錯移轉及容錯回復，為您的災害復原策略做出貢獻。

本教學課程說明如何為 Azure VM 設定災害復原至次要 Azure 區域。 在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 建立復原服務保存庫
> * 驗證目標資源設定
> * 設定 VM 的輸出存取
> * 啟用 VM 複寫

> [!NOTE]
> 本文將說明如何以最簡單的設定部署災害復原。 如果您想深入了解自訂設定，請檢閱[操作說明區段](azure-to-azure-how-to-enable-replication.md)底下的文章。 o

## <a name="prerequisites"></a>必要條件

若要完成本教學課程：

- 請確定您了解[情節架構和元件](concepts-azure-to-azure-architecture.md)。
- 檢閱所有元件的[支援需求](site-recovery-support-matrix-azure-to-azure.md)。

## <a name="create-a-vault"></a>建立保存庫

在來源區域以外的任何區域中建立保存庫。

1. 登入 [Azure 入口網站](https://portal.azure.com) > [復原服務]。
2. 按一下 [建立資源] > [管理工具] > [備份和 Site Recovery]。
3. 在 [名稱] 中，指定保存庫的易記識別名稱。 如果您有多個訂用帳戶，請選取適當的一個。
4. 建立資源群組，或選取現有的資源群組。 指定 Azure 區域。 若要查看支援的區域，請參閱 [Azure Site Recovery 定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)。
5. 若要從儀表板快速存取保存庫，請按一下 [釘選到儀表板]，然後按一下 [建立]。

   ![新增保存庫](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   新的保存庫會新增到主要 [復原服務保存庫] 頁面上 [所有資源] 之下的 [儀表板]。

## <a name="verify-target-resources"></a>驗證目標資源

1. 確認您的 Azure 訂用帳戶允許您在目標區域中建立 VM。 請連絡支援人員啟用所需的配額。
2. 確定您的訂用帳戶具有足夠的資源，可支援大小與來源 VM 相符的 VM。 Site Recovery 會為目標 VM 挑選相同的大小或最接近的大小。

## <a name="configure-outbound-network-connectivity"></a>設定輸出網路連線能力

若要 Site Recovery 如預期運作，必須從您需要複寫的 VM 中修改輸出網路連線能力。

> [!NOTE]
> Site Recovery 不支援使用驗證 Proxy 來控制網路連線能力。



### <a name="outbound-connectivity-for-urls"></a>URL 的輸出連線能力

如果您使用以 URL 為基礎的防火牆 Proxy 來控制輸出連線能力，請允許存取這些 URL。

| **URL** | **詳細資料** |
| ------- | ----------- |
| *.blob.core.windows.net | 允許將資料從 VM 寫入來源區域的快取儲存體帳戶中。 |
| login.microsoftonline.com | 提供 Site Recovery 服務 URL 的授權和驗證。 |
| *.hypervrecoverymanager.windowsazure.com | 允許 VM 與 Site Recovery 服務進行通訊。 |
| *.servicebus.windows.net | 允許 VM 寫入 Site Recovery 監視和診斷資料。 |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>IP 位址範圍的輸出連線能力

如果您想要使用 IP 位址控制輸出連線能力，而不是使用 URL，請在 IP 式防火牆、Proxy 或 NSG 規則上允許這些位址。

  - [Microsoft Azure 資料中心 IP 範圍](https://www.microsoft.com/en-us/download/details.aspx?id=41653)
  - [德國 Windows Azure 資料中心 IP 範圍](https://www.microsoft.com/en-us/download/details.aspx?id=54770)
  - [中國 Windows Azure 資料中心 IP 範圍](https://www.microsoft.com/en-us/download/details.aspx?id=42064)
  - [Office 365 URL 與 IP 位址範圍](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [Site Recovery 服務端點 IP 位址](https://aka.ms/site-recovery-public-ips)

您可以使用此[指令碼](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702)來建立所需的 NSG 規則。

## <a name="verify-azure-vm-certificates"></a>驗證 Azure VM 憑證

請檢查您想要複寫的 VM 是否具有最新的根憑證。 如果沒有的話，VM 會因安全性條件限制而無法註冊至 Site Recovery。

- 若為 Windows VM，請安裝所有最新的 Windows 更新，讓所有的受信任根憑證都在機器上。 在中斷連線的環境中，請遵循您組織的標準 Windows Update 和憑證更新程序。
- 若為 Linux VM，請遵循 Linux 散發者提供的指引，以取得最新的受信任根憑證及 VM 的憑證撤銷清單。

## <a name="set-permissions-on-the-account"></a>設定帳戶的權限

Azure Site Recovery 提供 3 種內建角色，以控制 Site Recovery 管理作業。

- **Site Recovery 參與者**：此角色具有在復原服務保存庫中管理 Azure Site Recovery 作業所需的所有權限。 不過，具有此角色的使用者無法建立或刪除復原服務保存庫，也無法為其他使用者指派存取權限。 此角色最適合災害復原系統管理員，他們可以為應用程式或整個組織啟用和管理災害復原。

- **Site Recovery 操作員** - 此角色具有執行和管理容錯移轉和容錯回復作業的權限。 具有此角色的使用者無法啟用或停用複寫、建立或刪除保存庫、註冊新的基礎結構，也無法為其他使用者指派存取權限。 此角色最適合災害復原操作員，在應用程式擁有者和 IT 系統管理員的指示下，操作員可以對虛擬機器或應用程式進行容錯移轉。 災害解決後，災害復原操作員可以重新保護和容錯回復虛擬機器。

- **Site Recovery 讀者**：此角色擁有可檢視所有 Site Recovery 管理作業的權限。 此角色最適合 IT 監督主管，以便監控目前的保護狀態並提出支援票證。

深入了解 [Azure RBAC 內建角色](../role-based-access-control/built-in-roles.md)

## <a name="enable-replication"></a>啟用複寫

### <a name="select-the-source"></a>選取來源

1. 在復原服務保存庫中，按一下 [+複寫]。
2. 在 [來源] 中，選取 [Azure]。
3. 在 [來源位置] 中，選取 VM 目前執行所在的來源 Azure 區域。
4. 選取虛擬機器執行所在的**來源訂用帳戶**。 這可以是您的復原服務保存庫所在的相同 Azure Active Directory 租用戶內的任何訂用帳戶。
5. 針對 Resource Manager VM 選取**來源資源群組**，或針對傳統 VM 選取**雲端服務**。
6. 按一下 [確定]  來儲存設定。

### <a name="select-the-vms"></a>選取 VM

Site Recovery 會擷取與訂用帳戶和資源群組/雲端服務相關聯的 VM 清單。

1. 在 [虛擬機器] 中，選取您要複寫的 VM。
2. 按一下 [確定]。

### <a name="configure-replication-settings"></a>設定複寫設定

Site Recovery 會設定目標區域的預設設定和複寫原則。 您可以根據需求變更設定。

1. 按一下 [設定] 以檢視目標和複寫設定。
2. 若要覆寫預設目標設定，請按一下 [資源群組、網路、儲存體和可用性] 旁的 [自訂]。

  ![配置設定](./media/azure-to-azure-tutorial-enable-replication/settings.png)


3. 自訂目標設定，如下所示：

    - **目標訂用帳戶**：用於災害復原的目標訂用帳戶。 根據預設，目標訂用帳戶會與來源訂用帳戶相同。 按一下 [自訂]，選取相同 Azure Active Directory 租用戶內的不同目標訂用帳戶。
    - **目標位置**：用於災害復原的目標區域。 我們建議目標位置符合 Site Recovery 保存庫的位置。
    - **目標資源群組**：目標區域中在容錯移轉後保留 Azure VM 的資源群組。 根據預設，Site Recovery 會在目標區域中建立具有 "asr" 尾碼的新資源群組。 目標資源群組的資源群組位置可以是任何區域，但是裝載您來源虛擬機器所在的區域除外。
    - **目標虛擬網路**：目標區域中 VM 在容錯移轉後所在的網路。
      根據預設，Site Recovery 會在目標區域中建立具有 "asr" 尾碼的新虛擬網路 (和子網路)。
    - **快取儲存體帳戶**：Site Recovery 會使用來源區域中的儲存體帳戶。 來源 VM 的變更會在複寫到目標位置之前，先傳送到此帳戶。
      >[!NOTE]
      >如果您使用啟用防火牆的快取儲存體帳戶，請確定您「允許信任的 Microsoft 服務」。 [深入了解。](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)
      >

    - **目標儲存體帳戶 (如果來源 VM 不使用受控磁碟)**：根據預設，Site Recovery 會在目標區域中建立新的儲存體帳戶，以反映來源 VM 儲存體帳戶。
      >[!NOTE]
      >如果您使用啟用防火牆的來源或目標儲存體帳戶，請確定您「允許信任的 Microsoft 服務」。 [深入了解。](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)
      >

    - **複本受控磁碟 (如果來源 VM 使用受控磁碟)**：根據預設，Site Recovery 會在目標區域中建立與來源 VM 受控磁碟具有相同儲存體類型 (標準或進階) 的複本受控磁碟，以反映來源 VM 的受控磁碟。
    - **目標可用性設定組**：根據預設，Azure Site Recovery 會針對來源區域的可用性設定組中的 VM，在目標區域中建立名稱尾碼為 "asr" 的新可用性設定組。 如果 Azure Site Recovery 建立的可用性設定組已經存在，則會重複使用。
    - **目標可用性區域**：根據預設，如果目標區域支援可用性區域，則 Site Recovery 會在目標區域中指派與來源區域相同的區域編號。 

    如果目標區域不支援可用性區域，則目標 VM 會預設為單一執行個體。 如有需要，您可以按一下 [自訂]，將這類 VM 設定為目標區域中可用性設定組的一部分。

    >[!NOTE]
    >啟用複寫之後，便無法變更可用性類型 - 單一執行個體、可用性設定組或可用性區域。 您需要停用後再啟用複寫，以變更可用性類型。
    >

4. 若要自訂複寫原則設定，請按一下 [複寫原則] 旁的 [自訂]，並根據需求修改下列設定：

    - **複寫原則名稱**：原則名稱。
    - **復原點保留**：Site Recovery 預設會保留復原點 24 小時。 您可以設定介於 1 與 72 小時之間的值。
    - **應用程式一致的快照頻率**：Site Recovery 預設會每隔 4 小時建立一份應用程式一致快照集。 您可以設定介於 1 與 12 小時之間的任何值。 應用程式一致快照集是 VM 內應用程式資料的時間點快照集。 磁碟區陰影複製服務 (VSS) 可確保在建立快照集時，VM 上的應用程式處於一致狀態。
    - **複寫群組**：如果您的應用程式需要 VM 之間的多部 VM 具有一致性，您可以為這些 VM 建立複寫群組。 根據預設，選取的 VM 不屬於任何複寫群組。

5. 如果您想要將 VM 新增至新的或現有的複寫群組，請在 [自訂] 中的多部 VM 一致性上選取 [是]。 然後按一下 [確定] 。

    - 在容錯移轉時，複寫群組中的所有機器都會共用當機時保持一致和應用程式一致復原點。 啟用多部 VM 一致性可能會影響工作負載的效能 (因為這需要使用大量 CPU)，應該只用於機器正在執行相同工作負載，且您需要多部機器之間具有一致性的情況。
    - 您最多可選擇在一個複寫群組中設置 16 個虛擬機器。
    - 如果您啟用多部 VM 一致性，則複寫群組中的機器會透過連接埠 20004 彼此通訊。 請確定並沒有防火牆應用裝置封鎖了 VM 之間透過連接埠 20004 進行的內部通訊。 如果您想要讓 Linux VM 成為複寫群組的一部分，請確定已根據特定 Linux 版本的指引手動開啟連接埠 20004上 的輸出流量。

### <a name="configure-encryption-settings"></a>加密設定

如果來源虛擬機器已啟用 Azure 磁碟加密 (ADE)，則會出現加密設定：

1. 檢閱加密設定。
    - **磁碟加密金鑰保存庫**：依預設，Azure Site Recovery 會根據來源 VM 磁碟加密金鑰，在目標區域中建立名稱尾端有 "asr" 的新金鑰保存庫。 如果 Azure Site Recovery 建立的金鑰保存庫已經存在，則會重複使用。
    - **金鑰加密金鑰保存庫**：依預設，Azure Site Recovery 會根據來源 VM 金鑰加密金鑰，在目標區域中建立名稱尾端有 "asr" 的新金鑰保存庫。 如果 Azure Site Recovery 建立的金鑰保存庫已經存在，則會重複使用。

2. 按一下 [自訂] 以選取自訂金鑰保存庫。

>[!NOTE]
>Azure Site Recovery 目前僅支援執行 Windows 作業系統及[可以使用 Azure AD 應用程式進行加密](https://aka.ms/ade-aad-app)的 Azure VM。
>

### <a name="track-replication-status"></a>追蹤複寫狀態

1. 在 [設定] 中，按一下 [重新整理] 以取得最新狀態。
2. 追蹤進度和狀態如下所示：
    - 在 [設定]  > [作業] > [Site Recovery 作業] 中，追蹤 [啟用保護] 作業的進度。
    - 在 [設定] > [複寫的項目] 中，您可以檢視 VM 的狀態和初始複寫進度。 按一下 VM 可向下鑽研其設定。

## <a name="next-steps"></a>後續步驟

您已在本教學課程中設定 Azure VM 的災害復原。 現在您可以起始災害復原演練，以確認容錯移轉是否如預期般運作。

> [!div class="nextstepaction"]
> [執行災害復原演練](azure-to-azure-tutorial-dr-drill.md)
