---
title: Azure 資訊安全中心中的 Endpoint protection 解決方案探索和健康狀態評估 |Microsoft Docs
description: 如何探索端點保護解決方案，並將其識別為狀況良好。
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
ms.date: 08/08/2019
ms.author: v-mohabe
ms.openlocfilehash: a5cd0f88173abb65a120aa305206505af51d9f9e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861366"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Azure 資訊安全中心中的 Endpoint protection 評估和建議

Azure 資訊安全中心中的 Endpoint protection 評估和建議會偵測並提供 Endpoint protection 解決方案[支援](https://docs.microsoft.com/azure/security-center/security-center-os-coverage)版本的健康情況評估。 本主題說明為 Azure 資訊安全中心的端點保護解決方案產生下列兩項建議的案例。

* **在您的虛擬機器上安裝端點保護解決方案**
* **解決電腦上的 endpoint protection 健康情況問題**

## <a name="windows-defender"></a>Windows Defender

* [MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps)執行時，會產生「 **在虛擬機器上安裝端點保護解決方案**」建議，且結果為 AMServiceEnabled **：**False**

* [MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps)執行時，會產生「**解決電腦上的端點保護健康情況問題**」的建議，並會發生下列其中一種或兩種情況：

  * 下列其中一個屬性至少為 false：

     **AMServiceEnabled**

     **AntispywareEnabled**

     **RealTimeProtectionEnabled**

     **BehaviorMonitorEnabled**

     **IoavProtectionEnabled**

     **OnAccessProtectionEnabled**

  * 如果下列其中一個或兩個屬性大於或等於7，則為。

     **AntispywareSignatureAge**

     **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Microsoft System Center endpoint protection

* 匯入**SCEPMpModule （"$env:P Rogramfiles\microsoft Security Client\MpProvider\MpProvider.psd1"）** 並 **執行時，會產生「在虛擬機器上安裝端點保護解決方案」的建議MProtComputerStatus**結果（ **AMServiceEnabled = false** ）

* **MprotComputerStatus**執行時，會產生「**解決電腦上的端點保護健康情況問題**」的建議，並會發生下列其中一種或兩種情況：

    * 下列其中一個屬性至少為 false：

       **AMServiceEnabled**
    
       **AntispywareEnabled**
    
       **RealTimeProtectionEnabled**
    
       **BehaviorMonitorEnabled**
    
       **IoavProtectionEnabled**
    
       **OnAccessProtectionEnabled**
          
    * 如果下列其中一個或兩個簽章更新大於或等於7，則為。 

       **AntispywareSignatureAge**
    
       **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* 如果不符合下列一或多項檢查，則會產生「**在虛擬機器上安裝端點保護解決方案**」的建議：
    * **HKLM： \ SOFTWARE\TrendMicro\Deep 安全性代理程式**已存在
    * **HKLM： \ SOFTWARE\TrendMicro\Deep 安全性 Agent\InstallationFolder**存在
    * 在安裝資料夾中可找到**dsq_query .cmd**檔案
    * 以 **Component. mode 執行 dsa_query 結果：偵測到趨勢微深度安全性代理程式**

## <a name="symantec-endpoint-protection"></a>Symantec endpoint protection
如果不符合下列任何一項檢查，則會產生「**在虛擬機器上安裝端點保護解決方案**」的建議：

* **HKLM： \ Software\Symantec\Symantec 端點 Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

或

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

如果不符合下列任何一項檢查，就會產生「**解決電腦上的端點保護健康情況問題**」的建議：  

* 檢查 Symantec 版本 > = 12：登錄位置：**HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion" -Value "PRODUCTVERSION"**

* 檢查即時保護狀態：**HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff == 1**

* 檢查簽章更新狀態：**HKLM\Software\Symantec\Symantec 端點 Protection\CurrentVersion\public-opstate\LatestVirusDefsDate < = 7 天**

* 檢查完整掃描狀態：**HKLM： \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime < = 7 天**

* 尋找 Symantec 12 簽名版本的簽章版本號碼路徑：**Registry Paths+ "CurrentVersion\SharedDefs" -Value "SRTSP"** 

* Symantec 14 的簽章版本路徑：**Registry Paths+ "CurrentVersion\SharedDefs\SDSDefs" -Value "SRTSP"**

登錄路徑：

* **"HKLM： \ Software\Symantec\Symantec Endpoint Protection" + $Path;**
* **"HKLM： \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>適用于 Windows 的 McAfee endpoint protection

如果不符合下列檢查，則會產生「**在虛擬機器上安裝端點保護解決方案**」的建議：

* **HKLM： \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion**已存在

* **HKLM： \ SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

如果不符合下列檢查，則會產生「**解決電腦上的端點保護健康情況問題**」的建議：

* McAfee 版本：**HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**

* 尋找簽章版本：**HKLM:\Software\McAfee\AVSolution\DS\DS -Value "dwContentMajorVersion"**

* 尋找簽章日期：**HKLM:\Software\McAfee\AVSolution\DS\DS -Value "szContentCreationDate" >= 7 days**

* 尋找掃描日期：**HKLM:\Software\McAfee\Endpoint\AV\ODS -Value "LastFullScanOdsRunTime" >= 7 days**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>適用于 Linux 的 McAfee Endpoint Security 威脅防護 

如果不符合下列其中一項或兩項檢查，則會產生「**在虛擬機器上安裝端點保護解決方案**」建議：  

- 檔案 **/opt/isec/ens/threatprevention/bin/isecav**結束 

- **"/opt/isec/ens/threatprevention/bin/isecav--version"** 輸出為：**McAfee name = 適用于 Linux 威脅防護的 McAfee Endpoint Security 和 McAfee 版本 > = 10**

如果不符合下列一或多項檢查，則會產生「**解決電腦上的端點保護健康情況問題**」建議：

- **"/opt/isec/ens/threatprevention/bin/isecav--listtask"** 會傳回**快速掃描、完整掃描**和兩個掃描 < = 7 天

- **"/opt/isec/ens/threatprevention/bin/isecav--listtask"** 會傳回**DAT 和引擎更新時間**，而且兩者 < = 7 天

- 「 **/opt/isec/ens/threatprevention/bin/isecav--getoasconfig--summary** 」會**在存取掃描狀態時**傳回

## <a name="sophos-antivirus-for-linux"></a>Sophos 適用于 Linux 的防毒軟體 

如果不符合下列其中一項或兩項檢查，則會產生「**在虛擬機器上安裝端點保護解決方案**」建議：

- 檔案 **/opt/sophos-av/bin/savdstatus**會結束或搜尋自訂位置 **"readlink $ （其 savscan）"**

- **"/opt/sophos-av/bin/savdstatus--version"** 會傳回 sophos Name = **Sophos 防毒軟體和 sophos 版本 > = 9**

如果不符合下列一或多項檢查，則會產生「**解決電腦上的端點保護健康情況問題**」建議：

- **"/opt/sophos-av/bin/savlog--maxage = 7 |grep-i "已排程掃描。已\*完成 "| tail-1"** ，傳回值   

- **"/opt/sophos-av/bin/savlog--maxage = 7 |grep 「掃描已完成**」 |tail-1 "，傳回值   

- **"/opt/sophos-av/bin/savdstatus--lastupdate"** 會傳回應 < = 7 天的 lastupdate 

- 「 **/opt/sophos-av/bin/savdstatus-v** 」等於「**正在執行存取掃描**」 

- **"/opt/sophos-av/bin/savconfig Get LiveProtection"** 傳回已啟用  

## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

您可以在下列位置取得 Microsoft Antimalware 擴充功能記錄檔：  
**%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware(Or PaaSAntimalware)\1.5.5.x(version#)\CommandExecution.log**

### <a name="support"></a>支援

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。
