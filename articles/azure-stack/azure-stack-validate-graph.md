---
title: 驗證 Azure Stack 的 Azure 圖表整合
description: 使用「Azure Stack 整備檢查程式」來驗證 Azure Stack 的圖表整合。
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
ms.date: 10/22/2018
ms.author: patricka
ms.reviewer: jerskine
ms.openlocfilehash: e1c1ba0a065a20874bf51d7464cbcfdfa13a571d
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2018
ms.locfileid: "49946871"
---
# <a name="validate-graph-integration-for-azure-stack"></a>驗證 Azure Stack 的圖表整合

使用「Azure Stack 整備檢查程式」工具 (AzsReadinessChecker) 來驗證您的環境是否已準備好與 Azure Stack 進行圖表整合。 您應該在開始資料中心整合之前，或在開始 Azure Stack 部署之前，先驗證圖表整合。

整備檢查程式會驗證：

* 為圖表整合建立之服務帳戶的認證具備可查詢 Active Directory 的適當權限。
* 「通用類別目錄」可供解析及連線。
* KDC 可供解析及連線。
* 已備妥必要的網路連線能力。

如需有關 Azure Stack 資料中心整合的詳細資訊，請參閱 [Azure Stack 資料中心整合 - 身分識別](azure-stack-integrate-identity.md)

## <a name="get-the-readiness-checker-tool"></a>取得整備檢查程式工具

從 [PSGallery](https://aka.ms/AzsReadinessChecker) 下載最新版的 Azure Stack 整備檢查程式工具 (AzsReadinessChecker)。

## <a name="prerequisites"></a>必要條件

必須具備下列先決條件。

**執行這個工具的電腦：**

* 具有網域連線能力的 Windows 10 或 Windows Server 2016。
* PowerShell 5.1 或更新版本。 若要檢查版本，請執行下列 PowerShell 命令，然後再檢閱「主要」版本和「次要」版本：  
   > `$PSVersionTable.PSVersion`
* Active Directory PowerShell 模組
* 最新版的 [Microsoft Azure Stack 整備檢查程式](https://aka.ms/AzsReadinessChecker)工具

**Active Directory 環境：**

* 識別現有 Active Directory 中圖表服務帳戶的使用者名稱和密碼
* 識別 Active Directory 樹系根 FQDN

## <a name="validate-graph"></a>驗證圖表

1. 在符合先決條件的電腦上，開啟系統管理 PowerShell 提示字元，然後執行下列命令以安裝 AzsReadinessChecker。

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. 從 PowerShell 提示字元中，執行下列命令以將 *$graphCredential* 變數設定為圖表帳戶。 使用 `domain\username` 格式以您的帳戶取代 `contoso\graphservice`。

    `$graphCredential = Get-Credential contoso\graphservice -Message "Enter Credentials for the Graph Service Account"`

1. 從 PowerShell 提示字元中，執行下列命令以開始圖表的驗證。 指定 **-ForestFQDN** 的值作為樹系根的 FQDN：

     `Invoke-AzsGraphValidation -ForestFQDN contoso.com -Credential $graphCredential`

1. 在此工具執行之後，請檢閱輸出。 確認圖表整合需求項目的狀態為 OK (正常)。 驗證成功時會出現類似以下的輸出：

    ```
    Testing Graph Integration (v1.0)
            Test Forest Root:            OK
            Test Graph Credential:       OK
            Test Global Catalog:         OK
            Test KDC:                    OK
            Test LDAP Search:            OK
            Test Network Connectivity:   OK

    Details:

    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Data Center Integration.

    Additional help URL: https://aka.ms/AzsGraphIntegration

    AzsReadinessChecker Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log

    AzsReadinessChecker Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsGraphValidation Completed
    ```

在生產環境中，如果是從操作員工作站測試網路連線，並無法視為可完整表示 Azure Stack 可用的連線能力。 Azure Stack 戳記的公用 VIP 網路將需要 LDAP 流量的連線能力，才能執行身分識別整合。

## <a name="report-and-log-file"></a>報告與記錄檔

每當驗證執行時，它會將結果記錄到 **AzsReadinessChecker.log** 和 **AzsReadinessCheckerReport.json**。 PowerShell 中的驗證結果會一同顯示這些檔案的位置。

驗證檔案可協助您在部署 Azure Stack 或調查驗證問題之前共用狀態。 這兩個檔案會保存每個後續驗證檢查的結果。 此報告會向部署團隊提供身分識別設定的確認。 記錄檔可協助部署或支援小組調查驗證問題。

這兩個檔案預設都會寫入至 `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`

使用︰

* 執行命令列結尾的 **-OutputPath** *路徑*參數來指定不同的報告位置。
* 執行命令結尾的 **-CleanReport** 參數來清除先前報告資訊的 *AzsReadinessCheckerReport.json*。 如需詳細資訊，請參閱 [Azure Stack 驗證報告](azure-stack-validation-report.md)。

## <a name="validation-failures"></a>驗證失敗

如果驗證檢查失敗，則會在 PowerShell 視窗中顯示有關失敗的詳細資料。 此工具也會將資訊記錄至 *AzsGraphIntegration.log*。

## <a name="next-steps"></a>後續步驟

[檢視整備報告](azure-stack-validation-report.md)  
[一般 Azure Stack 整合考量](azure-stack-datacenter-integration.md)  
