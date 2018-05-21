---
title: 使用 Azure 和 Azure Stack 應用程式設定混合式雲端身分識別 | Microsoft Docs
description: 了解如何使用 Azure 和 Azure Stack 應用程式設定混合式雲端身分識別。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 5224fe4682509f92e1f18d865e5cc5afb1218ad1
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-configure-hybrid-cloud-identity-with-azure-and-azure-stack-applications"></a>教學課程：使用 Azure 和 Azure Stack 應用程式設定混合式雲端身分識別

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*

您必須有兩個選項可授與在全域 Azure 和 Azure Stack 中對應用程式的存取權。 當 Azure Stack 可持續連線至網際網路時，您可以使用 Azure Active Directory (Azure AD)。 當 Azure Stack 的網際網路連線中斷時，您可以使用 Azure Directory 同盟服務 (AD FS)。 您可以使用服務主體授與在 Azure Stack 中對您應用程式的存取權，以便在 Azure Stack 中透過 Azure Resource Manger 進行部署或設定。 

在本教學課程中，您將建置範例環境，用以：

> [!div class="checklist"]
> * 在全域 Azure 與 Azure Stack 中建立混合式身分識別
> * 擷取用以存取 Azure Stack API 的權杖。

要執行這些步驟，您必須具有 Azure Stack 操作員權限。

## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>在入口網站中為 Azure AD 建立服務主體

如果您使用 Azure AD 做為身分識別存放區部署了 Azure Stack，則可以如同對 Azure 般建立服務主體。 [本文件](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-azure-ad)說明如何透過入口網站執行這些步驟。 開始之前，請確認您有[必要的 Azure AD 權限](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions)。

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>使用 PowerShell 為 AD FS 建立服務主體

如果您是使用 Azure Stack 部署 AD FS，則可以使用 PowerShell 來建立服務主體、指派用於存取的角色，以及從 PowerShell 使用該身分識別登入。 [本文件](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-ad-fs)說明如何使用 PowerShell 來執行這些步驟。

## <a name="using-the-azure-stack-api"></a>使用 Azure Stack API

[本教學課程](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-rest-api-use)將逐步解說擷取權杖以存取 Azure Stack API 的流程。

## <a name="connect-to-azure-stack-using-powershell"></a>使用 Powershell 連線至 Azure Stack

以下是使用本文件所提供的概念連線至 Azure Stack 的範例指令碼。

### <a name="prerequisites"></a>先決條件

使用您可以存取的訂用帳戶連線至 Azure Active Directory 的 Azure Stack 安裝。 如果您沒有 Azure Stack 安裝，您可以依照這些指示設定 [Azure Stack 開發套件](https://docs.microsoft.com/en-us/azure/azure-stack/asdk/asdk-deploy)。

[本教學課程](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-powershell-configure-quickstart)將引導您完成安裝 Azure PowerShell 並連線至 Azure Stack 安裝所需的步驟。

#### <a name="connect-to-azure-stack-using-code"></a>使用程式碼連線至 Azure Stack

若要使用程式碼連線至 Azure Stack，請使用的 Azure Resource Manager 端點 API 取得 Azure Stack 安裝的驗證和圖形端點，然後使用 REST 要求進行驗證。 您可以在[這裡](https://github.com/shriramnat/HybridARMApplication)找到範例應用程式。

> [!Note]  
除非您所選語言的 Azure SDK 支援 Azure API 設定檔，否則 SDK 可能無法與 Azure Stack 搭配使用。 若要深入了解 Azure API 設定檔，請移至[這裡](https://docs.microsoft.com/da-dk/azure/azure-stack/user/azure-stack-version-profiles)。

## <a name="next-steps"></a>後續步驟

 - 若要深入了解在 Azure Stack 處理身分識別的方式，請參閱 [Azure Stack 的身分識別架構](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-architecture)。  
 - 若要深入了解 Azure 雲端模式，請參閱[雲端設計模式](https://docs.microsoft.com/azure/architecture/patterns)。
