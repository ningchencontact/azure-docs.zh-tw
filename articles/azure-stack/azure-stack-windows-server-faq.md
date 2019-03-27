---
title: Azure Stack Windows Server 相關常見問題集 | Microsoft Docs
description: 適用於 Windows Server 的 Azure Stack Marketplace 常見問題集清單
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 11/12/2018
ms.openlocfilehash: b498283ee117935438c55e5162e92acdb0f5ebfe
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2019
ms.locfileid: "56649895"
---
# <a name="windows-server-in-azure-stack-marketplace-faq"></a>Azure Stack Marketplace 中的 Windows Server 常見問題集

本文能提供 [Azure Stack Marketplace](azure-stack-marketplace.md) 中 Windows Server 映像之相關常見問題集的解答。

## <a name="marketplace-items"></a>Marketplace 項目

### <a name="how-do-i-update-to-a-newer-windows-image"></a>我如何更新至較新的 Windows 映像？

首先，請判斷是否有任何 Azure Resource Manager 範本會參考特定的版本。 如果是，請更新那些範本，或保留較舊的映象版本。 最好使用 **version: latest**。

接下來，如果有任何虛擬機器擴展集會參考特定的版本，您應該思考未來會不會擴展它們，然後再決定是否要保留較舊的版本。 如果上述兩個條件都不適用，請先在 Marketplace 中刪除較舊的映象，再下載較新的版本。 如果原始的映象是使用 [Marketplace 管理] 來下載的，請繼續使用它來這麼做。 然後下載較新的版本。

### <a name="what-are-the-licensing-options-for-windows-server-marketplace-images-on-azure-stack"></a>適用於 Azure Stack 上 Windows Server Marketplace 映像的授權選項為何？

Microsoft 透過 Azure Stack Marketplace 提供兩種版本的 Windows Server 映像：

- **隨用即付**：這些映像會執行完整價格的 Windows 計量。
   適合對象：使用*使用量計費模型*的 Enterprise 合約 (EA) 客戶、不想使用 SPLA 授權的 CSP。
- **自備授權 (BYOL)**：這些映像會執行基本計量。
   適合對象：具有 Windows Server 授權的 EA 客戶、使用 SPLA 授權的 CSP。

Azure Stack 不支援 Azure Hybrid Use Benefit (AHUB)。 透過「容量」模型取得授權的客戶必須使用 BYOL 映像。 如果您正在測試使用 Azure Stack 開發套件 (ASDK)，則可以使用上述任一選項。

### <a name="what-if-i-downloaded-the-wrong-version-to-offer-my-tenantsusers"></a>我下載到錯誤的版本以提供給租用戶/使用者，該怎麼辦？

請先透過 [Marketplace 管理] 刪除錯誤的版本。 等候它完全完成 (請查看完成的通知，而非 [Marketplace 管理] 刀鋒視窗)。 然後下載正確的版本。

### <a name="what-if-my-user-incorrectly-checked-the-i-have-a-license-box-in-previous-windows-builds-and-they-dont-have-a-license"></a>我的使用者在舊版的 Windows 組建中錯誤地選取 [我有授權] 方塊，且他們沒有授權，該怎麼辦？

