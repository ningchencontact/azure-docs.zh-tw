---
title: 端點保護解決方案探索和健康狀態評估 Azure 資訊安全中心 |Microsoft Docs
description: 如何會探索端點保護解決方案，並識別為狀況良好。
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2019
ms.author: v-mohabe
ms.openlocfilehash: b17e5f16b988bfa562b00bc6f5b9dfd34be4ca43
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247960"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Endpoint protection 評估和 Azure 資訊安全中心的建議

Endpoint protection 評估和 Azure 資訊安全中心的建議會偵測並提供健康情況評估[支援](https://docs.microsoft.com/azure/security-center/security-center-os-coverage#supported-platforms-for-windows-computers-and-vms)的端點保護解決方案的版本。 本主題說明產生下列兩個端點保護解決方案，Azure 資訊安全中心建議的案例。

* **在您的虛擬機器上安裝端點保護解決方案**
* **解決您的機器上的 endpoint protection 健康情況問題**

## <a name="windows-defender"></a>Windows Defender

* **「 虛擬機器上安裝端點保護解決方案 」** 產生建議時[Get MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps)執行和結果是**AMServiceEnabled:False**

* **「 解析您的機器上的 endpoint protection 健全狀況問題 」** 產生建議時[Get MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps)執行和一個或兩個以下，就會發生：

  * 至少其中一個下列的屬性為 false:

     **AMServiceEnabled**

     **AntispywareEnabled**

     **RealTimeProtectionEnabled**

     **BehaviorMonitorEnabled**

     **IoavProtectionEnabled**

     **OnAccessProtectionEnabled**

  * 如果一個或多個下列屬性大於或等於 7。

     **AntispywareSignatureAge**

     **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Microsoft System Center endpoint protection

* **「 虛擬機器上安裝端點保護解決方案 」** 匯入時，會產生建議**SCEPMpModule ("$env: ProgramFiles\Microsoft 安全性 Client\MpProvider\MpProvider.psd1")** 上手**Get MProtComputerStatus**的結果， **AMServiceEnabled = false**

* **「 解析您的機器上的 endpoint protection 健全狀況問題 」** 產生建議時**Get MprotComputerStatus**執行和一個或兩個以下，就會發生：

    * 至少其中一個下列的屬性為 false:

       **AMServiceEnabled**
    
       **AntispywareEnabled**
    
       **RealTimeProtectionEnabled**
    
       **BehaviorMonitorEnabled**
    
       **IoavProtectionEnabled**
    
       **OnAccessProtectionEnabled**
          
    * 其中一個或多個下列簽章的更新是否大於或等於 7。 

       **AntispywareSignatureAge**
    
       **AntivirusSignatureAge**

## <a name="trend-micro"></a>趨勢科技

* **「 虛擬機器上安裝端點保護解決方案 」** 會產生建議，如果有一個或多個下列的檢查不符合：
    * **HKLM:\SOFTWARE\TrendMicro\Deep 安全性代理程式**存在
    * **HKLM:\SOFTWARE\TrendMicro\Deep 安全性 Agent\InstallationFolder**存在
    * **Dsq_query.cmd**檔案位於安裝資料夾
    * 執行**dsa_query.cmd**的結果， **Component.AM.mode： 開啟-Trend Micro Deep Security 代理程式偵測到**

## <a name="symantec-endpoint-protection"></a>Symantec endpoint protection
**「 虛擬機器上安裝端點保護解決方案 」** 如果不符合任何下列檢查，就會產生建議：

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

或

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

**「 解析您的機器上的 endpoint protection 健全狀況問題 」** 如果不符合任何下列檢查，就會產生建議：  

* 檢查 Symantec 版本 > = 12:登錄位置：**HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion" -Value "PRODUCTVERSION"**

* 檢查即時保護狀態：**HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff == 1**

* 檢查簽章更新狀態：**HKLM\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate <= 7 days**

* 檢查完整掃描的狀態：**HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 days**

* 尋找 Symantec 12 至特徵碼版本的簽章版本號碼的路徑：**Registry Paths+ "CurrentVersion\SharedDefs" -Value "SRTSP"** 

* Symantec 14 的特徵碼版本的路徑：**Registry Paths+ "CurrentVersion\SharedDefs\SDSDefs" -Value "SRTSP"**

登錄路徑：

**"HKLM:\Software\Symantec\Symantec Endpoint Protection" + $Path;** 
 **"HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>McAfee endpoint protection 的 Windows

**「 虛擬機器上安裝端點保護解決方案 」** 如果達不到下列檢查，就會產生建議：

* **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion** exists

* **HKLM:\SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

**「 解析您的機器上的 endpoint protection 健全狀況問題 」** 如果達不到下列檢查，就會產生建議：

* McAfee 版本：**HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**

* 尋找特徵碼版本：**HKLM:\Software\McAfee\AVSolution\DS\DS -Value "dwContentMajorVersion"**

* 尋找指定簽章日期：**HKLM:\Software\McAfee\AVSolution\DS\DS -Value "szContentCreationDate" >= 7 days**

* 尋找掃描日期：**HKLM:\Software\McAfee\Endpoint\AV\ODS -Value "LastFullScanOdsRunTime" >= 7 days**

## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

Microsoft 反惡意程式碼擴充功能記錄檔位於：  
**%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware(Or PaaSAntimalware)\1.5.5.x(version#)\CommandExecution.log**

### <a name="support"></a>支援

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。
