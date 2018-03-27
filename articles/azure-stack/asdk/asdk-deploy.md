---
title: 部署 Azure Stack 開發套件 | Microsoft Docs
description: 在本教學課程中，您要使用安裝程式指令碼來安裝 ASDK。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: de31ed75b79ddb3832e32fad141ea7aef806d4d3
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-deploy-the-asdk-using-the-installer"></a>教學課程：使用安裝程式部署 ASDK
在本教學課程中，您要在非生產環境中部署 Azure Stack 開發套件 (ASDK)。 

ASDK 是種測試和部署環境，可供您部署以評估及示範 Azure Stack 的功能和服務。 若要開始使用 ASDK，您需要準備主機電腦硬體，然後再執行一些指令碼 (安裝須花費數小時)。 在那之後，您就可以登入系統管理員和使用者入口網站，開始使用 Azure Stack。

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 下載部署套件並解壓縮
> * 準備開發套件主機電腦 
> * 在主機電腦上安裝 ASDK
> * 執行部署後設定
> * 向 Azure 註冊

## <a name="prerequisites"></a>先決條件 
安裝 ASDK前，您需要準備將裝載開發套件 (開發套件主機) 的電腦。 開發套件主機電腦必須符合硬體、軟體及網路需求。 

您也需要選擇使用 Azure Active Directory (Azure AD) 或 Active Directory Federation Services (AD FS) 作為部署的識別解決方案。 

請確定開發套件主機符合最低硬體需求，且您已在開始部署之前選擇好識別解決方案，讓安裝程序可以順暢執行。 

**[檢閱 ASDK 部署規劃考量](asdk-deploy-considerations.md)**

> [!TIP]
> 安裝作業系統之後，您可以在開發套件主機電腦上使用 [Azure Stack 部署需求檢查工具](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b)，來確認您的硬體符合所有需求。

## <a name="download-and-extract-the-deployment-package"></a>下載部署套件並解壓縮
確認您的開發套件主機電腦符合安裝 ASDK 的基本需求後，下一個步驟是下載 ASDK 部署套件並解壓縮。 開發套件包括 Cloudbuilder.vhdx 檔案，這是個包含可開機作業系統和 Azure Stack 安裝檔的虛擬硬碟。

您可以將部署套件下載到開發套件主機或另一部電腦。 解壓縮後的部署檔案會佔用 60 GB 的可用磁碟空間，因此使用另一部電腦可協助降低開發套件主機的硬體需求。

**[下載 Azure Stack 開發套件 (ASDK) 並解壓縮](asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>準備開發套件主機電腦
必須先備妥環境，並將系統設定為從 VHD 開機，才可以在主機電腦上安裝 ASDK。 準備好開發套件主機電腦後，它會從 CloudBuilder.vhdx 虛擬機器硬碟開機，讓您可以開始 ASDK 部署。

**[準備 ASDK 主機電腦](asdk-prepare-host.md)**

## <a name="install-the-asdk-on-the-host-computer"></a>在主機電腦上安裝 ASDK
準備好開發套件主機電腦後，就可以將 ASDK 部署到 CloudBuilder.vhdx 映像。 要部署 ASDK，除了使用下載並執行 asdk-installer.ps1 PowerShell 指令碼所提供的圖形化使用者介面 (GUI) 以外，也可以完全從[命令列](asdk-deploy-powershell.md)進行部署。 

> [!NOTE]
> 或者，您也可以在主機電腦開機進入 CloudBuilder.vhdx 後，在安裝 ASDK 前，「先」設定 [Azure Stack 遙測設定](asdk-telemetry.md#set-telemetry-level-in-the-windows-registry)。


**[安裝 Azure Stack 開發套件 (ASDK)](asdk-install.md)**

## <a name="perform-post-deployment-configurations"></a>執行部署後設定
安裝 ASDK 後，建議您進行一些安裝後的檢查，並變更一些設定。 您可以使用 test-AzureStack Cmdlet 來驗證您的安裝是否成功，還有安裝 Azure Stack PowerShell 和 GitHub 工具。 

使用 Azure AD 部署之後，您必須啟用 Azure Stack 系統管理員和租用戶入口網站。 此啟用同意將所有目錄使用者的正確權限 (列在同意頁面上) 提供給 Azure Stack 入口網站和 Azure Resource Manager。

您也應該重設密碼到期原則，以確保開發套件主機的密碼不會在評估期間結束前到期。

> [!NOTE]
> 或者，您也可以在「安裝 ASDK 後」，再設定 [Azure Stack 遙測設定](asdk-telemetry.md#enable-or-disable-telemetry-after-deployment)。

**[部署 ASDK 後的工作](asdk-post-deploy.md)**

## <a name="register-with-azure"></a>向 Azure 註冊
您必須向 Azure 註冊 Azure Stack，如此您才能夠[將 Azure 市集項目下載到 Azure Stack](asdk-marketplace-item.md)。

**[向 Azure 註冊 Azure Stack](asdk-register.md)**

## <a name="next-steps"></a>後續步驟
恭喜！ 完成這些步驟之後，您將會擁有一個具備[系統管理員](https://adminportal.local.azurestack.external)和[使用者](https://portal.local.azurestack.external)入口網站的開發套件環境。 

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 下載部署套件並解壓縮
> * 準備開發套件主機電腦 
> * 在主機電腦上安裝 ASDK
> * 執行部署後設定
> * 向 Azure 註冊

前進到下一個教學課程，以了解如何新增 Azure Stack 市集項目。

> [!div class="nextstepaction"]
> [新增 Azure Stack 市集項目](asdk-marketplace-item.md)




