---
title: 連接和管理 Microsoft Azure 資料方塊閘道裝置的 Windows PowerShell 介面透過 |Microsoft Docs
description: 描述如何連接到與 Windows PowerShell 介面透過管理方塊部署資料閘道。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/21/2019
ms.author: alkohli
ms.openlocfilehash: 67caaa2c6c9bd615d0b88bdd5de3442b46aa32cb
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403488"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>管理透過 Windows PowerShell 在 Azure 中部署資料閘道裝置

Azure 中部署資料閘道解決方案，可讓您透過網路傳送資料至 Azure。 本文說明一些您的資料方塊閘道裝置的設定和管理工作。 您可以使用 Azure 入口網站中，本機 web UI 或 Windows PowerShell 介面來管理您的裝置。

這篇文章著重於您使用的 PowerShell 介面執行工作。

這篇文章包含下列程序：

- 連線到 PowerShell 介面
- 啟動支援工作階段
- 建立支援封裝
- Upload certificate
- 在非 DHCP 環境中啟動
- 檢視裝置資訊

## <a name="connect-to-the-powershell-interface"></a>連線到 PowerShell 介面

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>建立支援封裝

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Upload certificate

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>在非 DHCP 環境中啟動

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>檢視裝置資訊

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>後續步驟

- 在 Azure 入口網站中部署 [Azure 資料箱閘道](data-box-gateway-deploy-prep.md)。
