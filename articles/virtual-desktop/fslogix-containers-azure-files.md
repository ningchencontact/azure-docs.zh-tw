---
title: FSLogix 設定檔的容器和 Windows 虛擬桌面-Azure 中的 Azure 檔案
description: 本文說明 Windows 虛擬桌面和 Azure 檔案內的容器，FSLogix 設定檔。
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: v-chjenk
ms.openlocfilehash: b3032aa796b3c79572bbf8b2beb85efc252ff73b
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497527"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>FSLogix 設定檔容器和 Azure 檔案

Windows 虛擬桌面預覽服務做為使用者設定檔解決方案建議 FSLogix 設定檔的容器。 FSLogix 被設計來漫遊設定檔，在遠端計算環境的詳細資訊，例如 Windows 虛擬桌面。 它會完成的使用者設定檔儲存在單一容器中。 在登入時，此容器會以動態方式會附加至使用原生支援虛擬硬碟 (VHD) 和 HYPER-V 虛擬硬碟磁碟 (VHDX) 的運算環境。 使用者設定檔就可立即使用，並出現在系統完全相同的原生的使用者設定檔。

在本文中，我們將說明 FSLogix 搭配 Azure 檔案的設定檔容器。 中的 Windows 虛擬桌面，也就是內容的資訊是[宣布在 3/21](https://www.microsoft.com/microsoft-365/blog/2019/03/21/windows-virtual-desktop-public-preview/)。

## <a name="user-profiles"></a>使用者設定檔

使用者設定檔包含有關個人，包括桌面設定、 永久網路連線等應用程式設定的組態資訊的資料項目。 根據預設，Windows 會建立本機使用者設定檔與作業系統緊密整合。

遠端使用者設定檔會提供使用者資料和作業系統之間的資料分割。 它可讓作業系統來取代或變更，而不會影響使用者資料。 在遠端桌面工作階段主機 (RDSH) 和虛擬桌面基礎結構 (VDI) 中，作業系統可能會被取代，原因如下：

- 作業系統升級
- 取代的現有虛擬機器 (VM)
- 使用者正在共用 （非持續） RDSH 或 VDI 環境的一部分

針對遠端使用者設定檔，包括這些技術的數種技術操作 Microsoft 產品：
- 漫遊使用者設定檔 (RUP)
- 使用者設定檔磁碟 (UPD)
- 企業狀態漫遊 (ESR)

UPD RUP 主機且最廣泛使用的技術，在遠端桌面工作階段主機 (RDSH) 和虛擬硬碟 (VHD) 環境中的使用者設定檔。

### <a name="challenges-with-previous-user-profile-technologies"></a>與先前的使用者設定檔技術的挑戰

使用者設定檔的現有與舊版 Microsoft 解決方案隨附於各種挑戰。 沒有先前的解決方案處理隨附的 RDSH 或 VDI 環境的所有使用者設定檔需求。 比方說，UPD 無法處理大型的 OST 檔案，而 RUP 不會保存最新的設定。

#### <a name="functionality"></a>功能

下表顯示優點和限制的前一個使用者設定檔的技術。

| Technology | 現代的設定 | Win32 設定 | OS 設定 | 使用者資料 | 伺服器 SKU 上支援 | 在 Azure 上的後端儲存體 | 後端儲存體內部 | 版本支援 | 後續登入時間 |注意|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **使用者設定檔磁碟 (UPD)** | 是 | 是 | 是 | 是 | 是 | 否 | 是 | 贏得 7 + | 是 | |
| **漫遊使用者設定檔 (RUP)，維護模式** | 否 | yes | 是 | 是 | 是| 否 | 是 | 贏得 7 + | 否 | |
| **企業狀態漫遊 (ESR)** | 是 | 否 | 是 | 否 | 請參閱附註 | 是 | 否 | Win 10 | 否 | 在 伺服器 SKU，但不支援的使用者介面的函式 |
| **User Experience Virtualization (UE-V)** | 是 | 是 | 是 | 否 | 是 | 否 | 是 | 贏得 7 + | 否 |  |
| **OneDrive 雲端檔案** | 否 | 否 | 否 | 是 | 請參閱附註 | 請參閱附註  | 請參閱附註 | Win 10 RS3 | 否 | 不在伺服器 SKU 上測試。 在 Azure 上的後端儲存體同步處理用戶端而定。 後端儲存體內部必須同步處理用戶端。 |

#### <a name="performance"></a>效能

需要 UPD[儲存空間直接存取 (S2D)](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment)位址效能需求。 UPD 使用伺服器訊息區塊 (SMB) 通訊協定。 它會複製設定檔中記錄使用者的 vm。 具有 S2D 的 UPD 是 RDS 小組的服務在預覽期間建議適用於 Windows 的虛擬桌面的解決方案。  

#### <a name="cost"></a>成本

雖然 S2D 叢集達到所需的效能，成本會很耗費資源，適用於企業客戶，但特別是昂貴的小型和中型企業 (SMB) 客戶。 此解決方案中，企業需支付進階儲存體磁碟，以及使用共用的磁碟 Vm 的費用。

#### <a name="administrative-overhead"></a>系統管理額外負荷

S2D 的叢集需要作業系統修補、 更新，並保存在安全的狀態。 這些程序和設定 S2D 災害復原的複雜性，讓 S2D 只適用於企業專職 IT 人員。

## <a name="fslogix-profile-containers"></a>FSLogix 設定檔的容器

在 2018 年 11 月 19 日， [Microsoft 取得 FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/)。 FSLogix 解決許多的設定檔容器挑戰。 在它們之間的索引鍵是：

- **效能：** [FSLogix 設定檔容器](https://fslogix.com/products/profile-containers)是高效能，並解決效能問題在過去已封鎖的快取 exchange 模式。
- **OneDrive:** 不含 FSLogix 設定檔的容器，在非持續性 RDSH 或 VDI 環境中不支援商務用 OneDrive。 [OneDrive 商務和 FSLogix 最佳作法](https://fslogix.com/products/technical-faqs/284-onedrive-for-business-and-fslogix-best-practices)描述其互動方式。 如需詳細資訊，請參閱 <<c0> [ 使用同步用戶端上的虛擬桌面](https://docs.microsoft.com/deployoffice/rds-onedrive-business-vdi)。
- **其他的資料夾：** FSLogix 提供功能來擴充使用者設定檔，以包含其他資料夾。

之後取得，Microsoft 會啟動現有的使用者設定檔解決方案，例如 UPD，取代 FSLogix 設定檔的容器。

## <a name="azure-files-integration-with-azure-active-directory"></a>Azure 檔案與 Azure Active Directory 的整合

FSLogix 分析容器效能和功能會利用雲端。 在 2018 年 24 日，Microsoft Azure 檔案宣布的公開預覽[支援 Azure Active Directory 驗證的 Azure 檔案](https://azure.microsoft.com/blog/azure-active-directory-integration-for-smb-access-now-in-public-preview/)。 透過處理成本和管理負擔，使用 Azure Active Directory 驗證的 Azure 檔案是在新的 Windows 虛擬桌面服務的使用者設定檔的進階方案。

## <a name="best-practices-for-windows-virtual-desktop"></a>適用於 Windows 的虛擬桌面的最佳作法

Windows 虛擬桌面提供完整控制大小、 類型和客戶使用的 Vm 數目。 如需詳細資訊，請參閱 <<c0> [ 什麼是 Windows 虛擬桌面預覽？](https://docs.microsoft.com/azure/virtual-desktop/overview)。

若要確保您的 Windows 虛擬桌面環境，請遵循最佳做法：

- Azure 檔案儲存體帳戶必須位於與工作階段主機 Vm 相同的區域。
- Azure 檔案權限應符合所述的權限[需求-設定檔的容器](https://docs.fslogix.com/display/20170529/Requirements+-+Profile+Containers)。
- 每個主應用程式集區必須建置相同的型別和相同的主要映像為基礎的 VM 大小。
- 每個主應用程式集區 VM 必須位於相同的資源群組，以協助管理、 調整及更新。
- 為了達到最佳效能，儲存體解決方案和 FSLogix 設定檔的容器應該位於相同的資料中心位置。
- 包含主要映像的儲存體帳戶必須位於相同的區域和訂用帳戶，佈建 Vm。

## <a name="next-steps"></a>後續步驟

您可以使用下列指示來設定 Windows 虛擬桌面環境。

- 若要開始建置您的桌面虛擬化解決方案，請參閱[建立租用戶中 Windows 虛擬桌面](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory)。
- 若要建立 Windows 虛擬桌面租用戶內的主應用程式集區，請參閱[建立 Azure Marketplace 的主應用程式集區](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace)。
- 若要設定完全受控的檔案共用在雲端中，請參閱 <<c0> [ 設定 Azure 檔案共用](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-enable)。
- 若要設定 FSLogix 設定檔的容器，請參閱[設定主應用程式集區的使用者設定檔共用](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-user-profile)。
- 若要指派使用者給主應用程式集區，請參閱[管理 Windows 虛擬桌面應用程式群組](https://docs.microsoft.com/azure/virtual-desktop/manage-app-groups)。
- 若要從網頁瀏覽器存取您的 Windows 虛擬桌面資源，請參閱[連接到 Windows 虛擬桌面](https://docs.microsoft.com/azure/virtual-desktop/connect-web)。
