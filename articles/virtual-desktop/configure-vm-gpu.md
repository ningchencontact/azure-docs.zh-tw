---
title: 設定 Windows 虛擬桌面 Preview-Azure GPU
description: 如何啟用 GPU 加速轉譯和 Windows 虛擬桌面預覽中的編碼。
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: a6a67c89253a1b16f9266d7917655d1b1104022e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "65159566"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop-preview"></a>設定圖形處理器 (gpu) 加速 Windows 虛擬桌面預覽

Windows 虛擬桌面的預覽支援 GPU 加速轉譯，以及改善的應用程式效能和延展性的編碼方式。 GPU 加速功能，請務必特別針對使用大量圖形的應用程式。

依照本文，以建立 Azure 虛擬機器 GPU 最佳化、 將它新增至您的主應用程式集區，並將它設定要用於轉譯和編碼方式的 GPU 加速功能。 本文假設您已經有設定 Windows 虛擬桌面租用戶。

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>選取 GPU 最佳化 Azure 虛擬機器大小

Azure 提供多種[GPU 最佳化的虛擬機器大小](/azure/virtual-machines/windows/sizes-gpu)。 最適合您的主應用程式集區取決於許多因素，包括您的特定應用程式工作負載，想要的使用者體驗及成本的品質。 一般情況下，更大且功能更強大的 Gpu 可提供更好的使用者體驗，以指定的使用者密度。

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>建立主應用程式集區、 佈建您的虛擬機器，並設定應用程式群組

建立新的主應用程式集區，使用您所選取的大小的 VM。 如需指示，請參閱[教學課程：使用 Azure Marketplace 中建立主應用程式集區](/azure/virtual-desktop/create-host-pools-azure-marketplace)。

Windows 虛擬桌面的預覽支援 GPU 加速轉譯，並在下列作業系統中的編碼：

* Windows 10 1511 版或更新版本
* Windows Server 2016 或更新版本

您也必須設定應用程式群組，或使用預設的桌面應用程式群組 （名為 「 桌面應用程式群組 」），所自動建立，當您建立新的主應用程式集區。 如需指示，請參閱[教學課程：管理 Windows 虛擬桌面預覽應用程式群組](/azure/virtual-desktop/manage-app-groups)。

>[!NOTE]
>Windows 虛擬桌面預覽僅支援已啟用 GPU 功能的主應用程式集區的 「 桌面 」 應用程式群組類型。 已啟用 GPU 功能的主應用程式集區不支援的型別 「 RemoteApp 」 的應用程式群組。

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>在您的虛擬機器中安裝支援的圖形驅動程式

若要利用 Azure N 系列 Vm 的 GPU 功能的 Windows 虛擬桌面預覽中，您必須安裝 NVIDIA 圖形驅動程式。 請遵循指示[在執行 Windows 的 N 系列 Vm 上安裝 NVIDIA GPU 驅動程式](/azure/virtual-machines/windows/n-series-driver-setup)安裝驅動程式，請以手動方式或使用[NVIDIA GPU 驅動程式延伸模組](/azure/virtual-machines/extensions/hpccompute-gpu-windows)。

請注意，只有[NVIDIA GRID 驅動程式](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers)發佈的 Azure Windows 虛擬桌面的預覽支援。

驅動程式安裝之後，VM 重新啟動是必要的。 使用上述指示中的驗證步驟，確認圖形驅動程式已成功安裝。

## <a name="configure-gpu-accelerated-app-rendering"></a>設定 GPU 加速應用程式轉譯

根據預設，應用程式和桌面同時執行多工作階段設定中會轉譯，而 CPU，並不會利用可用的 Gpu，讓呈現。 設定工作階段主機啟用 GPU 加速轉譯的群組原則：

1. 連線至 VM，使用具有本機系統管理員權限的帳戶的桌面。
2. 開啟 [開始] 功能表，然後輸入 「 gpedit.msc"以開啟 [群組原則編輯器]。
3. 瀏覽至樹狀**電腦組態** > **系統管理範本** > **Windows 元件** >  **遠端桌面服務** > **遠端桌面工作階段主機** > **遠端工作階段環境**。
4. 選取原則**用於所有的遠端桌面服務工作階段中的硬體預設圖形卡**並將此原則設定為**已啟用**啟用遠端工作階段中的 GPU 轉譯。

## <a name="configure-gpu-accelerated-frame-encoding"></a>設定 GPU 加速的框架編碼

遠端桌面進行遠端桌面用戶端的傳輸編碼 （不論是轉譯與 GPU 或 CPU），由應用程式和桌面轉譯所有圖形。 根據預設，遠端桌面不會針對這種編碼方式利用可用的 Gpu。 設定群組原則啟用 GPU 加速的框架編碼工作階段主機。 繼續上述的步驟：

1. 選取原則**遠端桌面連線排列優先順序 H.264/AVC 444 圖形模式**並將此原則設定為**已啟用**遠端工作階段中強制 H.264/AVC 444 轉碼器。
2. 選取原則**遠端桌面連線的編碼方式的設定 H.264/AVC 硬體**並將此原則設定為**已啟用**啟用硬體編碼 AVC/H.264 遠端工作階段中。

    >[!NOTE]
    >在 Windows Server 2016 中，設定選項**偏好 AVC 硬體編碼**要**永遠嘗試**。

3. 既然已編輯的群組原則，強制執行群組原則更新。 開啟命令提示字元並輸入：

    ```batch
    gpupdate.exe /force
    ```

4. 登出遠端桌面工作階段。

## <a name="verify-gpu-accelerated-app-rendering"></a>確認 GPU 加速應用程式轉譯

若要確認應用程式正在使用 GPU 來轉譯，請嘗試下列其中一項：

* 使用`nvidia-smi`公用程式中所述[確認驅動程式安裝](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation)執行您的應用程式時檢查 GPU 使用率。
* 在支援的作業系統版本上，您可以使用 工作管理員檢查 GPU 使用率。 若要查看應用程式是否使用 GPU 的 [效能] 索引標籤中選取 GPU。

## <a name="verify-gpu-accelerated-frame-encoding"></a>確認 GPU 加速的畫面格的編碼方式

若要確認 GPU 加速編碼使用遠端桌面：

1. 連線至 VM 使用 Windows 的虛擬桌面用戶端的桌面。
2. 啟動事件檢視器，並瀏覽至下列節點：**Applications and Services Logs** > **Microsoft** > **Windows** > **RemoteDesktopServices RdpCoreTS**  > **操作**
3. 若要判斷是否會使用 GPU 加速編碼，請尋找事件識別碼 170。 如果您看到 「 已啟用的 AVC 硬體編碼器：會使用 1 」 則 GPU 的編碼方式。
4. 若要判斷使用 AVC 444 模式是，尋找事件識別碼 162。 如果您看到 「 AVC 可用：1 個初始設定檔：會使用 2048 「 然後 AVC 444。

## <a name="next-steps"></a>後續步驟

這些指示應該有您啟動並執行的單一工作階段主機 VM 上的 GPU 加速功能。 在較大的主應用程式集區啟用 GPU 加速功能的一些其他考量：

* 請考慮使用[NVIDIA GPU 驅動程式延伸模組](/azure/virtual-machines/extensions/hpccompute-gpu-windows)簡化跨數個 Vm 的驅動程式安裝和更新。
* 請考慮使用 Active Directory 群組原則來簡化跨數個 Vm 的群組原則設定。 如需部署 Active Directory 網域中的群組原則的相關資訊，請參閱[使用 群組原則物件](https://go.microsoft.com/fwlink/p/?LinkId=620889)。
