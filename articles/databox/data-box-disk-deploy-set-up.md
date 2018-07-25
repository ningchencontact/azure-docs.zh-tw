---
title: 設定 Microsoft Azure 資料箱磁碟 | Microsoft Docs
description: 使用本教學課程以了解如何設定您的 Azure 資料箱磁碟
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/10/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 7f382e3b6e70aadf8c6a090a3d5c049f6b5c0337
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010356"
---
# <a name="tutorial-unpack-connect-and-unlock-azure-data-box-disk"></a>教學課程：針對 Azure 資料箱磁碟打開包裝、連線然後解除鎖定

本教學課程說明如何針對 Azure 資料箱磁碟打開包裝、連線然後解除鎖定。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 打開資料箱磁碟的包裝
> * 連接資料箱磁碟並解除鎖定。

## <a name="prerequisites"></a>必要條件

在您開始前，請確定：

1. 您已完成[教學課程：訂購 Azure 資料箱磁碟](data-box-disk-deploy-ordered.md)。
2. 您已收到磁碟，入口網站中的作業狀態會更新為**已交付**。
3. 您有主機電腦，可以在上面安裝資料箱磁碟解除鎖定工具。 您的主機電腦必須符合下列條件：
    - 執行[支援的作業系統](data-box-disk-system-requirements.md)。
    - 已[安裝 Windows PowerShell 4](https://www.microsoft.com/download/details.aspx?id=40855)。
    - 已[安裝 .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)。

## <a name="unpack-your-disks"></a>打開磁碟的包裝

 執行下列步驟以打開磁碟的包裝。

1. 資料箱磁碟是以小型包裹寄出。 請開啟包裹然後取出內容物。 檢查包裹裡每個磁碟是否有 1 到 5 個固態硬碟 (SSD) 和 USB 連接線。 檢查包裹是否有任何竄改的證據，或其他任何明顯的損毀。 

    ![資料箱磁碟寄送包裹](media/data-box-disk-deploy-set-up/data-box-disk-ship-package1.png)

2. 如果包裹已遭竄改或嚴重損毀，請勿開啟包裹。 請連絡 Microsoft 支援服務以協助您評估磁碟是否可正常運作，以及是否需要寄送替代品給您。
3. 確認包裹有明顯外盒，其中包含用來退貨的出貨標籤 (在目前的標籤底下)。 如果此標籤遺失或損毀，您隨時都可以從 Azure 入口網站下載並列印新標籤。 

    ![資料箱磁碟出貨標籤](media/data-box-disk-deploy-set-up/data-box-disk-package-ship-label.png)

4. 請保留包裹及包裝發泡材料，以便將磁碟退回。

## <a name="connect-and-unlock-your-disks"></a>連接磁碟並解除鎖定

請執行下列步驟，以連接磁碟並解除鎖定。

1. 使用內含的纜線將磁碟連接到執行支援作業系統的 Windows 電腦，如同必要條件中所述。 

    ![資料箱磁碟連線](media/data-box-disk-deploy-set-up/data-box-disk-connect-unlock.png)    
    
2. 在 Azure 入口網站中，移至 [一般] > [裝置詳細資料]。 
3. 按一下 [下載資料箱磁碟解除鎖定工具]。 

    ![資料箱磁碟解除鎖定工具下載](media/data-box-disk-deploy-set-up/data-box-disk-unload1.png)     

4. 在您用來複製資料的相同電腦上將工具解壓縮。
5. 在相同電腦上以系統管理員的身分，開啟 [命令提示字元] 視窗或執行 Windows PowerShell。
6. (選擇性) 若要確認您用來將磁碟解除鎖定的電腦符合作業系統需求，請執行系統檢查命令。 下方顯示一項範例輸出。 

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /SystemCheck
    Successfully verified that the system can run the tool.
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ``` 

7. 在 Azure 入口網站中，移至 [一般] > [裝置詳細資料]。 使用複製圖示來複製通行金鑰。
8. 請執行 `DataBoxDiskUnlock.exe` 並提供通行金鑰。 指派給磁碟的磁碟機代號隨即顯示。 下方顯示一項範例輸出。

    ```powershell
    PS C:\WINDOWS\system32> cd C:\DataBoxDiskUnlockTool\DiskUnlock
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe
    Enter the passkeys (format: passkey1;passkey2;passkey3):
    testpasskey1
    
    Following volumes are unlocked and verified.
    Volume drive letters: D:
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```

9. 針對日後任何磁碟重新插入請重複步驟 6-8。 如果您需要資料箱磁碟解除鎖定工具的說明，請使說明命令。   

    ```powershell
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /help
    USAGE:
    DataBoxUnlock /PassKeys:<passkey_list_separated_by_semicolon>
    
    Example: DataBoxUnlock /PassKeys:<your passkey>
    Example: DataBoxUnlock /SystemCheck
    Example: DataBoxUnlock /Help
    
    /PassKeys:       Get this passkey from Azure DataBox Disk order. The passkey unlocks your disks.
    /SystemCheck:    This option checks if your system meets the requirements to run the tool.
    /Help:           This option provides help on cmdlet usage and examples.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```  
10. 將磁碟解除鎖定之後，您就可以檢視磁碟的內容。    

    ![資料箱磁碟內容](media/data-box-disk-deploy-set-up/data-box-disk-content.png) 

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解 Azure 資料箱磁碟的相關主題，像是：

> [!div class="checklist"]
> * 打開資料箱磁碟的包裝
> * 連接資料箱磁碟並解除鎖定


請前進到下一個教學課程，以了解如何複製資料箱磁碟上的資料。

> [!div class="nextstepaction"]
> [複製資料箱磁碟上的資料](./data-box-disk-deploy-copy-data.md)

