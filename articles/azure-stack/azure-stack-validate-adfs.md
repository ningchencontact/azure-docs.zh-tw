---
title: 驗證 Azure Stack 的 AD FS 整合
description: 使用「Azure Stack 整備檢查程式」來驗證 Azure Stack 的 AD FS 整合。
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/28/2019
ms.author: patricka
ms.reviewer: jerskine
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 2200b9a48d7f83d6785c8dbb4a7b02be52fca75a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55241061"
---
# <a name="validate-ad-fs-integration-for-azure-stack"></a>驗證 Azure Stack 的 AD FS 整合

使用「Azure Stack 整備檢查程式」工具 (AzsReadinessChecker) 來驗證您的環境是否已準備好將 Azure Stack 與 Active Directory 同盟服務 (AD FS) 進行整合。 在開始資料中心整合或開始 Azure Stack 部署之前，請先驗證 AD FS 整合。

整備檢查程式會驗證：

* 「同盟中繼資料」包含有效的同盟 XML 元素。
* 可擷取「AD FS SSL 憑證」，並且能夠建置信任鏈結。 戳記上 AD FS 必須信任 SSL 憑證鏈結。 憑證必須由用於 Azure Stack 部署憑證的相同「憑證授權單位」簽署，或由受信任的根授權單位合作夥伴簽署。 如需完整的受信任根授權單位合作夥伴清單，請參閱 [TechNet](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca) \(英文\)。
* 「AD FS 簽署憑證」受信任且沒有快要到期。

如需有關 Azure Stack 資料中心整合的詳細資訊，請參閱 [Azure Stack 資料中心整合 - 身分識別](azure-stack-integrate-identity.md)。

## <a name="get-the-readiness-checker-tool"></a>取得整備檢查程式工具

從 [PowerShell 資源庫](https://aka.ms/AzsReadinessChecker) \(英文\) 下載最新版的「Azure Stack 整備檢查程式」工具 (AzsReadinessChecker)。  

## <a name="prerequisites"></a>必要條件

必須具備下列先決條件。

**執行這個工具的電腦：**

* 具有網域連線能力的 Windows 10 或 Windows Server 2016。
* PowerShell 5.1 或更新版本。 若要檢查版本，請執行下列 PowerShell 命令，然後再檢閱「主要」版本和「次要」版本：  
   > `$PSVersionTable.PSVersion`
* 最新版的 [Microsoft Azure Stack 整備檢查程式](https://aka.ms/AzsReadinessChecker) \(英文\) 工具。

**Active Directory 同盟服務環境：**

您至少需要下列其中一種形式的中繼資料：

* AD FS 同盟中繼資料的 URL。 例如 `https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`。
* 同盟中繼資料 XML 檔案。 例如 FederationMetadata.xml。

## <a name="validate-ad-fs-integration"></a>驗證 AD FS 整合

1. 在符合先決條件的電腦上，開啟系統管理 PowerShell 提示字元，然後執行下列命令以安裝 AzsReadinessChecker：

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. 從 PowerShell 提示字元中，執行下列命令以開始驗證。 指定 **-CustomADFSFederationMetadataEndpointUri** 的值作為同盟中繼資料的 URI。

     `Invoke-AzsADFSValidation -CustomADFSFederationMetadataEndpointUri https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`

1. 在此工具執行之後，請檢閱輸出。 確認 AD FS 整合需求項目的狀態為 OK (正常)。 驗證成功時會出現類似以下範例的輸出：

    ```
    Invoke-AzsADFSValidation v1.1809.1001.1 started.

    Testing ADFS Endpoint https://sts.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

            Read Metadata:                         OK
            Test Metadata Elements:                OK
            Test SSL ADFS Certificate:             OK
            Test Certificate Chain:                OK
            Test Certificate Expiry:               OK

    Details:
    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Data Center Integration.
    Additional help URL: https://aka.ms/AzsADFSIntegration

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsADFSValidation Completed
    ```

在生產環境中，如果是從操作員工作站測試憑證信任鏈結，並無法完全表示 Azure Stack 基礎結構中的 PKI 信任狀態。 Azure Stack 戳記的公用 VIP 網路需要針對 PKI 基礎結構的 CRL 連線。

## <a name="report-and-log-file"></a>報告與記錄檔

每當驗證執行時，它會將結果記錄到 **AzsReadinessChecker.log** 和 **AzsReadinessCheckerReport.json**。 這些檔案的位置會在 PowerShell 中與驗證結果一同顯示。

驗證檔案可協助您在部署 Azure Stack 或調查驗證問題之前共用狀態。 這兩個檔案會保存每個後續驗證檢查的結果。 此報告會向部署團隊提供身分識別設定的確認。 記錄檔可協助部署或支援小組調查驗證問題。

這兩個檔案預設都會寫入至 `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`。

使用︰

* **-OutputPath**：執行命令列結尾的 *path* 參數能指定不同的報告位置。
* **-CleanReport**：執行命令結尾的參數，用來清除先前報告資訊的 AzsReadinessCheckerReport.json。 如需詳細資訊，請參閱 [Azure Stack 驗證報告](azure-stack-validation-report.md)。

## <a name="validation-failures"></a>驗證失敗

如果驗證檢查失敗，則會在 PowerShell 視窗中顯示有關失敗的詳細資料。 此工具也會將資訊記錄至 *AzsReadinessChecker.log*。

下列範例會提供關於一般驗證失敗的指引。

### <a name="command-not-found"></a>找不到命令

`Invoke-AzsADFSValidation : The term 'Invoke-AzsADFSValidation' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.`

**原因**︰PowerShell Autoload 無法正確載入「整備檢查程式」模組。

**解決方案**︰明確匯入「整備檢查程式」模組。 請複製下列程式碼並貼到 PowerShell 中，並以目前所安裝版本的號碼更新 \<version\>。

`Import-Module "c:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\<version>\Microsoft.AzureStack.ReadinessChecker.psd1" -Force`

## <a name="next-steps"></a>後續步驟

[檢視整備報告](azure-stack-validation-report.md)  
[一般 Azure Stack 整合考量](azure-stack-datacenter-integration.md)  
