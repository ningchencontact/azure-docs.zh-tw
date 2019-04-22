---
title: 使用 Azure Cloud Shell 視窗 | Microsoft Docs
description: 如何使用 Azure Cloud Shell 視窗的概觀。
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: damaerte
ms.openlocfilehash: 2511f2c8fb706e232cde9ee4c02c7f8114bd3a2b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2019
ms.locfileid: "59699214"
---
# <a name="using-the-azure-cloud-shell-window"></a>使用 Azure Cloud Shell 視窗

本文件說明如何使用 Cloud Shell 視窗。

## <a name="swap-between-bash-and-powershell-environments"></a>在 Bash 與 PowerShell 環境之間交換

使用 Cloud Shell 工具列中的環境選取器，在 Bash 與 PowerShell 環境之間交換。  
![選取環境](media/using-the-shell-window/env-selector.png)

## <a name="restart-cloud-shell"></a>重新啟動 Cloud Shell
按一下 Cloud Shell 工具列中的重新啟動圖示，即可重設電腦狀態。  
![重新啟動 Cloud Shell](media/using-the-shell-window/restart.png)
> [!WARNING]
> 重新啟動 Cloud Shell 將會重設電腦狀態，而所有未由 Azure 檔案共用保存的檔案都將遺失。

## <a name="change-the-text-size"></a>變更文字大小
按一下視窗左上方的設定圖示，並將滑鼠游標移至 [文字大小] 選項上方，然後選取想要的文字大小。 您的選擇將會跨工作階段保存。
![文字大小](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>變更字型
按一下頂端的 [設定] 圖示方視窗中，將滑鼠停留的 「 字型 」 選項，然後選取您想要的字型。  您的選擇將會跨工作階段保存。
![Font](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>上傳及下載檔案
按一下頂端的 上傳/下載檔案 圖示方視窗中，然後選取 上傳或下載。  
![上傳/下載檔案](media/using-the-shell-window/uploaddownload.png)
* 上傳檔案，使用快顯視窗中瀏覽至您的本機電腦上的檔案，選取所需的檔案，然後按一下 [開啟] 按鈕。  檔案將會上傳到`/home/user`目錄。
* 下載檔案，在快顯視窗中，輸入完整的檔案路徑，並選取 [下載] 按鈕。  
> [!NOTE] 
> 檔案和檔案路徑會區分大小寫在 Cloud Shell 中。 再次檢查您的大小寫，在您的檔案路徑。

## <a name="open-another-cloud-shell-window"></a>開啟另一個的 Cloud Shell 視窗
Cloud Shell 跨瀏覽器索引標籤可讓多個並行工作階段，允許以不同的處理序形式存在的每個工作階段。
如果要結束工作階段，請務必從每個工作階段視窗結束，因為每個處理序雖然是在相同的電腦上執行，但卻是獨立執行的。  
按一下開啟新的工作階段圖示，在頂端方視窗。 新的索引標籤會開啟另一個工作階段連線到現有的容器。
![開啟新的工作階段](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Cloud Shell 編輯器
* 請參閱[使用 Azure Cloud Shell 編輯器](using-cloud-shell-editor.md)頁面。

## <a name="web-preview"></a>Web 預覽
按一下頂端的 [web 預覽] 圖示保留的視窗，選取 [設定] 中，指定要開啟所需的連接埠。  選取其中一個 「 開啟連接埠 」，只會開啟連接埠，或 「 開啟並瀏覽 」 來開啟連接埠，以及預覽新的索引標籤中的連接埠。  
![Web 預覽](media/using-the-shell-window/preview.png)  
<br>
![設定連接埠](media/using-the-shell-window/preview-configure.png)  
按一下頂端的 [web 預覽] 圖示方視窗中，選取 「...預覽連接埠 」若要預覽新的索引標籤中開啟的連接埠。按一下頂端的 [web 預覽] 圖示方視窗中，選取 「...關閉連接埠 」若要關閉開啟的連接埠。  
![預覽/關閉連接埠](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>將 Cloud Shell 視窗最大化和最小化
按一下視窗右上方的 [最小化] 圖示，即可將視窗隱藏。 再按一下 [Cloud Shell] 圖示，即可取消隱藏。
按一下 [最大化] 圖示，即可將視窗設成最大高度。 若要將視窗還原到先前的大小，請按一下 [還原]。  
![最小化或最大化視窗](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>複製和貼上
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>調整 Cloud Shell 視窗大小
按一下工具列上邊緣並向上或向下拖曳，即可調整 Cloud Shell 視窗大小。

## <a name="scrolling-text-display"></a>捲動文字顯示
使用您的滑鼠或觸控板來捲動終端機文字。

## <a name="exit-command"></a>結束命令
執行 `exit` 可終止作用中的工作階段。 此行為預設會在 20 分鐘後發生，不須進行互動。

## <a name="next-steps"></a>後續步驟

[Cloud Shell 中 Bash 的快速入門](quickstart.md) <br>
[Cloud Shell 中 PowerShell 的快速入門](quickstart-powershell.md)