---
title: 如何在 Azure 中的 OPC 對應項雲端相依性的部署 |Microsoft Docs
description: 如何部署 OPC 對應項 Azure 相依性。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: ae9f2b05bfc6ea6315022d04c8d267d916cf282e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61451594"
---
# <a name="deploying-dependencies-for-local-development"></a>部署適用於本機開發的相依性

這篇文章說明如何部署僅 Azure 平台服務需要執行本機開發和偵錯。   在結束時，您必須部署資源群組，其中包含您用於本機開發及偵錯所需的一切。

## <a name="deploy-azure-platform-services"></a>部署 Azure 平台服務

1. 請確定您具有 PowerShell 及[Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-1.1.0)安裝擴充功能。  開啟命令提示字元或終端機並執行：

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. 遵循提示來指派名稱給您的部署的資源群組。  指令碼會將相依性部署到您 Azure 訂用帳戶，但不是微服務中的資源群組。  指令碼也會在 Azure Active Directory 中註冊應用程式。  這樣才能支援 OAUTH 型驗證。  部署可能需要數分鐘的時間。

3. 指令碼完成之後，您可以選取儲存.env 檔案。  .Env 環境檔案是組態檔的所有服務和您想要在您的開發電腦上執行的工具。  

## <a name="troubleshooting-deployment-failures"></a>針對部署失敗進行疑難排解

### <a name="resource-group-name"></a>資源群組名稱

請確定您使用簡短且簡單的資源群組名稱。  若要取得名稱資源因此它必須符合命名需求的資源也會使用的名稱。  

### <a name="azure-active-directory-aad-registration"></a>Azure Active Directory (AAD) 註冊

部署指令碼會嘗試註冊 Azure Active Directory 中的 AAD 應用程式。  根據您選取的 AAD 租用戶的權限，這可能會失敗。   有 3 個選項：

1. 如果您選擇的 AAD 租用戶的租用戶清單時，重新啟動指令碼，然後從清單選擇不同。
2. 或者，部署私用的 AAD 租用戶，並重新啟動指令碼，然後選取要使用它。
3. 沒有驗證的情況下繼續。  因為您在本機執行您的微服務，這是可以接受的但無法模擬生產環境。  

## <a name="next-steps"></a>後續步驟

既然您已成功部署至現有專案的 OPC 對應項服務，以下是建議的下一個步驟：

> [!div class="nextstepaction"]
> [深入了解如何部署 OPC 對應項模組](howto-opc-twin-deploy-modules.md)