請參閱[將具有權益的 Windows Server VM 轉換回預付型方案](../virtual-machines/windows/hybrid-use-benefit-licensing.md#powershell-1)。

### <a name="what-if-i-have-an-older-image-and-my-user-forgot-to-check-the-i-have-a-license-box-or-we-use-our-own-images-and-we-do-have-enterprise-agreement-entitlement"></a>我有較舊的映像且我的使用者忘記選取 [我有授權] 方塊，或是我們使用自己的映像且擁有 Enterprise 合約權利，該怎麼辦？

請參閱[轉換使用適用於 Windows Server 的 Azure Hybrid Benefit 的現有 VM](../virtual-machines/windows/hybrid-use-benefit-licensing.md#convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server)。 請注意，Azure Hybrid Benefit 並不適用於 Azure Stack，但系統仍會套用此設定的效果。

### <a name="what-about-other-vms-that-use-windows-server-such-as-sql-or-machine-learning-server"></a>那使用 Windows Server 的其他 VM (例如 SQL 或機器學習服務伺服器) 呢？

這些映像會套用 **licenseType** 參數，因此它們是預付型方案。 您可以設定此參數 (請參閱先前的常見問題集解答)。 這只適用於 Windows Server 軟體，而非如 SQL 的多層式產品 (需要您自備授權)。 預付型方案授權並不適用於多層式軟體產品。

### <a name="i-have-an-enterprise-agreement-ea-and-will-be-using-my-ea-windows-server-license-how-do-i-make-sure-images-are-billed-correctly"></a>我有 Enterprise 合約 (EA) 且將會使用我的 EA Windows Server 授權，我要如何確保映像會正確計費？

您可以將 **licenseType:Windows_Server** 新增至 Azure Resource Manager 範本中。 此設定必須個別加入至每個虛擬機器資源區塊個別。

## <a name="activation"></a>啟用

若要在 Azure Stack 上啟用 Windows Server 虛擬機器，下列條件必須成立：

- OEM 已在 Azure Stack 上的每個主機系統上設定適當的 BIOS 標記。
- Windows Server 2012 R2 和 Windows Server 2016 必須使用[自動虛擬機器啟用](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11))(英文\)。 Azure Stack 不支援金鑰管理服務 (KMS) 和其他啟用服務。

### <a name="how-can-i-verify-that-my-virtual-machine-is-activated"></a>我如何確認虛擬機器已經啟用？

從提高權限的命令提示字元中執行下列命令：

```shell
slmgr /dlv
```

如果虛擬機器已正確啟用，您將能在 `slmgr` 輸出中清楚看見指出此情況的訊息與主機名稱。 請不要依賴顯示器上的浮水印，因為它們可能不是最新狀態，或是顯示位於您虛擬機器後其他虛擬機器的狀態。

### <a name="my-vm-is-not-set-up-to-use-avma-how-can-i-fix-it"></a>我的 VM 沒有設定為使用 AVMA，該如何修正它？

從提高權限的命令提示字元中執行下列命令：

```shell
slmgr /ipk <AVMA key>
```

請參閱[自動虛擬機器啟用](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) \(英文\) 一文，以取得用於您映像的金鑰。

### <a name="i-create-my-own-windows-server-images-how-can-i-make-sure-they-use-avma"></a>我是自行建立 Windows Server 映像，該如何確定它們是使用 AVMA？

建議您在執行 `sysprep` 命令之前，先搭配適當的金鑰執行 `slmgr /ipk` 命令。 或是將 AVMA 金鑰包含在任何 Unattend.exe 安裝檔案中。

### <a name="i-am-trying-to-use-my-windows-server-2016-image-created-on-azure-and-it-is-not-activating-or-using-kms-activation"></a>我嘗試使用自己在 Azure 上建立的 Windows Server 2016 映像，但它無法啟用或是正在使用 KMS 啟用。

執行 `slmgr /ipk` 命令。 Azure 映像可能會無法正確回復至 AVMA，但如果它們能連線到 Azure KMS 系統，便可以正常啟用。 建議您確定這些 VM 已設定為使用 AVMA。

### <a name="i-have-performed-all-of-these-steps-but-my-virtual-machines-are-still-not-activating"></a>我已執行上述所有步驟，但我的虛擬機器仍無法啟用。

請連絡您的硬體供應商，以確定已安裝正確的 BIOS 標記。

### <a name="what-about-earlier-versions-of-windows-server"></a>那較舊版本的 Windows Server 呢？

較舊版本的 Windows Server 不支援[自動虛擬機器啟用](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) \(英文\)。 您必須手動啟用這些 VM。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章：

- [Azure Stack Marketplace 概觀](azure-stack-marketplace.md)
- [將市集項目從 Azure 下載到 Azure Stack](azure-stack-download-azure-marketplace-item.md)
