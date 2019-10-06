---
title: 在 Azure 實驗室服務的範本 VM 上啟用嵌套虛擬化 |Microsoft Docs
description: 瞭解如何在 Azure 實驗室服務的範本 VM 上啟用「嵌套虛擬化」。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 9a52fd958d646af5edd09065e9acf95796c8ce33
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981991"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>在 Azure 實驗室服務的範本虛擬機器上啟用嵌套虛擬化
本文說明如何在 Azure 實驗室服務中的範本虛擬機器上設定嵌套虛擬化。 當類別中的每個學生都需要多部電腦時，Azure 實驗室服務中會使用「嵌套虛擬化」。
 
## <a name="considerations"></a>考量
在設定具有嵌套虛擬化的實驗室之前，以下是幾個要考慮的事項。

- 建立新的實驗室時，請選取 [**中（嵌套虛擬化）** ] 或 [**大**大小] 作為虛擬機器大小。 這些虛擬機器大小支援嵌套虛擬化。 
- 選擇可為主機和用戶端虛擬機器提供良好效能的大小。  請記住，使用虛擬化時，您所選擇的大小必須足以容納一部電腦，但主機以及必須同時執行的任何用戶端電腦。
- 用戶端虛擬機器將無法存取 Azure 資源，例如 Azure 虛擬網路上的 DNS 伺服器。
- 主機虛擬機器需要安裝程式，才能讓用戶端電腦具有網際網路連線能力。 
- 用戶端虛擬機器已授權為獨立的機器。 如需 Microsoft 作業系統和產品授權的相關資訊，請參閱[Microsoft 授權](https://www.microsoft.com/licensing/default)。 在設定範本機器之前，請先檢查所使用之任何其他軟體的授權合約。

## <a name="enable-nested-virtualization-on-a-template-vm"></a>在範本 VM 上啟用嵌套虛擬化
本節中的步驟著重于設定 Windows Server 2016 或 Windows Server 2019 的嵌套虛擬化。 您將使用腳本來設定具有 Hyper-v 的範本電腦。 如需自動化解決方案，請參閱[實驗室服務 Hyper-v 腳本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)中的腳本。 下列步驟將逐步引導您瞭解如何使用腳本。

1. 如果您使用的是 Internet Explorer，您可能必須將 `https://github.com` 新增至信任的網站清單。 
    1. 開啟 Internet Explorer。
    1. 選取齒輪圖示，然後選擇 [**網際網路選項**]。  
    1. 當 [**網際網路選項**] 對話方塊出現時，選取 [**安全性**]，選取 [**信任的網站**]，按一下 [**網站**] 按鈕。
    1. 出現 [**信任的網站**] 對話方塊時，將 `https://github.com` 新增至 [信任的網站] 清單，然後選取 [**關閉**]。

        ![信任的網站](../media/how-to-enable-nested-virtualization-template-vm/trusted-sites-dialog.png)
1.  如下列步驟所述，下載 Git 存放庫檔案。  或者，您也可以從[https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git)複製 Git 存放庫。 
    1. 移至 [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/)。
    1. 按一下 [**複製或下載**] 按鈕。
    1. 按一下 [**下載 ZIP**]。
    1. 解壓縮 ZIP 檔案
1. 以**系統管理員**模式啟動**PowerShell** 。
1. 在 PowerShell 視窗中，流覽至含有已下載腳本的資料夾。 如果您是從存放庫檔案的最上層資料夾流覽，則腳本位於 `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\`。
1. 您可能必須變更執行原則，才能成功執行腳本。 執行下列命令：
    
    ```powershell
    Set-ExecutionPolicy bypass -force 
    ```
1. 執行腳本：
    
    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > 腳本可能需要重新開機電腦。 遵循腳本中的指示並重新執行腳本，直到輸出中出現**腳本完成**為止。
1. 別忘了重設執行原則。 執行下列命令： 

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>結論
現在，您的範本電腦已準備好建立 Hyper-v 虛擬機器。 如需如何建立 Hyper-v 虛擬機器的指示，請參閱[在 hyper-v 中建立虛擬機器](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v)。 此外，請參閱[Microsoft 評估中心](https://www.microsoft.com/evalcenter/)以查看可用的作業系統和軟體。  

## <a name="next-steps"></a>後續步驟 
請參閱下列文章：

- [如何在 Azure VM 中啟用嵌套虛擬化](../../virtual-machines/windows/nested-virtualization.md) 
- [在 Windows Server 上安裝 Hyper-v 角色](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)
- [實驗室服務 Hyper-v 腳本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)
