---
title: 連接和管理 Microsoft Azure 資料方塊邊緣裝置的 Windows PowerShell 介面透過 |Microsoft Docs
description: 描述如何連接到與 Windows PowerShell 介面透過管理資料方塊的邊緣。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/21/2019
ms.author: alkohli
ms.openlocfilehash: 9b0e94deda205497cda4ebf383f302c6c3bb896a
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403590"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>管理透過 Windows PowerShell 的 Azure 資料方塊的邊緣裝置

Azure 資料方塊邊緣解決方案可讓您處理資料，並透過網路傳送至 Azure。 本文說明一些您的資料方塊的邊緣裝置的設定和管理工作。 您可以使用 Azure 入口網站中，本機 web UI 或 Windows PowerShell 介面來管理您的裝置。

這篇文章著重於您使用的 PowerShell 介面執行工作。

這篇文章包含下列程序：

- 連線到 PowerShell 介面
- 啟動支援工作階段
- 建立支援封裝
- Upload certificate
- 將裝置重設
- 檢視裝置資訊
- 取得計算記錄檔
- 監視和疑難排解計算模組

## <a name="connect-to-the-powershell-interface"></a>連線到 PowerShell 介面

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>建立支援封裝

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Upload certificate

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="view-device-information"></a>檢視裝置資訊
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>重設您的裝置

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>取得計算記錄檔

如果您的裝置上設定 「 計算 」 角色，您也可以透過 PowerShell 介面來取得計算記錄檔。

1. [連線到 PowerShell 介面](#connect-to-the-powershell-interface)。
2. 使用`Get-AzureDataBoxEdgeComputeRoleLogs`計算記錄檔取得您的裝置。

    下列範例會示範這個指令程式的使用方式：

    ```
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username/password" -RoleInstanceName "IotRole" -FullLogCollection
    ```
    以下是此指令程式所使用的參數的描述：
    - `Path`:提供您想要用來建立計算記錄檔封裝的共用網路路徑。
    - `Credential`:提供網路共用的使用者名稱和密碼。
    - `RoleInstanceName`:提供此字串`IotRole`此參數。
    - `FullLogCollection`:此參數可確保記錄檔封裝將包含所有計算記錄檔。 根據預設，記錄檔封裝只包含一部分的記錄檔。

## <a name="monitor-and-troubleshoot-compute-modules"></a>監視和疑難排解計算模組

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]


## <a name="next-steps"></a>後續步驟

- 在 Azure 入口網站中部署 [Azure Data Box Edge](data-box-edge-deploy-prep.md)。
