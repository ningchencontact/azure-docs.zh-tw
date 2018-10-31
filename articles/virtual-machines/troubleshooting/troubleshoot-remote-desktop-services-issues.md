---
title: 遠端桌面服務未在 Azure VM 上啟動 | Microsoft Docs
description: 了解如何針對在連線到虛擬機器時的遠端桌面服務問題進行疑難排解 | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 39b793e2722766f3f28829b4dc48534054abd97e
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988977"
---
# <a name="remote-desktop-services-is-not-starting-on-an-azure-vm"></a>遠端桌面服務未在 Azure VM 上啟動

本文將說明如何針對在遠端桌面服務 (TermService) 未啟動或無法啟動時連線到 Azure 虛擬機器 (VM) 的問題進行疑難排解。

>[!NOTE]
>Azure 建立和處理資源的部署模型有二種： [資源管理員和傳統](../../azure-resource-manager/resource-manager-deployment-model.md)。 本文將說明如何使用 Resource Manager 部署模型。 建議您針對新的部署使用此模型，而不要使用傳統部署模型。

## <a name="symptoms"></a>徵兆

當您嘗試連線到 VM 時，會遇到下列案例：

- VM 螢幕擷取畫面顯示作業系統已完全載入且正在等待認證。
- VM 上的所有應用程式都會如預期般運作且可存取。
- VM 正在回應 Microsoft 遠端桌面通訊協定 (RDP) 連接埠 (預設值 3389) 上的 TCP 連線。
- 當您嘗試建立 RDP 連線時，系統不會提示您提供認證。

## <a name="cause"></a>原因

此問題的發生原因是遠端桌面服務未在虛擬機器上執行。 原因可能取決於下列案例：

- 已將 TermService 服務設定為**停用**。
- TermService 服務當機或停止回應。

## <a name="solution"></a>解決方法

若要解決此問題，請使用下列其中一個解決方案，或逐一嘗試各個解決方案：

### <a name="solution-1-using-the-serial-console"></a>解決方案 1：使用序列主控台

1. 選取 [支援與疑難排解] > [序列主控台 (預覽)] 來存取[序列主控台](serial-console-windows.md)。 如果已在 VM 上啟用此功能，您就能成功連線該 VM。

2. 針對 CMD 執行個體建立新通道。 輸入 **CMD** 以啟動通道來取得通道名稱。

3. 切換至執行 CMD 執行個體的通道，在此案例中，它應該是通道 1。

   ```
   ch -si 1
   ```

4. 再按一次 **Enter** 鍵，然後輸入 VM 的有效使用者名稱和密碼 (本機或網域識別碼)。

5. 查詢 TermService 服務的狀態。

   ```
   sc query TermService
   ```

6. 如果服務狀態顯示**已停止**，請嘗試啟動服務。

   ```
   sc start TermService
   ```

7. 再次查詢服務，以確定服務已成功啟動。

   ```
   sc query TermService
   ```

### <a name="solution-2-using-a-recovery-vm-to-enable-the-service"></a>解決方案 2：使用復原 VM 來啟用服務

[備份 OS 磁碟](../windows/snapshot-copy-managed-disk.md)並[將 OS 磁碟連結至救援 VM](../windows/troubleshoot-recovery-disks-portal.md)。 接著，開啟提升權限的 CMD 執行個體，並在 救援 VM 上執行下列指令碼：

>[!NOTE]
>我們假設指派給已連結 OS 磁碟的磁碟機代號是 F。請將它取代為您 VM 中的適當值。 當此動作完成之後，從復原 VM 中斷磁碟連結，然後[重新建立您的 VM](../windows/create-vm-specialized.md)。 如需進一步疑難排解，您可以使用**解決方案 1**，因為已啟用序列主控台。

```
reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM

REM Enable Serial Console
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} displaybootmenu yes
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} timeout 10
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} bootems yes
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /ems {<Boot Loader Identifier>} ON
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

REM Get the current ControlSet from where the OS is booting
for /f "tokens=3" %x in ('REG QUERY HKLM\BROKENSYSTEM\Select /v Current') do set ControlSet=%x
set ControlSet=%ControlSet:~2,1%

REM Suggested configuration to enable OS Dump
set key=HKLM\BROKENSYSTEM\ControlSet00%ControlSet%\Control\CrashControl
REG ADD %key% /v CrashDumpEnabled /t REG_DWORD /d 2 /f
REG ADD %key% /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
REG ADD %key% /v NMICrashDump /t REG_DWORD /d 1 /f

REM Set default values back on the broken service
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v ImagePath /t REG_EXPAND_SZ /d "<Image Path>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v ObjectName /t REG_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v type /t REG_DWORD /d 16 /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v ImagePath /t REG_EXPAND_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v ObjectName /t REG_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v type /t REG_DWORD /d 16 /f

REM Enable default dependencies from the broken service
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Driver/Service Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Driver/Service Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg unload HKLM\BROKENSYSTEM
```

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員

如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。
