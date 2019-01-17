---
title: 針對 Azure Stack 驗證即服務進行疑難排解 | Microsoft Docs
description: 針對 Azure Stack 的「驗證即服務」進行疑難排解。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ROBOTS: NOINDEX
ms.openlocfilehash: 0fb1afb1a07b31f7e261c958b8a03bec3b299433
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2019
ms.locfileid: "54245288"
---
# <a name="troubleshoot-validation-as-a-service"></a>針對驗證即服務進行疑難排解

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

以下是與軟體版本及其解決方案無關的常見問題。

## <a name="local-agent"></a>本機代理程式

### <a name="the-portal-shows-local-agent-in-debug-mode"></a>入口網站以偵錯模式顯示本機代理程式

這可能是因為代理程式因網路連線不穩定而無法將活動訊號傳送給服務。 傳送活動訊號時會每隔 5 分鐘傳送一次。 如果服務持續 15 分鐘未收到活動訊號，就會將代理程式視為非使用中，而不會再於該代理程式上排定測試。 請查看位於代理程式啟動目錄中 *Agenthost.log* 檔案內的錯誤訊息。

> [!Note]
> 任何已經在代理程式上執行的測試都將繼續執行，但如果活動訊號在測試結束前仍未還原，則代理程式將會無法更新測試狀態或上傳記錄。 測試將一律顯示為「正在執行」，而將必須取消。

### <a name="agent-process-on-machine-was-shut-down-while-executing-test-what-to-expect"></a>執行測試時，機器上的代理程式處理序被關閉。 有何影響？

如果代理程式處理序是以非正常方式關閉的 (例如機器被重新啟動、處理序被終止 (在代理程式視窗上按 CTRL+C 會視為正常關閉))，則在代理程式上執行的測試將會繼續顯示為「正在執行」。 如果重新啟動代理程式，代理程式就會將測試的狀態更新成「已取消」。 如果未重新啟動代理程式，測試就會顯示為「正在執行」，您必須手動取消該測試。

> [!Note]
> 工作流程內的測試會排定成依序執行。 「暫止」測試會等到相同工作流程中處於「正在執行」狀態的測試完成後，才會執行。

## <a name="vm-images"></a>VM 映像

### <a name="handle-slow-network-connectivity"></a>處理緩慢的網路連線

您可以將 PIR 映像下載到您本機資料中心內的共用中。 接著，您便可以檢查該映像。

<!-- This is from the appendix to the Deploy local agent topic. -->

#### <a name="download-pir-image-to-local-share-in-case-of-slow-network-traffic"></a>在網路流量緩慢時將 PIR 映像下載至本機共用

1. 從以下來源下載 AzCopy：[vaasexternaldependencies(AzCopy)](https://vaasexternaldependencies.blob.core.windows.net/prereqcomponents/AzCopy.zip)

2. 將 AzCopy.zip 解壓縮，然後切換至包含 AzCopy.exe 的目錄

3. 從已提升權限的提示字元開啟 Windows PowerShell。 執行下列命令：

```PowerShell  
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterFullBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterCoreBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'WindowsServer2012R2DatacenterBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1404LTS.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1604-20170619.1.vhd' /NC:12 /V:azcopylog.log /Y
```

> [!Note]  
> LocalFileShare 是共用路徑或本機路徑。

#### <a name="verifying-pir-image-file-hash-value"></a>驗證 PIR 映像檔雜湊值

您可以使用 **Get-HashFile** Cmdlet 來取得所下載公用映像存放庫映像檔的雜湊值，以檢查映像檔的完整性。

| 檔案名稱 | SHA256 |
|---------------------------------------|------------------------------------------------------------------|
| Server2016DatacenterFullBYOL.vhd | 6ED58DCA666D530811A1EA563BA509BF9C29182B902D18FCA03C7E0868F733E9 |
| WindowsServer2012R2DatacenterBYOL.vhd | 9792CBF742870B1730B9B16EA814C683A8415EFD7601DDB6D5A76D0964767028 |
| Server2016DatacenterCoreBYOL.vhd | 5E80E1A6721A48A10655E6154C1B90E320DF5558487D6A0D7BFC7DCD32C4D9A5 |
| Ubuntu1404LTS.vhd | B24CDD12352AAEBC612A4558AB9E80F031A2190E46DCB459AF736072742E20E0 |
| Ubuntu1604-20170619.1.vhd | C481B88B60A01CBD5119A3F56632A2203EE5795678D3F3B9B764FFCA885E26CB |

### <a name="failure-occurs-when-uploading-vm-image-in-the-vaasprereq-script"></a>在 `VaaSPreReq` 指令碼中上傳 VM 映像時發生失敗

首先，確認環境狀況良好：

1. 從 DVM / Jump Box，確認您可以使用管理員認證成功登入管理入口網站。
1. 確認沒有任何警示或警告。

如果環境狀況良好，請手動上傳 VaaS 測試回合所需的 5 個 VM 映像：

1. 以服務管理員身分登入管理入口網站。 您可以從 ECE 存放區或戳記資訊檔案找到管理入口網站 URL。 如需相關指示，請參閱[環境參數](azure-stack-vaas-parameters.md#environment-parameters)。
1. 選取 [更多服務] > [資源提供者] > [計算] > [VM 映像]。
1. 選取位於 [VM 映像] 刀鋒視窗頂端的 [+ 新增] 按鈕。
1. 修改或檢查第一個 VM 映像的下列欄位值：
    > [!IMPORTANT]
    > 並非所有預設值對現有的 Marketplace 項目而言都正確。

    | 欄位  | 值  |
    |---------|---------|
    | 發行者 | MicrosoftWindowsServer |
    | 供應項目 | WindowsServer |
    | OS 類型 |  Windows |
    | SKU | 2012-R2-Datacenter |
    | 版本 | 1.0.0 |
    | OS 磁碟 Blob URI | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/WindowsServer2012R2DatacenterBYOL.vhd |

1. 選取 [建立] 按鈕。
1. 針對剩餘的 VM 映像重複上述步驟。

所有 5 個 VM 映像的屬性如下：

| 發行者  | 供應項目  | OS 類型 | SKU | 版本 | OS 磁碟 Blob URI |
|---------|---------|---------|---------|---------|---------|
| MicrosoftWindowsServer| WindowsServer |  Windows | 2012-R2-Datacenter | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/WindowsServer2012R2DatacenterBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer |  Windows | 2016-Datacenter | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Server2016DatacenterFullBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer |  Windows | 2016-Datacenter-Server-Core | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Server2016DatacenterCoreBYOL.vhd |
| Canonical | UbuntuServer | Linux | 14.04.3-LTS | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Ubuntu1404LTS.vhd |
| Canonical | UbuntuServer | Linux | 16.04-LTS | 16.04.20170811 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Ubuntu1604-20170619.1.vhd |

## <a name="next-steps"></a>後續步驟

- 如需了解最新版本的變更，請檢閱[驗證即服務的版本資訊](azure-stack-vaas-release-notes.md)。