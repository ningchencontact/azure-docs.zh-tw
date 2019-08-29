---
layout: LandingPage
description: 了解如何針對虛擬機器部署進行疑難排解。
title: Azure 虛擬機器疑難排解文件 | Microsoft Docs
services: virtual-machines
author: genlin
manager: gwallace
ms.assetid: ''
ms.service: virtual-machines
ms.tgt_pltfrm: na
ms.topic: landing-page
ms.date: 10/3/2018
ms.author: genli
ms.openlocfilehash: ed1873fa8e74b2af5734411dc80aeb9089b61a7e
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70080444"
---
# <a name="troubleshooting-azure-virtual-machines"></a>針對 Azure 虛擬機器進行疑難排解

## <a name="tools-for-troubleshooting"></a>疑難排解工具

- [序列主控台](serial-console-windows.md)
- [開機診斷](boot-diagnostics.md)
- [Windows VM：將作業系統磁碟連結到另一個 VM 以進行疑難排解](troubleshoot-recovery-disks-portal-windows.md)
- [Linux VM：將作業系統磁碟連結到另一個 VM 以進行疑難排解](troubleshoot-recovery-disks-portal-linux.md)

## <a name="cant-connect-to-the-vm"></a>無法連線我的 VM

### <a name="windows"></a>Windows

**常見解決方案**

- [重設 RDP](reset-rdp.md)
- [RDP 疑難排解](troubleshoot-rdp-connection.md)
- [詳細 RDP 疑難排解](detailed-troubleshoot-rdp.md)

**RDP 錯誤**

- [無授權伺服器](troubleshoot-rdp-no-license-server.md)
- [外部](Troubleshoot-rdp-internal-error.md)
- [驗證錯誤](troubleshoot-authentication-error-rdp-vm.md)
- [針對特定錯誤進行疑難排解](troubleshoot-specific-rdp-errors.md)

**VM 開機錯誤**

* [BitLocker 開機錯誤](troubleshoot-bitlocker-boot-error.md) 
* [在開機期間，Windows 顯示「正在檢查檔案系統」](troubleshoot-check-disk-boot-error.md)
* [藍色畫面錯誤](troubleshoot-common-blue-screen-error.md)
* [VM 啟動停滯在「正在準備 Windows，](troubleshoot-vm-boot-configure-update.md)
* [藍色畫面上顯示「重要服務已失敗」錯誤](troubleshoot-critical-service-failed-boot-error.md)
* [重新開機迴圈問題](troubleshoot-reboot-loop.md)
* [VM 啟動程序停滯於 Windows Update 階段](troubleshoot-stuck-updating-boot-error.md)
* [VM 以安全模式開機](troubleshoot-rdp-safe-mode.md)

**其他**
- [離線重設 Windows VM 密碼](reset-local-password-without-agent.md)
- [設定錯誤後重設 NIC](reset-network-interface.md)

### <a name="linux"></a>Linux

- [SSH 疑難排解](troubleshoot-ssh-connection.md)
- [詳細 SSH 疑難排解](detailed-troubleshoot-ssh-connection.md)
- [常見的錯誤訊息](error-messages.md)
- [離線重設 Linux VM 密碼](reset-password.md)

## <a name="vm-deployment-issues"></a>VM 部署問題

- [配置失敗](allocation-failure.md)
- 重新部署 VM
    - [Linux](redeploy-to-new-node-linux.md)
    - [Windows](redeploy-to-new-node-windows.md)
- 針對部署進行疑難排解
    - [Linux](troubleshoot-deploy-vm-linux.md)
    - [Windows](troubleshoot-deploy-vm-windows.md)
- [裝置名稱已變更](troubleshoot-device-names-problems.md)
- [離線安裝 Windows VM 代理程式](install-vm-agent-offline.md)
- [重新啟動或調整 VM 大小](restart-resize-error-troubleshooting.md)

## <a name="vm-performance-issue"></a>VM 效能問題
- [虛擬機器效能問題](performance-diagnostics.md)
- Windows
    - [如何使用 PerfInsights](how-to-use-perfinsights.md)
    - [效能診斷擴充功能](performance-diagnostics-vm-extension.md)
- Linux
    - [如何使用 PerfInsights](how-to-use-perfinsights-linux.md)