---
title: 管理 Azure Stack 中的 API 版本設定檔 | Microsoft Docs
description: 了解 Azure Stack 中的 API 版本設定檔。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 1ea65c9c1f69c8eec77eb498a5963b0d77ce57f1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
ms.locfileid: "32155202"
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>管理 Azure Stack 中的 API 版本設定檔

*適用於：Azure Stack 整合系統和 Azure Stack 開發套件*

API 設定檔會指定 Azure 資源提供者和 Azure REST 端點的 API 版本。 您可以使用 API 設定檔建立不同語言的自訂用戶端。 每個用戶端都會使用 API 設定檔來連絡 Azure Stack 的正確資源提供者和 API 版本。 

您可以建立應用程式來與 Azure 資源提供者搭配運作，而不需要清楚區分每個資源提供者 API 可與 Azure Stack 相容的是哪個版本。 只要將應用程式對到設定檔，SDK 就會還原為正確的 API 版本。


本主題可協助您：
 - 了解 Azure Stack 的 API 設定檔。
 - 如何使用 API 設定檔來開發解決方案。
 - 要到哪裡尋找程式碼專屬的指引。

## <a name="summary-of-api-profiles"></a>API 設定檔的摘要

- API 設定檔可用來代表一組 Azure 資源提供者和其 API 版本。
- API 設定檔是為了讓開發人員建立跨多個 Azure 雲端之範本而建立。 其設計目的，是為了滿足您需要相容且穩定介面的需求。
- 設定檔每年會發行四次。
- 三個設定檔命名慣例如下：
    - **最新**  
        Azure 中所發行的最新 API 版本。
    - **yyyy-mm-dd-hybrid**  
    每兩年發行一次，此發行著重於跨多個雲端的一致性和穩定性。 此設定檔的目標是最佳 Azure Stack 相容性。 
    - **yyyy-mm-dd-profile**  
    落於最佳穩定性和最新功能之間。

### <a name="api-profiles-and-azure-stack-compatibility"></a>API 設定檔和 Azure Stack 相容性

最新的 API 設定檔與 Azure Stack 不相容。 命名慣例可協助您識別要在 Azure Stack 解決方案中使用的設定檔。

**最新**  
此設定檔是全域 Azure 中的最新 API 版本，它不適用於 Azure Stack。 此設定檔具有最大數目的重大變更。 設定檔不考慮其他雲端的穩定性與相容性。 如果您正在嘗試使用最新 API 版本，便應該使用此設定檔。

**yyyy-mm-dd-hybrid**  
此設定檔會在每年 3 月和 9 月發行。 此設定檔具有各種雲端的最佳穩定性與相容性。 此設定檔針對全域 Azure 和 Azure Stack 為目標設計。 此設定檔中列出的 Azure API 版本將與 Azure Stack 中列出的版本相同。 您可以使用此設定檔開發混合式雲端方案的程式碼。

**yyyy-mm-dd-profile**  
此設定檔將在 6 月和 12 月發行給全域 Azure。 此設定檔對 Azure Stack 沒有作用；將有許多的重大變更。 雖然它具有最佳的穩定性和最新功能，但「最新」與此設定檔之間的差異在於，「最新」將始終由最新的 API 版本組成，而不論 API 何時發行。 如果明天為 Compute API 建立了新的 API 版本，則該 API 版本將在「最新」設定檔中列出，但不會在 yyyy-mm-dd-profile 設定檔中列出，因為此設定檔已事先建立。 它涵蓋了 6 月或 12 月之前發行的最新版本。

## <a name="azure-resource-manager-api-profiles"></a>Azure Resource Manager API 設定檔

Azure Stack 不會使用全域 Azure 中所找到的最新版 API。 在建立您自己的解決方案時，您需要在 Azure 中尋找每個資源提供者可與 Azure Stack 相容的 API 版本。

您可以使用 API 設定檔，而不必研究每個資源提供者和 Azure Stack 所支援的特定版本。 設定檔會指定一組資源提供者和 API 版本。 SDK 或是使用 SDK 所建置的工具，將會還原為設定檔中所指定的目標 api-version。 透過 API 設定檔，您可以指定套用至整個範本的設定檔版本，而在執行階段，Azure Resource Manager 會選取正確的資源版本。

API 設定檔可與使用 Azure Resource Manager 的工具搭配運作，例如 PowerShell、Azure CLI、SDK 所提供的程式碼，以及 Microsoft Visual Studio。 工具和 SDK 可使用設定檔來讀取建置應用程式時所納入的模組和程式庫版本。

例如，如果使用 PowerShell 以支援 api-version 2016-03-30 的 **Microsoft.Storage** 資源提供者建立儲存體帳戶，並使用 api-version 為 2015-12-01 的 Microsoft.Compute 資源提供者建立 VM，您就必須查看哪個 PowerShell 模組支援使用 2016-03-30 來進行儲存，以及哪個模組支援使用 2015-02-01 來進行計算，然後安裝這兩個模組。 但您也可以改為使用設定檔。 使用 Cmdlet **Install-Profile *profilename***，然後 PowerShell 就會載入正確的模組版本。

同樣地，在使用 Python SDK 來建置以 Python 為基礎的應用程式時，您也可以指定設定檔。 SDK 會為指令碼所指定的資源提供者載入正確模組。

身為開發人員，您可以專注於撰寫解決方案。 您可以使用設定檔，並清楚程式碼可跨所有支援該設定檔的雲端來運作，而不必研究哪個 api-version、資源提供者和哪個雲端可一起運作。

## <a name="api-profile-code-samples"></a>API 設定檔程式碼範例

您可以找到程式碼範例，來協助您使用設定檔整合解決方案與您在 Azure Stack 慣用的語言。 目前，您可以找到下列語言的指引和範例：

- **PowerShell**  
您可以使用可透過「PowerShell 資源庫」取得的 **AzureRM.Bootstrapper** 模組，來取得使用 API 版本設定檔所需的 PowerShell Cmdlet。 如需資訊，請參閱[使用適用於 PowerShell 的 API 版本設定檔](azure-stack-version-profiles-powershell.md)。
- **Azure CLI 2.0**  
您可以將環境組態更新為使用 Azure Stack 專屬的 API 版本設定檔。 如需資訊，請參閱[使用適用於 Azure CLI 2.0 的 API 版本設定檔](azure-stack-version-profiles-azurecli2.md)。
- **GO**  
在 GO SDK 中，設定檔結合了不同服務不同版本的不同資源類型。 設定檔可於 profiles/ 路徑下取得，而其版本採用 **YYYY-MM-DD** 格式。 如需資訊，請參閱[使用適用於 GO 的 API 版本設定檔](azure-stack-version-profiles-go.md)。
- **Ruby**  
Azure Stack 資源管理員的 Ruby SDK 會提供工具，可協助您建置及管理基礎結構。 SDK 中的資源提供者包括運算、虛擬網路和使用 Ruby 語言的儲存體。 如需資訊，請參閱[使用 API 版本設定檔與 Ruby](azure-stack-version-profiles-ruby.md)

## <a name="next-steps"></a>後續步驟
* [安裝 Azure Stack 適用的 PowerShell](azure-stack-powershell-install.md)
* [設定 Azure Stack 使用者的 PowerShell 環境](azure-stack-powershell-configure-user.md)
* [檢閱設定檔所支援之資源提供者 API 版本的詳細資料](azure-stack-profiles-azure-resource-manager-versions.md)。
