---
title: Azure 中 Linux 虛擬機器的開機診斷 | Microsoft Docs
description: Azure 中 Linux 虛擬機器的兩個偵錯功能概觀
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: Deland-Han
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: delhan
ms.openlocfilehash: 38cc806cb77af60cda10f3aeac2e5ed13b445b8c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
ms.locfileid: "33941848"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-linux-virtual-machines-in-azure"></a>如何使用開機診斷為 Azure 中的 Linux 虛擬機器進行疑難排解

Azure 現在提供兩個偵錯功能的支援︰Azure 虛擬機器 Resource Manager 部署模型的主控台輸出和螢幕擷取畫面支援。 

將自己的映像送至 Azure 或甚至啟動其中一個平台映像時，虛擬機器進入不可開機狀態的原因有很多。 這些功能可讓您輕鬆地診斷及復原開機失敗的虛擬機器。

若為 Linux 虛擬機器，您可以從入口網站輕鬆地檢視主控台記錄的輸出︰

![Azure 入口網站](./media/boot-diagnostics/screenshot1.png)
 
不過，若為 Windows 和 Linux 虛擬機器，Azure 也可讓您從 Hypervisor 查看 VM 的螢幕擷取畫面︰

![Error](./media/boot-diagnostics/screenshot2.png)

所有區域中的 Azure 虛擬機器都支援這兩項功能。 請注意，螢幕擷取畫面和輸出最多可能需要 10 分鐘的時間才會出現在您的儲存體帳戶中。

## <a name="common-boot-errors"></a>常見的開機錯誤

- [檔案系統問題](https://support.microsoft.com/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) 
- [核心問題](https://support.microsoft.com/help/4091524/how-recovery-azure-linux-vm-from-kernel-related-boot-related-issues/) 
- [FSTAB 錯誤](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors)

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>在新的虛擬機器上啟用診斷
1. 在 Azure 入口網站建立新的虛擬機器時，請在部署模型下拉式清單中選取 [Azure Resource Manager]︰
 
    ![Resource Manager](./media/boot-diagnostics/screenshot3.jpg)

2. 在 [設定] 中啟用 [開機診斷]，然後選取要放置這些診斷檔案的儲存體帳戶。
 
    ![建立 VM](./media/boot-diagnostics/create-storage-account.png)

    > [!NOTE]
    > 開機診斷功能不支援進階儲存體帳戶。 如果您使用進階儲存體帳戶來進行開機診斷，可能會在啟動虛擬機器時收到 StorageAccountTypeNotSupported 錯誤。 
    >
    > 

3. 如果您正以 Azure Resource Manager 範本進行部署，請巡覽至您的虛擬機器的資源並附加診斷設定檔區段。 請記得使用 “2015-06-15” API 版本標頭。

    ```json
    {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          … 
    ```

4. 診斷設定檔可讓您選取想要放置這些記錄的儲存體帳戶。

    ```json
            "diagnosticsProfile": {
                "bootDiagnostics": {
                "enabled": true,
                "storageUri": "[concat('http://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
                }
            }
            }
        }
    ```

若要在已啟用開機診斷的情況下部署範例虛擬機器，請至此查看我們的存放庫。

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>在現有的虛擬機器上啟用開機診斷 

若要在現有的虛擬機器上啟用開機診斷功能，請遵循下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後選取虛擬機器。
2. 在 [支援 + 疑難排解] 中，選取 [開機診斷]  > [設定]，將狀態變更為 [開啟]，然後選取儲存體帳戶。 
4. 確認 [開機診斷] 選項已選取，然後儲存變更。

    ![更新現有的 VM](./media/boot-diagnostics/enable-for-existing-vm.png)

3. 重新啟動 VM 才會生效。

## <a name="next-steps"></a>後續步驟

如果在使用「虛擬機器開機診斷」時看見「無法取得記錄內容」錯誤，請參閱[無法在虛擬機器開機診斷中取得記錄錯誤內容](https://support.microsoft.com/help/4094480/failed-to-get-contents-of-the-log-error-in-vm-boot-diagnostics-in-azur)。